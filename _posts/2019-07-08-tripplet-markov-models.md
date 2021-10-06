---
# layout: splash
title: 'Triplet Markov Models! (part-2c)'
date: 2019-07-04
permalink: /posts/poem-generation/tripplet-markov-models/
excerpt: ""
tags:
  - poem-generation
  - nlp
header: 
  teaser: /images/poem-generation/triplet.png
  overlay_color: "#123"
  opacity: 1
  overlay_filter: linear-gradient(rgba(20, 20, 20, 0.5), rgba(50, 0, 0, 0.0))
  show_overlay_excerpt: false
---

---
A poem generated with triplet markov model!
![hmmp1](/images/poem-generation/triplet.png)

---

In the first article, we generated poetry which was descent but had some drawbacks. It lacked a lot of essential poetry features, as it was implemented with a basic N-gram model! Then, I introduced Hidden Markov models, using which we added meter in the form of the number of syllables as a feature!

## This article's focus

We are going to add structure to our poems. I hope to add Part of Speech (POS) tags as a feature to our HMM. Now there will be 2 unobserved states, part of speech tags, and syllable count it makes this model the Triple Markov Model!

### Drawbacks of the previous method (HMM with Syllables)

![hmmp1](/images/poem-generation/HMMp1.png)

The poem might have some rhythm. There's one major flaw though. The lines of the stanzas have multiple punctuations. Especially, if I let it generate even more lines it completely starts ignoring "newline" character. We need to give it a structure which is specific to poems.

One way I thought it could have a structure is if we add the POS tags to model.

### The Logic

- Find the POS Tags of the words.
- Train another N-gram model based on POS Tags.
- Find a way to combine this N-gram with the HMM model! (Triplet Markov)

### Finding the POS Tags of the words

We want to generate a poem which consists of the words' POS Tags. It is quite important to note that a single word may be tagged with multiple POS tags. I.e. a word like "duck", can be both be the action to duck, or the bird duck. It depends on the context it occurs in. So we can't just have a dictionary-based approach as we did for Syllables in a word!

Initially, I trained an HMM from a tagged dataset, but I soon realized that the tagged dataset wasn't anymore representative than what NLTK offers for POS tagging. I will soon be writing an article, which will implement the Viterbi Algorithm in HMM, implementing this tagging. For now, I stick to using the nltk.pos_tag() function present in the NLTK library.

``` python
#Demonstration of the function returns
import nltk
nltk.pos_tag(["This","is","a","sentence"])
```

![postag](/images/poem-generation/postag.png)

Here, I assume that POS Tags here are accurate. 

Our aim here is two generate two things.

- Poems where words are replaced by their POS Tags.
- A dictionary, where Words are matched with the total occurrences of their POS tags. (So that we know how probable a word is as a tag!)

Also, I have modularized all the data processing we have done so far in the previous articles in a python script called Data. You could check out the script for more details!

``` python
import Data
import nltk
# This consists of all the poems, processed, i.e. lowercase, etc.
poems = Data.PoetryFoundationPoems()
```

The data structure I used to find the counts of a word marked as POS tags as the Bi-grams discussed in N-grams. I.e. A dictionary, where all the keys are the words, and the value of each word is another dictionary which consists of all the POS Tags the word has ever been marked with and its counts!

``` python
# When given a poem marked by the nltk_pos tagger
# A dictionary, which is to be filled, a tagged poem
def word_pos_tags(dic, poems_tagged):
    # Iterate through all the word-tag sequences
    for word, tag in poems_tagged:
        # If the word is already present in the dictionary
        if(word in dic):
            # If the word is already marked with the same tag
            if(tag in dic[word]): 
                # Increase the count of the tag with which the word occurs by 1.
                dic[word][tag] = dic[word][tag]+1
            else:
                # Mark that word with this new tag, and initialize count to 1.
                dic[word][tag] = 1
        else:
            # If the word never occured in dictionary, then create an entry for the word and initialize the tag count to 1.
            dic[word] = {tag:1}
    return dic # Which is now updated!
```
This part of the code takes care of the second aim. Now, let's convert all the poems to their POS tags! One important thing to take care of while tagging poems is, we need to create a new type of tag for "newline" character, as that is something not present in the nltk.pos_tag function.
``` python
# Takes a poem, and dictionary to fill.
def tag_pos(poem, dic):
    poem = [w.lower() for w in poem] #All words lower are neccessary.
    nltk_tags = nltk.pos_tag(poem) #Tagging them with NLTK
    modify_tags = [] # Seperately checking for the NEWLINE char
    only_tags = [] # Poem of Tags!
    for word,tag in nltk_tags:
        if(word == "newline"):
            modify_tags = modify_tags + [(word,"NEWLINE")]
            only_tags = only_tags + ["NEWLINE"]
        else: 
            modify_tags = modify_tags + [(word,tag)]
            only_tags = only_tags + [tag]
    # Adding the words with their POS tags to the dictionary!
    dic = word_pos_tags(dic, modify_tags)
       
    return(only_tags, dic)
```
We have created the functions necessary to achieve the aim. Now let's apply them to our dataset.

