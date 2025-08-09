---
title: "AWS"
author_profile: true
layout: archive
permalink: /book
sidebar:
  nav: "sidebar-category"
---


{% assign posts = site.categories.book %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}