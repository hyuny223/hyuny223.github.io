---
title: "나의 우분투 세팅"
layout: archive
permalink: /categories/ubuntu
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.Ubuntu %}
{% for post in posts %} {% include archive-category.html type=page.entries_layout %} {% endfor %}