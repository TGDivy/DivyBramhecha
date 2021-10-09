---
layout: single
title: "Resume"
permalink: /cv/
author_profile: true
classes: wide
redirect_from:
  - /resume
---
{% include base_path %}
---

### Education

**BSc. Hons Artificial Intelligence**, The University of Edinburgh

<p style="text-align:left;">
    First Class Honors
    <span style="float:right;">
        Sept 2018 - May 2022
    </span>
</p>

---

### EXPERIENCE

---

**Data Science Intern [teampage](https://adarga.ai/article/welcome-adarga-interns-2021)** 

<p style="text-align:left;">
    at Adarga, London.
    <span style="float:right;">
        June 2021 - Sept 2021
    </span>
</p>

- Work in an agile, user-centered space.
- Prototyping, developing and deploying natural language models in a continuous integration and deployment following the MLOps principles.
- Making extensive use of Pachyderm, Seldon and ArgoCD while prototying models.
- Deploying the machine learning models in Kubernetes with Apache Kafka.
- Creating user centric analysis reports, to inform the direction of the projects.

---

**NLP Research Intern [teampage](https://www.cs.hhu.de/en/research-groups/dialog-systems-and-machine-learning/our-team/alumni)** 

<p style="text-align:left;">
    at Dialog Systems And Machine Learning Group, Heinrich Heine Universität
    <span style="float:right;">
        May 2020 - Oct 2020
    </span>
</p>

Supervised by Dr. Milica Gasic and Dr. Michael Heck, I worked on [DSTC9 competition](https://dstc9.dstc.community/tracks) 
- Beyond Domain APIs: Task-oriented Conversational Modeling with Unstructured Knowledge Access. 
- I developed novel algorithms to extract the correct knowledge entry/ intent and match them with external knowledge sources. 
- This allows for friction-less conversation in cases where user requests are out of scope of existing APIs and Data bases.
- I developed and experimented with various models, writing distributed GPU code in PyTorch, and Google Cloud.

---

### [TEACHING](/teaching)

<p style="text-align:left;">
    <a href="http://www.drps.ed.ac.uk/20-21/dpt/cxinfd11005.htm">
    Introduction to Applied Machine Learning
    </a>
    <span style="float:right;">
        Semester 1, Semester 2
    </span>
    <br>
    <b>Roles:</b> Tutor, Demonstrator, Marker
    <span style="float:right;">
        2020, 2021
    </span>
</p>

<p style="text-align:left;">
    <a href="http://www.drps.ed.ac.uk/19-20/dpt/cxinfr08025.htm">
    Introduction to Computation
    </a>
    <span style="float:right;">
        Semester 1
    </span>

    <br>
    <b>Roles:</b> Tutor, Demonstrator, Marker
    <span style="float:right;">
        2019, 2020
    </span>
</p>

<p style="text-align:left;">
    <a href="http://www.drps.ed.ac.uk/20-21/dpt/cxinfr08010.htm">
    Informatics 2D - Reasoning and Agents
    </a>
    <span style="float:right;">
        Semester 1, Semester 2
    </span>
    <br>
    <b>Roles:</b> Demonstrator
    <span style="float:right;">
        2020
    </span>
</p>

---

### [PROJECTS](/teaching)


<!-- **AI based Poem generator with [tutorial blogs.](https://web.archive.org/web/20191215191035/https://divybramhecha.tech/category/research/)** 
Using Natural Language Modelling, and Understanding with Probabilistic, feature Engineering Approach to generate unique poems! Writing tutorials on the same in blog posts, clearly communicating the knowledge of the subject.

**Web Scrapper for Poetry Foundation
[link.](https://www.kaggle.com/tgdivy/poetry-foundation-poems)** 
Using Selenium and Beautiful Soup, collecting all the poems, and their respective tags, poets, title from Poetry Foundation. The complete process is found in a tutorial blog series and communicated in a clear, precise manner.

**Investigating how RNN's work
[link.](https://github.com/TGDivy/RNNs-Subject-verb)** 
This was part of a masters level course at the university, where I in a team of 2, investigate how RNN's work. We investigate how the performance changes with the number of back steps used in the algorithm as the distance
between the subject and the verb increases.

**MB-TI Personality classifier
[link.](https://github.com/TGDivy/MBTI-Personality-Classifier)** 
This was one my first projects in NLP. Created a group of classifier NLP models which use your social media posts to predict your MB-TI personality type. -->

{% for staff_member in site.portfolio reversed%}

<a href="{{ staff_member.url }}">
    {{ staff_member.title }}

![alt]({{ staff_member.header_teaser }}){:width="250px"}

{{staff_member.excerpt}}

---

{% endfor %}