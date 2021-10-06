---
title: 'Web Scrapping with Beautiful Soup. (Part-1a)'
date: 2014-08-14
permalink: /posts/web-scraping/bs/
excerpt: This article focuses on how to use Beautiful Soup for extracting information from the Poetry Foundation.
tags:
  - web-scraping
header: 
  overlay_image: /images/web-scraping/background1.jpeg
  # opacity: 1
  # overlay_filter: linear-gradient(rgba(20, 20, 20, 0.5), rgba(50, 0, 0, 0.0))
  show_overlay_excerpt: false
  teaser: /images/web-scraping/background1.jpeg
---

This article focuses on how to use Beautiful Soup for extracting information from the Poetry Foundation.

No tagged dataset for poems exist. Hence, I have decided to scrape the web for my own dataset.

For this task, I have decided to use the Poetry Foundation as the source for all poems as it seems to have a really massive collection, moreover, they are all tagged by the topics. Maybe, later when training the system, I might require texts other than poems for the given topics and so I might write another article later describing how I extract that information.

---

Installing Beautiful Soup.

```
$ pip install beautifulsoup4
```

Importing the necessary libraries.

```python
import urllib.request
import bs4 as bs
import re
```

Those are the basic libraries needed for reading and navigating the webpages efficiently.

First, we need to create a BeautifulSoup object from the provided URL. This is needed to parse the webpage with this API.

Most websites require a header, i.e. the browser signature before they send their HTML code.

```python
url = "https://www.poetryfoundation.org/poems/150097"
hdr = {'User-Agent':'Mozilla/5.0'}
req = urllib.request.Request(url,headers=hdr)
sauce = urllib.request.urlopen(req).read()
soup = bs.BeautifulSoup(sauce,'html.parser') #Beautiful Soup object
```

The above step is required for most websites. Now that we have the Beautiful Soup object, we can use that to navigate through HTML tags.

Some basic knowledge of HTML tags is required. https://www.w3schools.com/html/default.asp This website contains good introduction on HTML and it doesn't take that long to learn it.

The most important thing about the HTML is that it is reduced to 4 types of objects, the basic and important one is called the 'tag'. Finding the correct tag is the key to finding the desired information.

Hence, we must learn more about tags. Tags have a name and can have attributes. These HTML trees can be navigated by traveling through the tag names.

```python
soup.body # This will find the first body tag and returns that #branch.
```

This is generally only used when you know the tag you want to zoom in on. Furthermore, one might want all the values of a certain tag. Especially when one wants all the Url's, or all the paragraphs, and other such cases. Hence, we use the find_all() function.

```python
find_all("h1") #Returns all the tag branches containing h1 tag
#eg: Poetry Foundation
```

This could be quite useful, but most websites these days are long HTML and have a lot of such tags. They add attributes to them, hence we want to filter them by their attribute details. Something like:

```python
<div class="section-content"> This is the Content</div>
```

Most of the information which we desire is very specific like the attribute names. This can be found by inspecting the element of the webpage on the browser. Hence, we always want to extract tags which are very specific. This can be done by adding attribute arguments to the find_all function.

```python
soup.find_all('div', class_="section-content")
# This is the content
```

A lot of times, you might not know the exact content of the attribute, or might want multiple contents. Like, something that matches both

```python
<div class="section-name"> Name </div>
<div class="section-content"> This is the Content</div>
```

This is where regular expressions come handy. The article belove explains regexp quite well. 
https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285

```python
(soup.find_all('div', class_=re.compile("section.*"))[0].text)
[Name, This is the Content]
```

The last important thing to know is accessing information from the attribute section.

```python
<a href="https://poetryfoundation.org/poems/150097"> Name </a>
```

If we extract information using previous methods, we get the wrong output. So we need to use the get function.

```python
soup.a #Name
soup.get("href") #https://poetryfoundation.org/poems/150097
```

---

In this section, I will go over the code I initially used to extract information from the Poetry Foundation. You can use this code to perform a web scrapping of your own to get the feel of how it works. The URL I used for this code is: https://www.poetryfoundation.org/poems/150097/immediately-motionless-likeness

The entire code can be found on the GitHub page: https://github.com/TGDivy/WebScrapping-PoetryFoundation

I had to write a function which reformated the Unicode into readable, printable text. Then, I could easily extract the poem as it was inside the "o-poem" class which was unique in the webpage.

```python
def pretty_text(text):
    final = (((text).replace(u'\xa0', u' ')).replace(u'\r ',u'\n'))
    return final
poem = (pretty_text(soup.find_all('div', class_="o-poem")[0].text))
```

Similarly, I wanted other available information about the poem like its title, its poet, and its tags. Hence, using regular expression and attribute selection I extract that information as well.

```python
title = soup.find_all('h1')[0].text
poet = soup.find_all('a', href=re.compile('.*poets/.*'))[0].text
tags = soup.find_all('a', href=re.compile('.*topics.*'))
tags = [tag.text for tag in tags]
tags = ",".join(tags)
```

---

Now that I have extracted the poem from the given URL, I still need to extract all such URL's where I can extract these poem details from. Apparently, the browsing page for the tutorial has all its information loaded through javascript. Hence, in the next article, I will discuss how to extract the URLs from the Poetry Foundation.