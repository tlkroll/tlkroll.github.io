---
layout: universal
title: Main Page
image: /thumbnail.png
---
<div>
<h1>Malware Analysis & Reverse Engineering</h1>
<p>
My work involves static and dynamic analysis, low-level debugging, and examining how malicious code interacts with operating system internals. I’m particularly interested in translating this analysis into practical detection strategies.
</p>
<p>
I document my work through detailed write-ups that highlight both technical depth and real-world applicability.
</p>
<p>
Currently seeking Malware Analysis / Detection Engineering roles.
</p>
<br>
<p>
Here are a few samples from my Malware Analysis page:
</p>
</div>
<br>
<br>

{% for post in site.categories.featured %}
  <div>
    <h2>{{ post.excerpt }}</h2>
    <a href="{{ post.url }}">
      <img src="{{ post.thumbnail }}" alt="{{ post.title }}" />
    </a>
  </div>
{% endfor %}   