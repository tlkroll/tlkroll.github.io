---
layout: universal
title: Projects
permalink: /projects/
---
<ul>
  {% for post in site.categories.projects %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
    <br>
  {% endfor %}
</ul>