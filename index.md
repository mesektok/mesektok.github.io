---
layout: page
title: Home
permalink: /
---

<div class="mt-5">
  {% assign latest_post = site.posts | first %}
  {% if latest_post %}
  <div class="p-5 mb-4 bg-light rounded-3 border">
    <div class="container-fluid py-2">
      <span class="badge bg-danger mb-2">New</span>
      <h1 class="display-5 fw-bold">{{ latest_post.title }}</h1>
      <p class="col-md-8 fs-4 text-muted">{{ latest_post.excerpt | strip_html | truncate: 120 }}</p>
      <a href="{{ latest_post.url | relative_url }}" class="btn btn-primary btn-lg">읽어보기</a>
    </div>
  </div>
  {% endif %}
</div>

<hr class="my-5">

<div class="pt-2">
  <h2 class="mb-4 pb-3 border-bottom">최근 글</h2>

  {% comment %} 
    offset: 1 -> 가장 최신글 하나를 건너뜁니다.
    limit: 2  -> 그 다음부터 딱 2개만 가져옵니다.
  {% endcomment %}
  {% assign recent_posts = site.posts | offset: 1 | limit: 2 %}

  <div class="row g-4">
    {% for post in recent_posts %}
    <div class="col-md-6">
      <div class="card h-100 shadow-sm">
        <div class="card-body d-flex flex-column">
          <h3 class="card-title h5">
            <a href="{{ post.url | relative_url }}" class="text-decoration-none text-dark">{{ post.title }}</a>
          </h3>
          <div class="text-muted small mb-3">
            {{ post.date | date: "%Y-%m-%d" }}
          </div>
          <p class="card-text flex-grow-1 text-secondary">
            {{ post.excerpt | strip_html | truncate: 100 }}
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
