---
title: x64 asm
---

# ASM Snippets

Listado de snippets en `/asm`:

<ul>
{% for p in site.pages %}
  {% if p.path contains 'asm/' and p.name != 'index.md' %}
    <li><a href="{{ p.url | relative_url }}">{{ p.title | default: p.name }}</a></li>
  {% endif %}
{% endfor %}
</ul>
