---
layout: page
title: Home
permalink: /
---

<div class="mt-5 mb-5">
  {% assign latest_post = site.posts | first %}
  {% if latest_post %}
    <div class="latest-post-hero">
      <div class="text-muted mb-2 small">New</div>
      <h1 class="display-5 fw-bold mb-3">{{ latest_post.title }}</h1>
      <div class="text-muted mb-4 small">{{ latest_post.date | date: "%Y-%m-%d" }}</div>
      
      <article class="post-content text-secondary lh-lg">
        {{ latest_post.content }}
      </article>
      
      <div class="mt-5 mb-5 border-bottom" style="opacity: 0.5;"></div>
    </div>
  {% endif %}
</div>

<div class="pt-2">
  <h2 class="h4 mb-4 fw-bold">최근 글</h2>

  <div class="row g-4">
    {% comment %} 
      - offset: 1 (첫 번째 글은 위에서 보여줬으니 제외)
      - limit: 2  (이미지처럼 딱 2개만 노출)
    {% endcomment %}
    {% for post in site.posts offset: 1 limit: 2 %}
    <div class="col-md-6">
      <div class="card h-100 shadow-sm" style="border: 1px solid #dee2e6; border-radius: 0.5rem;">
        <div class="card-body d-flex flex-column p-4">
          
          <h3 class="card-title h5 mb-2">
            <a href="{{ post.url | relative_url }}" class="text-decoration-none" style="color: #0056b3;">
              {{ post.title }}
            </a>
          </h3>
          
          <div class="text-muted small mb-4">
            {{ post.date | date: "%Y-%m-%d" }}
          </div>
          
          <p class="card-text flex-grow-1 text-secondary mb-4">
            {{ post.excerpt | strip_html | truncate: 120 }}
          </p>
          
          <a href="{{ post.url | relative_url }}" class="btn btn-outline-primary w-100" style="border-radius: 0.3rem;">
            자세히 보기 →
          </a>
          
        </div>
      </div>
    </div>
    {% endfor %}
  </div>
</div>
