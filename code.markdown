---
layout: universal
title: Code
permalink: /code/
---
<ul>
  {% for post in site.categories.code %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>