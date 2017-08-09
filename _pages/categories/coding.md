---
layout: page
title: Coding
tags:
- navbar
---

Here will be links to coding stuff.

{% for page in site.pages %}
{% if page.tags contains "coding" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}