``` python
poems_of_tags = [] # 1st aim.
pos_dicts = {} # 2nd aim.
for poem in poems:
    poem_tagged, pos_dicts = tag_pos(poem, pos_dicts)
    poems_of_tags = poems_of_tags + [poem_tagged]
```
### Train another N-gram model based on POS Tags.
We can now start building the N-grams model! Moreover, all of the code we used last time can be used for this. It would be quite inefficient if were to replicate the same code over and over again. Hence, I modulated the entire code in a python file called Models, which has a class called N-grams which represents it! For more details, on that code please check this out!

Now implementing the N-grams is quite simple!

``` python
import model
FourGram         = model.nGrams(poems, 4)
FourGramPOS_tags = model.nGrams(poems_of_tags, 4)
```
There! We have already trained our 4 gram model for our syllable_poems! Moreover, predicting the next word can be done as simply as:
``` python
FourGram.next_word(["I","LOVE","YOU"])
# It will output, ","
```
There's one change I made to the next_word function compared to what we discussed in the previous article. Instead of predicting the next word based on the pure counts, I rescale the counts into probabilities. This change is discussed in the previous article. Its implementation is quite simple!

### Find a way to combine this N-gram with the HMM model! (Triplet Markov)

**The problem:** We already found a way to combine the Syllable N-gram with the normal N-gram model. We wish to apply a similar method to combine the POS Tags N-gram with that model. The slight difficulty here lies in the fact that a word can have multiple POS tags, unlike syllables where each word has a specific syllable count value. So we need to be able to find a way to include that in our probabilities.

Combing these 3 n-grams models, where 2 of the 3 states are unobservable is what we refer to as Triplet Markov Model!

### The logic
Explaining with an example seems simpler. Consider the following dataset:

- He played tennis.
- Children played chess.
- Children played slow.

Consider the word after "played".

For the previous model, we calculate the probabilities (unscaled) after played, are as follows:

- tennis = 3/18
- chess = 2/18
- slow = 2/18

Here, the POS tags of the sentences are, He = preposition, played = verb past tense, tennis = chess = noun, Children = noun plural, slowly = verb.

So the probablity of POS tag after verb past tense is:

- noun = 2/3
- verb = 1/3

The probablity of slow being a verb is 1, chess, tennis being a noun is 1.

Now, the final probabilities(unscaled) would be, for word after played:

- tennis = 3/18 * 2/3 * 1 =6/54
- chess = 2/18 * 2/3 * 1 =4/54 
- slow = 2/18 * 1/3 * 1 = 2/54

Hence, in this case, we are more likely to predict the word "tennis" after the word "played"! Note, that the word still remains to be "tennis". But even in this small example, the final probabilities were affected! When implemented on a larger scale, it makes all the difference!

### Implementing the above model!

Previously we trained the two models, and the one trained in the last article:

``` python
import model
FourGram         = model.nGrams(poems, 4)
FourGramSyllable = model.nGrams(syllable_poems , 4)
FourGramPOS_tags = model.nGrams(poems_of_tags, 4)
```

I hope you are familiar with the nGrams object. It was discussed in the previous article.

The code for the combining POS tags with the Syllable-word HMM is quite similar, except a few major changes. Discussing the entire code here seems to be too clustered. I will post the entire code on my GIT page and attach a link here! Here, I will discuss the entire logic of the code. If there are few requests, I will discuss the entire code here :).

``` python
#Input for the function:
preceding_words, POS_tags_of_words,
words_nGram, Pos_nGram, syllable_nGram,
POS_dictionary_words, Syllable_dictionary.
#Use the N-gram models, and iterate through them to reach the end of the sequence!
#We want to output both the next word and the next POS tag!
Max = 0
next_word = "Error"
next_tag  = "Error2"
# Iterate through all the possible words!
for word,probablity_word in list(wordsDic.items()):
    # Iterate through all the tags possible for the given word
        for tag, prob_tag_given_word in word_POS_p:
            # Multiply all the possible probablities, i.e. the syllable_count, the word, the tag_prob, the_tag_sequence!
            # If all of that is the highest probablity, make it the next word tag sequence!
            if(final_prob>Max):
                Max = final_prob
                next_word = word
                next_tag  = tag
    
return(next_word, next_tag)
```
I really hope that was helpful in implementing, the algorithm!

### Further Improving the model!

One of the major problems that I encountered initially was: There are a lot of single-syllable words used in poetry, it happens to be such that after any syllable word it is most likely that a single syllable word must occur. That certainly, is a big problem. To deal with it I applied smoothing. I will soon be writing an article on smoothing, but for more details right now please check this out!

The smoothing was applied to all aspects of the model, not just the syllables.

Now we have managed to add both rhythm and structure to the poem. Yet, the problem of repetition lies, probably due to overfitting. Moreover, It lacks a lot of aspects, like rhyme, stanza selection, theme. Adding all these things is certainly possible using a Markov model, but it ignores long term dependencies. So we'll have to implement clever models. It's going to get complicated soon! I'll be holding on to post about rhymes, for a while, maybe I will dive right into Neural Net based methods!