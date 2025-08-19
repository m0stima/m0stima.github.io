---
title: x64 asm exercises
---

# x64 asm exercises

<ul>
{% for p in site.pages %}
  {% if p.path contains 'x64asm/' and p.name != 'index.md' %}
    <li><a href="{{ p.url | relative_url }}">{{ p.title | default: p.name }}</a></li>
  {% endif %}
{% endfor %}
</ul>
