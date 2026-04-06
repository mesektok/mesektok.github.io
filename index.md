---
layout: page
title: Home
permalink: /
---

<div class="mt-5 pt-4">
  <h2 class="mb-4 pb-3 border-bottom">최근 글</h2>

  {% comment %} offset을 제거하여 모든 글을 대상으로 확인합니다 {% endcomment %}
  {% assign recent_posts = site.posts | limit: 4 %}

  <div class="row g-4">
    {% for post in recent_posts %}
    <div class="col-md-6">
      <div class="card h-100">
        <div class="card-body d-flex flex-column">
          <h3 class="card-title">
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
    {% else %}
      <p class="text-center">현재 등록된 게시글이 없습니다.</p>
    {% endfor %}
  </div>
</div>
