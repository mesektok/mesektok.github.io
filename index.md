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
{% endfor %} </div> <div class="text-center mt-5 mb-5">
    <a href="{{ '/archives/' | relative_url }}" class="btn btn-outline-primary px-4 py-2" style="border-radius: 2rem; font-weight: 500;">
      최근 글 더 보기  →
    </a>
  </div>
</div> ```

### 🛠️ 왜 이렇게 수정했나요?

1.  **`text-center`**: 버튼을 왼쪽 구석이 아닌 화면 중앙에 배치해서 시각적 균형을 맞췄습니다.
2.  **`btn-outline-primary`**: 3형제 카드에 썼던 파란색 테두리 스타일과 통일감을 주었습니다.
3.  **`px-4 py-2` & `border-radius: 2rem`**: 버튼을 약간 도톰하고 둥글게 만들어 "더 보기"라는 액션을 더 강조했습니다.
4.  **`mt-5`**: 카드와 버튼 사이가 너무 붙지 않게 적당한 여백을 주었습니다.

---

### 🧐 만약 '아카이브' 말고 그냥 리스트를 더 펼치고 싶다면?

현재 코드는 클릭 시 **전체 글 목록 페이지(Archives)**로 이동하는 방식입니다. Chirpy 테마에서는 이 방식이 가장 깔끔하고 관리하기 편합니다.

이제 버튼까지 달아주면 완벽한 홈 화면이 되겠네요! 적용해 보시고 버튼 위치나 모양이 마음에 드시는지 확인해 보세요. 🐜✨
  
</div>
