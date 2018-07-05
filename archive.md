---
layout: page
title: Archive
sidebar_link: true
---

<section class = "archive-post-list">

  {% for post in site.posts %}
    {% assign currentDate = post.date | date: "%Y.%m" %}
    {% if currentDate != myDate %}
      {% unless forloop.first %}</ul>{% endunless %}
      <h1>{{ currentDate }}</h1>
      <ul>
      {% assign myDate = currentDate %}
    {% endif %}
    <li><a href="{{ post.url }}"><span>{{ post.date | date: "%m.%d" }}</span> - {{ post.title }}</a></li>
    {% if forloop.last %}</ul>{% endif %}
  {% endfor %}

</section>
