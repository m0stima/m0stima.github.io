---
title: x64 asm exercises
---

# References
```
https://www.felixcloutier.com/x86/
https://cburch.com/csbsju/cs/350/docs/nasm/nasmdoc0.html
https://learn.microsoft.com/en-us/cpp/build/x64-calling-convention?view=msvc-170
https://learn.microsoft.com/en-us/cpp/build/x64-software-conventions?view=msvc-170
```

# Basic commands
```cmd

-- Developer Command Prompt for VS 2022 --

nasm -f win64 <file_name>.asm -o <file_name>.obj
link /nologo /entry:main /subsystem:console <file_name>.obj /defaultlib:kernel32.lib /libpath:"C:\Program Files (x86)\Windows Kits\10\Lib\10.0.26100.0\um\x64"
link /nologo /entry:main /subsystem:console <file_name>.obj /debug /defaultlib:kernel32.lib /libpath:"C:\Program Files (x86)\Windows Kits\10\Lib\10.0.26100.0\um\x64"
```

# x64 asm exercises

<ul>
{% for p in site.pages %}
  {% if p.path contains 'x64asm/' and p.name != 'index.md' %}
    <li><a href="{{ p.url | relative_url }}">{{ p.title | default: p.name }}</a></li>
  {% endif %}
{% endfor %}
</ul>
