---
layout: archive
permalink: /posts/
title: "Blog Posts"
author_profile: true
---

{% include base_path %}

{% assign valid_tags = "poem-generation, web-scraping" | split: ', ' % %}

<ul class="taxonomy__index">

{% for tag in site.tags %}
{% if valid_tags contains tag[0] %}
{% assign posts = tag[1] %}
  <li>
  <a href="#{{ tag[0] }}">
        <strong>{{ tag[0] }}</strong> <span class="taxonomy__count">{{ posts | size }}</span>
    </a>
</li>
{% endif %}
{% endfor %}
</ul>

{% for tag in site.tags %}
{% if valid_tags contains tag[0] %}
{% assign posts = tag[1] %}

<section id="{{ tag[0] }}" class="taxonomy__section">
    <h2 class="archive__subtitle">{{ tag[0] }}</h2>
    <div class="entries-{{ entries_layout }}">
      {% for post in posts %}
        {% include archive-single.html type=entries_layout %}
      {% endfor %}
    </div>
    <a href="#page-title" class="back-to-top">{{ site.data.ui-text[site.locale].back_to_top | default: 'Back to Top' }} &uarr;</a>
</section>


{% endif %}
{% endfor %}
