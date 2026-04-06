---
layout: home
title: Home
permalink: /
---

{%- assign recent_posts = site.posts | where_exp: "post", "post.hidden != true" -%}

<div class="home-recent mt-5">
  <h2 class="mb-4">최근 글</h2>

  <div class="row g-4">
    {%- for post in recent_posts limit:2 -%}
    <div class="col-md-6 mb-4">
      <div class="card h-100">
        <div class="card-body d-flex flex-column">
          <h3 class="card-title">
            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          </h3>
          <div class="text-muted small mb-3">
            {{ post.date | date: "%Y-%m-%d" }}
          </div>
          <p class="card-text flex-grow-1">
            {{ post.excerpt | strip_html | truncate: 150 }}
          </p>
          <a href="{{ post.url | relative_url }}" class="btn btn-outline-primary btn-sm mt-auto">
            자세히 보기 →
          </a>
        </div>
      </div>
    </div>
    {%- endfor -%}
  </div>
</div>

<div class="home-more my-5">
  <a class="btn btn-primary" href="{{ '/archives/' | relative_url }}">
    최근 글 더 보기 →
  </a>
</div>
