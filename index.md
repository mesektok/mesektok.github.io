---
layout: home
title: Home
permalink: /
---

<!-- 최근 글 2개만 별도 표시 -->
{% assign recent_posts = site.posts | offset: 1 | limit: 2 %}

<section class="mt-5">
  <h2>최근 글 (2개만)</h2>
  <div class="row">
    {% for post in recent_posts %}
    <div class="col-md-6 mb-4">
      <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
      <p>{{ post.excerpt | strip_html | truncate: 140 }}</p>
    </div>
    {% endfor %}
  </div>
</section>
