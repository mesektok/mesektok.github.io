---
layout: post
title: "Chirpy 포스트 대표 이미지(썸네일) 넣는 방법"
date: 2026-04-06 00:36:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, thumbnail, featured-image]
description: "Chirpy 블로그 글에 대표 이미지(썸네일)를 넣는 방법을 초보자 기준으로 자세히 정리했습니다."
image:
  path: /assets/img/posts/chirpy-featured-image-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 설정 예시
---

블로그 글을 쓰다 보면  
글 목록에서 보이는 썸네일 이미지나,  
글 상단에 표시되는 대표 이미지를 넣고 싶을 때가 있습니다.

특히 Chirpy 테마는 대표 이미지를 잘 설정해두면  
홈 화면, 글 상단, 공유 미리보기까지 훨씬 깔끔하게 보일 수 있어서  
처음부터 알아두면 정말 편합니다.

오늘은 **Chirpy 포스트 대표 이미지(썸네일) 넣는 방법**을  
완전 초보자 기준으로 쉽게 정리해보겠습니다.

---

## 대표 이미지는 어디에 쓰일까?

Chirpy에서 대표 이미지는 단순히 글 맨 위에만 보이는 것이 아닙니다.

설정에 따라 아래처럼 활용될 수 있습니다.

- 블로그 메인 글 목록 썸네일
- 포스트 상단 대표 이미지
- SNS나 메신저 공유 미리보기 이미지

즉, 대표 이미지는  
글의 첫인상을 결정하는 중요한 요소라고 생각하면 됩니다.

---

## 먼저 가장 쉬운 방법부터

초보자라면 대표 이미지는 아래 방식으로 관리하는 것이 가장 편합니다.

- 글 파일은 `_posts` 폴더에 넣기
- 대표 이미지 파일은 `assets/img/posts/...` 아래에 넣기
- 글 맨 위 front matter에 이미지 경로 적기

예를 들면 이런 구조입니다.

```text
_posts/2026-04-06-chirpy-featured-image-beginner-guide.md
assets/img/posts/chirpy-featured-image-guide/cover.jpg
```

Chirpy 관련 안내에서는 정적 파일을 `_posts` 안이 아니라  
보통 `assets` 폴더 아래에 두는 방식을 사용합니다.

그래서 초보자도 이 구조로 시작하는 것이 가장 헷갈리지 않습니다.

---

## 1. 대표 이미지 파일은 어디에 넣을까?

가장 추천하는 위치는 아래처럼 만드는 것입니다.

```text
assets/img/posts/글이름폴더/cover.jpg
```

예를 들어 이 글이라면 다음처럼 만들 수 있습니다.

```text
assets/img/posts/chirpy-featured-image-guide/cover.jpg
```

이렇게 해두면 나중에 다른 글 이미지와도 섞이지 않아서 관리하기 편합니다.

폴더명과 파일명은 가능하면 아래처럼 쓰는 것이 좋습니다.

- 영문 소문자
- 하이픈(-) 사용
- 공백, 한글, 괄호 최소화

예를 들어 이런 이름이 좋습니다.

```text
cover.jpg
featured-image.jpg
chirpy-thumbnail-guide.jpg
```

---

## 2. front matter에 대표 이미지 넣기

Chirpy에서 대표 이미지를 넣는 핵심은  
**글 맨 위 front matter의 `image` 설정**입니다.

가장 추천하는 기본형은 아래와 같습니다.

```yaml
***
layout: post
title: "Chirpy 포스트 대표 이미지 넣는 방법"
date: 2026-04-06 00:36:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, thumbnail]
image:
  path: /assets/img/posts/chirpy-featured-image-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 예시
***
```

여기서 중요한 부분은 두 가지입니다.

### `path`
대표 이미지 파일의 경로입니다.

```yaml
path: /assets/img/posts/chirpy-featured-image-guide/cover.jpg
```

경로 앞에 `/` 를 붙여서 절대경로 형태로 쓰는 것이 안전합니다.

### `alt`
이미지가 보이지 않을 때 대신 보여줄 설명 문구입니다.

```yaml
alt: Chirpy 포스트 대표 이미지 예시
```

이 값은 접근성 측면에서도 도움이 되고,  
이미지 의미를 정리하는 데도 좋습니다.

---

## 3. 더 간단하게 쓰는 방법도 있다

Chirpy에서는 아래처럼 더 짧게 쓸 수도 있습니다.

```yaml
***
layout: post
title: "Chirpy 포스트 대표 이미지 넣는 방법"
date: 2026-04-06 00:36:00 +0900
image: /assets/img/posts/chirpy-featured-image-guide/cover.jpg
***
```

이 방법은 간단해서 편하지만,  
`alt` 문구를 같이 넣기 어렵기 때문에  
초보자라도 가능하면 **객체형 방식**을 추천합니다.

즉, 아래처럼 쓰는 쪽이 더 좋습니다.

```yaml
image:
  path: /assets/img/posts/chirpy-featured-image-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 예시
```

---

## 4. 대표 이미지 권장 크기

Chirpy 문서에서는 대표 이미지로  
**1200 x 630 해상도**를 권장합니다.

이 크기는 비율로 보면 약 **1.91:1** 입니다.

왜 이 크기가 좋을까요?

- 글 목록 썸네일에서 보기 좋고
- 포스트 상단 이미지로도 무난하고
- SNS 공유 이미지 비율과도 잘 맞기 때문입니다

