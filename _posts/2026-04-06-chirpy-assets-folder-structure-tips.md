---
layout: post
title: "Chirpy 블로그 assets 폴더 구조 최적화 팁"
date: 2026-04-06 00:45:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, assets, 폴더구조]
description: "Chirpy + Jekyll 블로그에서 assets 폴더를 깔끔하게 정리하는 방법과 추천 폴더 구조를 초보자 기준으로 정리했습니다."
image:
  path: /assets/img/posts/chirpy-assets-structure/cover.jpg
  alt: Chirpy assets 폴더 구조 예시
---

Jekyll + Chirpy로 블로그를 만들다 보면  
점점 이미지, CSS, JS 파일이 늘어나면서 `assets` 폴더가 금방 지저분해집니다.

처음에는 대충 넣어도 상관없지만,  
나중에 글이 많아지면 **어떤 파일이 어디 쓰이는지** 찾는 것만으로도 시간이 꽤 걸립니다.

그래서 오늘은 **Chirpy 블로그에서 assets 폴더 구조를 최적화하는 팁**을  
초보자 기준으로 정리해 보았습니다.

---

## 1. 기본 원칙: 유형별 + 용도별로 나누기

Jekyll 공식 문서는 정적 자산을 `assets/css`, `assets/js`, `assets/images`처럼  
**파일 유형별 폴더**로 나누는 패턴을 소개합니다. [web:496][web:491]

Chirpy 커뮤니티에서도 정적 이미지를 `_posts`가 아니라  
`assets/img` 아래에 두는 방식을 많이 사용합니다. [web:426]

여기에 **“용도별”** 개념을 한 번 더 얹으면 관리가 훨씬 쉽습니다.

- 유형별: CSS / JS / IMG / FONT …
- 용도별: 포스트 이미지 / 사이트 공용 이미지 / 아이콘 …

즉, “무슨 종류의 파일인지”와 “어디에 쓰이는지”를 동시에 기준으로 나누는 것이 핵심입니다.

---

## 2. 추천 assets 폴더 구조

Chirpy 블로그에서 사용하기 좋은 예시 구조는 아래와 같습니다.

```text
assets/
├─ css/             # 추가 스타일시트 (custom.css 등)
├─ js/              # 커스텀 스크립트
├─ img/
│  ├─ posts/        # 포스트 전용 이미지
│  │  ├─ chirpy-assets-structure/
│  │  │  ├─ cover.jpg
│  │  │  ├─ example-01.jpg
│  │  │  └─ example-02.jpg
│  │  ├─ adsense-guide/
│  │  │  ├─ cover.jpg
│  │  │  └─ step-01.jpg
│  ├─ site/         # 사이트 공용 이미지 (로고, 프로필, 기본 OG 이미지)
│  │  ├─ logo.png
│  │  ├─ profile.png
│  │  └─ og-default.jpg
│  └─ icons/        # 작은 아이콘, UI 이미지
└─ lib/             # Chirpy가 제공하는 라이브러리 자산 (starter 템플릿 기본 구조)
```

Chirpy starter 저장소에서도 `assets/lib`를 별도로 두고  
라이브러리 리소스를 관리하는 패턴을 사용합니다. [web:507]

여기에 `img/posts`, `img/site`, `img/icons`를 추가로 나누면  
내가 추가하는 파일까지 깔끔하게 분리할 수 있습니다.

---

## 3. 포스트 이미지는 `img/posts/글폴더`로 묶기

포스트에서 사용하는 이미지는  
**글마다 전용 폴더를 만들어서 관리**하는 것이 가장 깔끔합니다.

예를 들어 이 글의 포스트 파일이 아래라면:

```text
_posts/2026-04-06-chirpy-assets-structure.md
```

이미지 폴더는 이렇게 맞춰 줍니다.

```text
assets/img/posts/chirpy-assets-structure/
  ├─ cover.jpg
  ├─ example-01.jpg
  └─ example-02.jpg
```

이렇게 하면:

- 어떤 이미지가 어떤 글에서 쓰이는지 바로 알 수 있고
- 글을 삭제할 때 관련 이미지만 같이 정리하기도 쉽고
- 대표 이미지와 본문 이미지를 한 폴더에서 관리할 수 있습니다.

Jekyll 사용자들 사이에서도  
포스트마다 해당 자산을 모아 두는 구조가 오래전부터 추천 패턴으로 쓰이고 있습니다. [web:488][web:500]

---

## 4. 대표 이미지 vs 본문 이미지 경로 정리

### 4-1. 대표 이미지(썸네일)

대표 이미지는 front matter의 `image` 속성에 넣습니다.  
Chirpy 문서는 `image`를 문자열 또는 `image.path` + `alt` 형식으로 사용하는 방식을 안내합니다. [web:379][web:391]

```yaml
***
layout: post
title: "Chirpy 블로그 assets 폴더 구조 최적화 팁"
date: 2026-04-06 00:45:00 +0900
image:
  path: /assets/img/posts/chirpy-assets-structure/cover.jpg
  alt: Chirpy assets 폴더 구조 예시
***
```

여기서 `cover.jpg`는 글을 대표하는 썸네일 이미지입니다.

- 홈 화면의 글 목록 썸네일
- 포스트 상단 대표 이미지
- SNS 공유 미리보기 이미지

등에 활용됩니다. [web:379][web:486]

### 4-2. 본문 이미지

본문 이미지는 Markdown 문법으로 글 안에 직접 넣습니다. [web:379]

```markdown


_포스트 전용 이미지 폴더 구조 예시_
```

