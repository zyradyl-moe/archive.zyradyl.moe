---
layout: default
---

<h1>Tags</h1>
<ul class="tags">
{% for tag in site.tags %}
  {% assign t = tag | first %}
  {% assign posts = tag | last %}
  <li>
    <a href="#{{t | downcase | replace " ","-"}}">
      {{t | downcase | replace:" ","-" }}
    </a>
 </li>
{% endfor %}
</ul>

{% for tag in site.tags %}
  {% assign t = tag | first %}
  {% assign posts = tag | last %}

<h1>{{ t | downcase }}</h1>
<ul>
{% for post in posts %}
  {% if post.tags contains t %}
  <li>
    <a href="{{ post.url }}">{{ post.title }}</a>
    <span class="date">{{ post.date | date: "%B %-d, %Y"  }}</span>
  </li>
  {% endif %}
{% endfor %}
</ul>
{% endfor %}
