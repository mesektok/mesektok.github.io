---
layout: home
title: Home
permalink: /
---

{% assign recent_posts = site.posts | offset: 1 | limit: 2 %}

<section class="mt-5">
  <h2 class="mb-4">최근 글</h2>
  <div class="row g-4">
    {% for post in recent_posts %}
    <div class="col-md-6">
      <div class="card h-100">
        <div class="card-body">
          <h3>
            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          </h3>
          <div class="small text-muted mb-2">
            {{ post.date | date: "%Y-%m-%d" }}
          </div>
          <p>
            {{ post.excerpt | strip_html | truncate: 150 }}
          </p>
          <a href="{{ post.url | relative_url }}" class="btn btn-outline-primary btn-sm">자세히 보기 →</a>
        </div>
      </div>
    </div>
    {% endfor %}
  </div>
</section>
