---
layout: page
title: Home
---

{% assign featured = site.posts.first %}
{% assign recent_posts = site.posts | offset: 1 | limit: 4 %}

<div class="home-hero">
  <h1>mesektok tech-art 블로그</h1>
  <p>개발자 노트, GitHub Pages, Jekyll, 블로그 운영 기록을 정리합니다.</p>
</div>

{% if featured %}
<section class="home-featured">
  <div class="home-section-title">
    <h2>최신 글</h2>
  </div>

  <article class="featured-post">
    <h3>
      <a href="{{ featured.url | relative_url }}">{{ featured.title }}</a>
    </h3>

    <div class="post-meta">
      <span>{{ featured.date | date: "%Y-%m-%d" }}</span>
      {% if featured.categories and featured.categories.size > 0 %}
        <span>· {{ featured.categories | first }}</span>
      {% endif %}
    </div>

    <p>{{ featured.excerpt | strip_html | truncate: 220 }}</p>

    <a class="read-more" href="{{ featured.url | relative_url }}">더 읽기 →</a>
  </article>
</section>
{% endif %}

{% if recent_posts.size > 0 %}
<section class="home-recent">
  <div class="home-section-title">
    <h2>최근 글</h2>
  </div>

  <ul class="recent-post-list">
    {% for post in recent_posts %}
    <li class="recent-post-item">
      <h3>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h3>

      <div class="post-meta">
        <span>{{ post.date | date: "%Y-%m-%d" }}</span>
        {% if post.categories and post.categories.size > 0 %}
          <span>· {{ post.categories | first }}</span>
        {% endif %}
      </div>

      <p>{{ post.excerpt | strip_html | truncate: 120 }}</p>
    </li>
    {% endfor %}
  </ul>
</section>
{% endif %}
