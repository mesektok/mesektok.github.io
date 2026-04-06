---
title: "첫 번째 포스트를 올리고 홈에 노출시키는 방법"
date: 2026-04-06 09:00:00 +0900
categories: [guide]
tags: [post, chirpy, jekyll]
---

블로그를 만들고 나면 가장 먼저 확인하고 싶은 것은 글이 홈 화면에 잘 나오는지입니다.

포스트 파일 이름 규칙, 날짜 형식, 카테고리, 태그가 기본 구조에 맞으면 홈에 최근 글 목록이 자연스럽게 표시됩니다.

홈 화면을 조금만 커스터마이징하면 최신 글 1개를 강조하고 최근 글 목록도 보기 좋게 구성할 수 있습니다.

이 글에서는 첫 글 작성부터 홈 노출까지의 흐름을 간단히 정리해 보겠습니다.
---

## 1. 포스트 파일 만들기

Chirpy 블로그에서 글은 모두 `_posts` 폴더 안에 들어갑니다.

1. GitHub 저장소에서 `_posts` 폴더로 이동합니다.
2. **Add file → Create new file** 을 클릭합니다.
3. 파일 이름을 다음 규칙대로 작성합니다.

```text
YYYY-MM-DD-슬러그.md
```

예시:

```text
2026-04-06-first-post-and-home-expose.md
```

- `YYYY-MM-DD` 부분은 글의 날짜.
- `슬러그` 부분은 영문 소문자와 `-` 만 사용하면 안전합니다.

파일 이름이 이 규칙을 따라야  
Chirpy가 글 날짜와 URL을 정확히 인식합니다. 
---

## 2. front matter(상단 설정) 작성하기

파일 맨 위에는 반드시 `---` 로 시작하는 설정 블록이 있어야 합니다.

```markdown
***
title: "첫 번째 포스트를 올리고 홈에 노출시키는 방법"
date: 2026-04-06 09:00:00 +0900
categories: [guide]
tags: [post, chirpy, jekyll]
***
```

- `title` : 홈/목록/상단에 표시되는 글 제목.
- `date` : 글 날짜와 시간.  
  - 형식은 `YYYY-MM-DD HH:MM:SS +0900` 처럼 타임존까지 적는 것이 좋습니다.
- `categories` : 글이 속한 상위 분류. 보통 1개만 넣습니다.
- `tags` : 검색·필터링용 키워드. 여러 개 가능.

이 front matter가 없거나 `---` 가 빠지면  
Jekyll이 글로 인식하지 않아서 홈에 나오지 않습니다. 

---

## 3. 본문 내용 작성하기

front matter 아래 한 줄 띄우고 글을 작성합니다.

```markdown
블로그를 만들고 나면 가장 먼저 확인하고 싶은 것은  
**내가 쓴 글이 홈 화면에 잘 보이는지**입니다.

여기부터는 일반 마크다운 문법으로
자유롭게 내용을 작성하면 됩니다.
```

- 제목은 `##`, `###` 로 구분합니다.
- 이미지가 있다면 `/assets/...` 절대 경로를 쓰면 안전합니다.

본문은 홈 노출 여부에 직접 영향을 주지는 않지만,  
글이 빈 상태면 나중에 테스트할 때 헷갈릴 수 있으니  
짧게라도 내용을 채워 두는 게 좋습니다.

---

## 4. 커밋하고 GitHub Pages 빌드 확인하기

1. 글을 저장한 뒤, GitHub에서 **Commit changes** 를 눌러 커밋합니다.
2. 저장소 상단의 **Actions** 탭으로 이동합니다.
3. `Deploy Jekyll site to Pages` 또는 비슷한 워크플로우가  
   초록색 체크(✅)로 성공했는지 확인합니다. 

- 성공하면 GitHub Pages가 새로운 HTML을 생성한 상태입니다.
- 실패(❌)라면 로그를 열어 문법 오류(front matter, Liquid 등)를 먼저 해결해야  
  홈 화면에 글이 반영됩니다.

---

## 5. 홈 화면에서 노출 확인하기

1. 브라우저에서 자신의 블로그 주소를 엽니다.  
   - 예: `https://username.github.io` 또는 커스텀 도메인.
2. Chirpy 기본 테마에서는 홈에 **최근 포스트 목록**이 자동으로 표시됩니다.
3. 방금 만든 글이 최상단(가장 위)에 보이는지 확인합니다. 

만약 안 보인다면 아래를 다시 점검합니다.

- `_posts` 폴더 안에 있는지
- 파일 이름이 `YYYY-MM-DD-슬러그.md` 형식을 지켰는지
- `date` 값이 미래 날짜가 아닌지
- `published: false` 같은 속성이 들어가 있지 않은지

---

## 6. 최신 글 1개 강조(특집 섹션)하기

홈에서 첫 글을 더 눈에 띄게 보여주고 싶다면,  
Chirpy의 인덱스 템플릿을 약간 수정해 “특집 포스트 1개 + 나머지 목록” 구조로 만들 수 있습니다. 

대략적인 흐름은 다음과 같습니다.

1. `_layouts/home.html` 또는 `index.html` 의 내용을 확인합니다.
2. 루프 부분에서 **첫 번째 포스트만 따로 뽑는 루프**를 추가합니다.

예시 개념(코드 단순화):

```liquid
{% assign posts = site.posts | where_exp: "post", "post.categories contains 'guide'" %}

{%- comment -%} 최신 글 1개 강조 영역 {%- endcomment -%}
{% assign featured = posts | first %}

<section class="featured-post">
  <h2>{{ featured.title }}</h2>
  <p>{{ featured.excerpt }}</p>
  <a href="{{ featured.url | relative_url }}">자세히 보기 →</a>
</section>

{%- comment -%} 나머지 최근 글 목록 {%- endcomment -%}
<ul class="post-list">
  {% for post in posts offset:1%}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      <span class="post-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
    </li>
  {% endfor %}
</ul>
```

이렇게 하면:

- 가장 최근 글 1개는 상단 “특집 섹션”에 크게 노출되고
- 그 아래에는 나머지 최근 글 목록이 간단한 리스트로 이어집니다.

처음에는 기본 홈 레이아웃을 그대로 쓰다가,  
글이 여러 개 쌓이면 이런 방식으로 홈을 커스터마이징해 주면 좋습니다.

---

## 7. 마지막 체크 포인트

첫 글을 작성했는데 홈에 보이지 않을 때는 아래를 순서대로 확인해 보세요.

1. 파일 위치가 `_posts` 가 맞는가  
2. 파일명 형식이 `YYYY-MM-DD-슬러그.md` 인가  
3. front matter 위·아래에 `---` 가 정확히 들어갔는가  
4. `date` 가 올바른 날짜·시간 형식인가  
5. `categories`, `tags` 가 배열 형식으로 적혀 있는가  
6. GitHub Actions 빌드가 성공했는가  
7. 브라우저 캐시/시크릿 모드에서 새로고침해도 동일한가

이 흐름만 익숙해지면,  
이후에 쓰는 모든 글도 자동으로 홈에 잘 노출되기 때문에  
“글을 올렸는데 왜 안 보이지?” 같은 스트레스를 거의 받지 않게 됩니다.
