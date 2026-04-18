---
layout: post
title: "Jekyll Chirpy 테마에서 카테고리 관리하는 방법"
date: 2026-04-10
categories: [도전기, 도구]
tags: [jekyll, chirpy, 카테고리, github-pages, 블로그운영]
description: "GitHub Pages Jekyll Chirpy 테마에서 카테고리를 만들고 관리하는 방법을 단계별로 정리합니다."
---

블로그 글이 쌓이기 시작하면 자연스럽게 이런 고민이 생깁니다.

**"글을 카테고리별로 묶어서 보여주려면 어떻게 하지?"**

Chirpy 테마 기준으로 카테고리 관리 방법을 정리합니다.

---

## 카테고리 지정 방법

각 포스팅 파일 상단 **Front Matter** 에 작성합니다.

```yaml
---
layout: post
title: "Notion으로 글감 관리하는 방법"
date: 2026-04-10
categories: [도전기, 도구]
tags: [노션, 블로그, 초보]
---
```

`categories: [도전기, 도구]` 처럼 쓰면 이 글은 **두 카테고리에 동시에** 속합니다.

---

## Chirpy 테마는 카테고리 자동 지원

`_config.yml` 맨 아래를 보면 이미 설정되어 있습니다.

```yaml
jekyll-archives:
  enabled: [categories, tags]
  layouts:
    category: category
    tag: tag
  permalinks:
    tag: /tags/:name/
    category: /categories/:name/
```

포스팅에 `categories: [도전기]` 만 쓰면  
**별도 파일 없이도 카테고리 페이지가 자동 생성됩니다.**

자동 생성되는 주소:
```
mesektok.com/categories/도전기/
mesektok.com/categories/도구/
```

---

## 카테고리 페이지 파일을 직접 만드는 방법

자동 생성으로 충분하지만, **카테고리 설명을 추가하고 싶다면** 직접 파일을 만듭니다.  
직접 만들면 SEO에도 유리합니다.

### 폴더와 파일 동시 생성하는 법

GitHub에서는 폴더를 단독으로 만들 수 없습니다.  
파일 이름에 `/` 를 입력하는 순간 폴더가 자동으로 생성됩니다.

```
① 저장소 루트에서
   "Add file" → "Create new file" 클릭

② 파일명 입력칸에 입력
   categories/도전기.md
   ↑ / 를 치는 순간 폴더 자동 생성
```

### 파일 내용

**`categories/도전기.md`**

```yaml
---
layout: category
title: "도전기"
category: 도전기
permalink: /categories/도전기/
---
```

**`categories/도구.md`**

```yaml
---
layout: category
title: "도구"
category: 도구
permalink: /categories/도구/
---
```

---

## 이 블로그의 카테고리 구조

| 카테고리 | 내용 |
|---|---|
| `도전기` | 애드센스 여정, 시행착오 전부 |
| `도구` | Notion, Git, AI 사용법 |
| `블로그운영` | SEO, 글쓰기 루틴, 수익화 |
| `일상` | 은퇴 후 디지털 라이프 |

---

## 정리

- 포스팅 Front Matter 에 `categories:` 만 써주면 됩니다
- Chirpy 테마는 카테고리 자동 지원 — 별도 설정 불필요
- 카테고리 설명을 추가하고 싶다면 `categories/` 폴더에 파일 직접 생성
- GitHub 웹에서 파일명에 `/` 입력하면 폴더 자동 생성

다음 글에서는 태그 관리 방법과 사이드바 메뉴 구성을 정리합니다.

---

*📌 [도전기 시리즈 전체 보기](/categories/)*
