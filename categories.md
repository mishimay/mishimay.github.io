---
layout: page
title: カテゴリ一覧
---

{% for category in site.categories %}
  {% capture name %}{{ category | first }}{% endcapture %}
  <h2 id="{{ name }}">{{ name }}</h2>
  <ul>
  {% for post in site.categories[name] %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      <span>{{ post.date | date: site.minima.date_format }}</span>
    </li>
  {% endfor %}
  </ul>
{% endfor %}
