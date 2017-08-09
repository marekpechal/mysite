---
layout: page
title: Photography & 3d graphics
tags:
- navbar
---

<h2> Photography </h2>

Here will be links to photography stuff.

{% for page in site.pages %}
{% if page.tags contains "photography" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}

<h2> Blender </h2>

Here will be links to Blender stuff.

{% for page in site.pages %}
{% if page.tags contains "blender" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}

