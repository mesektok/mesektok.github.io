---
layout: page
title: Home
permalink: /
---

<div class="mt-5">
  {% assign latest_post = site.posts | first %}
  {% if latest_post %}
  <div class="p-4 p-md-5 mb-4 bg-light rounded-3 border shadow-sm">
    <div class="container-fluid py-2">
      <span class="badge bg-danger mb-3">New</span>
      <h1 class="display-5 fw-bold mb-3">{{ latest_post.title }}</h1>
      <div class="text-muted mb-4 small">{{ latest_post.date | date: "%Y-%m-%d" }}</div>
      
      <article class="post-content mb-4 text-secondary">
        {{ latest_post.content }}
      </article>
      
      <hr class="my-4">
      <p class="small text-muted mb-0">위 글은 가장 최신 포스트입니다.</p>
    </div>
  </div>
  {% endif %}
</div>

<div class="my-5 py-3"></div> <div class="pt-2">
  <h2 class="mb-4 pb-3 border-bottom">최근 글</h2>

  {% comment %} 
    offset: 1 -> 위에서 본문 전체를 보여준 최신글 제외
    limit: 2  -> 그 다음 2개만 출력
  {% endcomment %}
  {% assign recent_posts = site.posts | offset: 1 | limit: 2 %}

  <div class="row g-4">
    {% for post in recent_posts %}
    <div class="col-md-6">
      <div class="card h-100 shadow-sm border-0 bg-white">
        <div class="card-body d-flex flex-column p-4">
          <h3 class="card-title h5 mb-2">
            <a href="{{ post.url | relative_url }}" class="text-decoration-none text-dark fw-bold">{{ post.title }}</a>
          </h3>
          <div class="text-muted small mb-3">
            {{ post.date | date: "%Y-%m-%d" }}
          </div>
          <p class="card-text flex-grow-1 text-secondary mb-4">
            {{ post.excerpt | strip_html | truncate: 120 }}
          </p>
          <a href="{{ post.url | relative_url }}" class="btn btn-outline-primary btn-sm mt-auto w-100">
            자세히 보기 →
          </a>
        </div>
      </div>
    </div>
    {% endfor %}
  </div>
</div>
