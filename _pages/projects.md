---
permalink: /projects/
title: "Projects"
author_profile: true
layout: collection
entries_layout: grid
classes: wide
---

# Projects

Here are some of my research and engineering projects:

{% for project in site.projects %}
- **[{{ project.title }}]({{ project.url }})**  
  📅 **Published:** {{ project.date | date: "%B %d, %Y" }}  
  {{ project.description }}
{% endfor %}
