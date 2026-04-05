---
layout: page
title: Home
permalink: /
---

{% assign featured = site.posts.first %}
{% assign recent_posts = site.posts | offset: 1 | limit: 2 %}
{% if featured %}
<article class="home-featured-post">
  <header class="mb-5">
    <h1>{{ featured.title }}</h1>

    <div class="post-meta mb-3">
      <span>{{ featured.date | date: "%b %-d, %Y" }}</span>
      {% if featured.categories and featured.categories.size > 0 %}
        <span> · {{ featured.categories | first }}</span>
      {% endif %}
      <span> · By {{ featured.author | default: site.social.name }}</span>
    </div>
  </header>

  <div class="post-content">
    {{ featured.content }}
  </div>
</article>
{% endif %}

{% if recent_posts.size > 0 %}
<section class="home-recent mt-5">
  <h2 class="mb-4">최근 글</h2>

  <div class="recent-grid">
    {% for post in recent_posts %}
    <article class="recent-card">
      <h3>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h3>

      <div class="post-meta mb-2">
        <span>{{ post.date | date: "%Y-%m-%d" }}</span>
        {% if post.categories and post.categories.size > 0 %}
          <span> · {{ post.categories | first }}</span>
        {% endif %}
      </div>

      <p>{{ post.excerpt | strip_html | truncate: 140 }}</p>
    </article>
    {% endfor %}
  </div>
</section>
{% endif %}
