---
layout: page
title: Home
---

{% assign featured = site.posts.first %}
{% assign recent_posts = site.posts | offset: 1 | limit: 4 %}

{% if featured %}
<article class="home-featured-post">
  <!-- 여기 안은 아까 드린 Liquid/HTML 그대로 -->
</article>
{% endif %}

{% if recent_posts.size > 0 %}
<section class="home-recent mt-5">
  <!-- 최근 글 카드 코드 -->
</section>
{% endif %}
