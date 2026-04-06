---
layout: home
title: Home
permalink: /
---

<!-- 최근 글 정확히 2개만 표시 -->
{% assign recent_posts = site.posts | offset: 1 | limit: 2 %}

<div class="mt-5">
  <h2 class="mb-4 pb-2 border-bottom">최근 글</h2>
  
  <div class="row g-4">
    {% for post in recent_posts %}
    <div class="col-md-6 mb-4">
      <div class="card h-100 shadow-sm">
        <div class="card-body d-flex flex-column">
          <h3 class="card-title fs-5">
            <a href="{{ post.url | relative_url }}" class="text-decoration-none">{{ post.title }}</a>
          </h3>
          
          <div class="text-muted small mb-3">
            {{ post.date | date: "%Y-%m-%d" }}
          </div>
          
          <p class="card-text flex-grow-1">
            {{ post.excerpt | strip_html | truncate: 160 }}
          </p>
          
          <a href="{{ post.url | relative_url }}" class="btn btn-outline-primary btn-sm mt-auto">
            자세히 보기 →
          </a>
        </div>
      </div>
    </div>
    {% endfor %}
  </div>
</div>
