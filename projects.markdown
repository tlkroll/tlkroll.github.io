---
layout: universal
title: Projects
permalink: /projects/
---
<h2>Nothing here yet</h2> 
This page is intended to feature physical projects I'm working on. To see examples of coding projects, please visit my <a href="/code">Code</a> page
<ul>
  {% for post in site.categories.projects %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
    <br>
  {% endfor %}
</ul>