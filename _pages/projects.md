---
permalink: /projects/
title: "Projects"
author_profile: true
layout: collection
entries_layout: grid
classes: wide
---


Here are some of my research and engineering projects:

{% assign sorted_projects = site.projects | sort: "order" %}
{% for project in sorted_projects %}
- **[{{ project.title }}]({{ project.url }})**  
  🛠 **Tools Used:** {{ project.tools }}
{% endfor %}
