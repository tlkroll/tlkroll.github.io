---
layout: universal
title: Write-ups
permalink: /write-ups/
---
<ul>
  {% for post in site.categories.write-ups %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
    <br>
  {% endfor %}
</ul>