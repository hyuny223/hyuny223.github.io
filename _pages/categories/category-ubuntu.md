---
title: "Ubuntu"
layout: archive
permalink: /categories/ubuntu
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.Ubuntu %} <!--카테고리명 : Ubuntu-->
{% for post in posts %} {% include archive-category.html type=page.entries_layout %} {% endfor %}
