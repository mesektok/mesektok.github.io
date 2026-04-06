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
      limit를 3으로 늘려줍니다.
    {% endcomment %}
    {% for post in site.posts offset: 1 limit: 3 %}
    
    <div class="col-md-4">
      <div class="card h-100 shadow-sm border-0 bg-white" style="border-radius: 0.8rem;">
        <div class="card-body d-flex flex-column p-4">
          
          <div class="text-muted small mb-2">
            {{ post.date | date: "%b %-d, %Y" }}
          </div>
          
          <h3 class="card-title h5 mb-3">
            <a href="{{ post.url | relative_url }}" class="text-decoration-none text-dark fw-bold">
              {{ post.title }}
            </a>
          </h3>
          
          <p class="card-text flex-grow-1 text-secondary small mb-0">
            {{ post.excerpt | strip_html | truncate: 80 }}
          </p>
          
        </div>
      </div>
    </div>
    {% endfor %}
  </div>
</div>
