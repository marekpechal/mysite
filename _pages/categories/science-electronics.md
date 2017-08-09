---
layout: page
title: Science & Electronics
tags:
- navbar
---

<h2>Physics</h2>

Here will be links to physics stuff.

{% for page in site.pages %}
{% if page.tags contains "physics" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}

<h2>Math</h2>

Here will be links to math stuff.

{% for page in site.pages %}
{% if page.tags contains "math" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}

<h2>Chemistry</h2>

Here will be links to chemistry stuff.

{% for page in site.pages %}
{% if page.tags contains "chemistry" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}


<h2>Electronics</h2>

Here will be links to electronics stuff.

{% for page in site.pages %}
{% if page.tags contains "electronics" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}


<h2>Home lab equipment</h2>

Here will be links to home lab equipment stuff.

{% for page in site.pages %}
{% if page.tags contains "home-lab" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}


<h2>3d printing</h2>

Here will be links to 3d printing.

{% for page in site.pages %}
{% if page.tags contains "3d-printing" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}

