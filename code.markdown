---
layout: universal
title: Code
permalink: /code/
---
These are some of the projects I completed while studying computer science. Looking back at the code, there is certainly a lot I would change, but there were also a lot of requirements I needed to adhere to which did not make for the cleanest code. They are still good examples which show that I am familiar with Java, C++, Python, and even some front-end frameworks.
<ul>
  {% for post in site.categories.code %}
    <li>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.excerpt }}
    </li>
    <br>
  {% endfor %}
</ul>