만약 비율이 너무 다르면  
이미지가 자동으로 확대되거나 일부가 잘려 보일 수 있습니다.

그래서 대표 이미지는 가능하면  
처음부터 **가로형 1200 x 630** 으로 준비하는 것이 가장 편합니다. [web:379][web:457]

---

## 5. 대표 이미지가 글 목록 썸네일처럼 안 보일 때

초보자 입장에서는  
“나는 대표 이미지를 넣었는데 왜 안 보이지?”  
싶을 때가 있습니다.

이럴 때는 아래를 먼저 확인해 보세요.

### 이미지 경로가 맞는지
가장 흔한 문제입니다.

예를 들어 실제 파일이 아래 위치에 있다면

```text
assets/img/posts/chirpy-featured-image-guide/cover.jpg
```

front matter에는 아래처럼 써야 합니다.

```yaml
image:
  path: /assets/img/posts/chirpy-featured-image-guide/cover.jpg
```

### 파일명이 실제와 정확히 같은지
대소문자까지 정확해야 합니다.

예를 들면 아래는 서로 다릅니다.

- `cover.jpg`
- `Cover.jpg`

GitHub Pages에서는 이런 차이 때문에 이미지가 안 뜰 수 있습니다.

### 이미지 파일이 아직 업로드되지 않았는지
글만 먼저 커밋하고, 이미지 파일은 빠뜨리는 경우도 많습니다.

이 경우 경로가 맞아 보여도 실제로는 이미지가 없습니다.

### 빌드가 아직 안 끝났는지
Chirpy + GitHub Pages는 글을 수정한 뒤  
GitHub Actions에서 다시 빌드한 후 반영됩니다.

즉, 조금 기다렸다가 새로고침해야 보일 수도 있습니다.

---

## 6. `media_subpath`를 쓰면 더 편할 때도 있다

이미지가 한 장만 있으면 그냥 전체 경로를 직접 쓰면 됩니다.

하지만 한 글에서 이미지를 여러 장 쓸 예정이라면  
`media_subpath`를 설정해두면 훨씬 편합니다.

예시는 아래와 같습니다.

```yaml
***
layout: post
title: "Chirpy 포스트 대표 이미지 넣는 방법"
date: 2026-04-06 00:36:00 +0900
media_subpath: /assets/img/posts/chirpy-featured-image-guide
image:
  path: cover.jpg
  alt: Chirpy 대표 이미지 예시
***
```

이렇게 해두면 대표 이미지뿐 아니라  
본문에 들어가는 이미지도 같은 폴더 기준으로 관리하기가 쉬워집니다.

즉, 같은 글에서 이미지가 여러 장일 경우에는  
`media_subpath`가 꽤 편리한 기능입니다.

---

## 7. 초보자에게 가장 추천하는 실제 예시

처음 시작하는 분이라면  
아래 예시를 거의 그대로 써도 괜찮습니다.

```yaml
***
layout: post
title: "Chirpy 포스트 대표 이미지(썸네일) 넣는 방법"
date: 2026-04-06 00:36:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, thumbnail]
description: "Chirpy 글에 대표 이미지와 썸네일을 넣는 가장 쉬운 방법"
image:
  path: /assets/img/posts/chirpy-featured-image-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 예시
***
```

그리고 실제 파일은 아래 위치에 두면 됩니다.

```text
assets/img/posts/chirpy-featured-image-guide/cover.jpg
```

이 방식이 가장 단순하고,  
나중에 봐도 구조를 이해하기 쉽습니다.

---

## 8. 본문 이미지와 대표 이미지는 어떻게 다를까?

이 부분도 처음에는 많이 헷갈립니다.

### 대표 이미지
글 전체를 대표하는 이미지입니다.

- 글 목록 썸네일
- 포스트 상단 이미지
- 공유용 미리보기

등에 활용될 수 있습니다.

### 본문 이미지
글 안쪽 설명용 이미지입니다.

예를 들면 사용법 설명 화면, 캡처 이미지, 예시 사진 같은 것입니다.

즉,

- 대표 이미지는 글의 얼굴
- 본문 이미지는 글의 설명 자료

라고 생각하면 이해가 쉽습니다.

---

## 9. 대표 이미지가 안 보일 때 체크리스트

마지막으로 초보자용 체크리스트도 정리해보겠습니다.

- 이미지 파일이 실제로 업로드되어 있는가
- 파일 경로가 정확한가
- 경로 앞에 `/`를 붙였는가
- 파일명 대소문자가 같은가
- front matter 문법이 맞는가
- GitHub Actions 빌드가 성공했는가
- 이미지 비율이 너무 이상해서 잘려 보이는 것은 아닌가

이 정도만 점검해도 대부분의 문제는 해결됩니다.

---

## 마무리

Chirpy에서 대표 이미지 넣는 방법은 생각보다 어렵지 않습니다.

핵심만 정리하면 아래 3가지입니다.

- 대표 이미지는 `assets/img/posts/...` 아래에 저장하기
- 글 맨 위 front matter에 `image.path` 와 `alt` 넣기
- 가능하면 1200 x 630 가로형 이미지로 준비하기

처음에는 파일 위치와 경로 때문에 조금 헷갈릴 수 있지만,  
한 번만 구조를 잡아두면 이후에는 훨씬 편하게 글을 작성할 수 있습니다.

특히 블로그를 꾸준히 운영할 생각이라면  
대표 이미지 구조를 처음부터 정리해두는 것이 정말 중요합니다.
