---
title: x64 asm exercises
---

# x64 asm exercises

List of exercises in `/docs`:

<ul>
{% for p in site.pages %}
  {% if p.path contains 'docs/' and p.name != 'index.md' %}
    <li><a href="{{ p.url | relative_url }}">{{ p.title | default: p.name }}</a></li>
  {% endif %}
{% endfor %}
</ul>
