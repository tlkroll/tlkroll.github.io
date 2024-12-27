---
layout: universal
title: Projects
permalink: /projects/
---
This page is intended to feature non-coding projects I've worked on. To see examples of programming projects, please visit my <a href="/code">Code</a> page
<ul>
  {% for post in site.categories.projects %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
    <br>
  {% endfor %}
</ul>