여기서도 `/assets/...` 처럼 **앞에 슬래시를 붙인 절대경로**를 쓰면  
경로가 꼬일 위험이 줄어듭니다. [web:500][web:499]

---

## 5. media_subpath로 경로를 더 단순하게 만들기

이미지가 많은 글이라면  
매번 긴 경로를 적는 것이 번거로울 수 있습니다.

이때 Chirpy가 제공하는 `media_subpath`를 활용하면  
공통 경로를 한 번만 적고,  
본문에서는 파일명만 써도 되게 만들 수 있습니다. [web:379]

```yaml
***
layout: post
title: "Chirpy 블로그 assets 폴더 구조 최적화 팁"
date: 2026-04-06 00:45:00 +0900
media_subpath: /assets/img/posts/chirpy-assets-structure
image:
  path: cover.jpg
  alt: Chirpy assets 폴더 구조 예시
***
```

이렇게 설정하면 본문에서는 아래처럼 쓸 수 있습니다.

```markdown


```

Chirpy 공식 문서에서도 `media_subpath`를 이용해  
포스트별 이미지 경로를 단순하게 관리하는 방법을 소개합니다. [web:379]

초보자는 처음에는 절대경로 방식으로 쓰다가,  
이미지가 많아졌을 때 `media_subpath`로 점진적으로 옮겨도 충분합니다.

---

## 6. 공용 이미지는 `img/site`로 분리하기

포스트와 상관없이 블로그 전반에서 쓰이는 이미지는  
`assets/img/site/` 같은 별도 폴더에 모아두는 것이 좋습니다.

예를 들면:

```text
assets/img/site/
  ├─ logo.png         # 사이트 로고
  ├─ profile.png      # 프로필 사진
  └─ og-default.jpg   # 사이트 기본 OG 이미지
```

Chirpy 설정이나 `_includes` 템플릿에서  
이 경로를 그대로 쓰면 됩니다. 예를 들어 사이드바 아바타 이미지를 바꿀 때  
`/assets/img/profile/paul.png` 같은 경로를 쓰는 사례도 있습니다. [web:506]

공용 이미지를 포스트 폴더와 분리해 두면:

- 테마 변경 시에도 경로를 한 번에 관리하기 쉽고
- 사이트 전반 디자인을 바꿀 때 수정 범위를 줄일 수 있습니다.

---

## 7. 파일명 규칙으로 유지보수성 높이기

폴더 구조만큼 중요한 것이 **파일명 규칙**입니다.

추천 규칙은 아래와 같습니다.

- 모두 소문자, 영어, 숫자만 사용
- 단어 구분은 `-`(하이픈) 사용 → `chirpy-assets-structure-01.jpg`
- 대표 이미지는 `cover.jpg`로 통일
- 캡처 순서는 `step-01.jpg`, `step-02.jpg`
- 예시 이미지는 `example-01.jpg`, `example-02.jpg`

이렇게 해두면:

- 나중에 파일만 봐도 용도를 알 수 있고
- 대소문자 차이로 인한 404 문제를 줄일 수 있으며
- 경로를 직접 타이핑할 때도 실수가 줄어듭니다. [web:488][web:500]

---

## 8. 자주 하는 실수와 해결 팁

assets 폴더를 쓰다 보면 아래 같은 실수가 자주 나옵니다.

1. **이미지를 `_posts` 안에 섞어 넣는 경우**  
   → 정적 파일은 `assets/img/posts/...` 아래로 옮기고,  
   경로도 `/assets/...` 로 통일하는 편이 좋습니다. [web:426][web:488]

2. **경로 앞의 `/` 를 빼먹는 경우**  
   → 상대경로가 되면서 GitHub Pages에서 실제 경로와 달라질 수 있습니다.  
   가능하면 항상 `/assets/...` 형태를 사용합니다. [web:499][web:500]

3. **대표 이미지와 본문 이미지를 같은 설정으로 헷갈리는 경우**  
   → 대표 이미지는 `image.path`, 본문 이미지는 Markdown으로 분리해서 생각하면 덜 헷갈립니다. [web:379][web:452]

4. **폴더 이름·파일 이름에 공백, 한글, 괄호를 많이 쓰는 경우**  
   → URL 인코딩 문제와 경로 오류의 원인이 될 수 있습니다.  
   가능하면 `kebab-case` 영어 이름만 사용하는 것이 좋습니다. [web:488]

---

## 9. 한 번 정해두면 평생 편해지는 구조

지금까지 정리한 내용을 한 줄로 요약하면 이렇습니다.

> **“assets는 유형별 + 용도별로, 포스트 이미지는 글 폴더 단위로 묶어라.”**

다시 정리하면:

- `assets/css`, `assets/js`, `assets/img` 로 기본 분리하기 [web:496]
- `assets/img/posts/글폴더/` 구조로 포스트 이미지를 관리하기 [web:426][web:488]
- 대표 이미지는 `image.path`로, 본문 이미지는 Markdown으로 넣기 [web:379]
- 공용 이미지는 `assets/img/site/` 에 모으기 [web:506]
- 필요하면 `media_subpath`로 경로를 짧게 관리하기 [web:379]

블로그를 처음 만들 때는 자잘한 규칙들이 번거롭게 느껴질 수 있지만,  
글이 쌓일수록 **처음에 잘 설계한 assets 구조가 진짜 자산**이 됩니다.

앞으로 Chirpy 블로그를 꾸준히 운영할 계획이라면  
지금 한 번만 구조를 정리해 두는 것을 강력히 추천합니다.
