
---
layout: default
title: Home
---

<h1>最新文章</h1>

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      <span> - {{ post.date | date_to_string }}</span>
    </li>
  {% endfor %}
</ul>