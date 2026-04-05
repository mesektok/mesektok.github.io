---
layout: post
title: "Chirpy 이미지 경로 정리하는 방법과 대표 이미지·본문 이미지 차이점"
date: 2026-04-06 00:44:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, image, thumbnail, markdown]
description: "Chirpy 블로그에서 이미지 경로를 정리하는 방법과 대표 이미지, 본문 이미지의 차이점을 초보자 기준으로 쉽게 정리했습니다."
image:
  path: /assets/img/posts/chirpy-image-path-guide/cover.jpg
  alt: Chirpy 이미지 경로 정리 가이드
---

Chirpy 블로그를 쓰다 보면  
이미지 경로가 자꾸 헷갈릴 때가 있습니다.

어떤 이미지는 글 맨 위에 넣고,  
어떤 이미지는 본문 중간에 넣고,  
어떤 건 경로가 길고 어떤 건 짧아서 더 혼란스러워지기도 합니다.

그래서 오늘은 **Chirpy 이미지 경로 정리하는 방법**과  
**대표 이미지와 본문 이미지 차이점**을  
초보자 기준으로 한 번에 정리해보겠습니다.

---

## 왜 이미지 경로 정리가 중요할까?

처음에는 이미지만 보이면 된다고 생각할 수 있습니다.

하지만 글 수가 많아지면 아래 문제가 자주 생깁니다.

- 이미지 파일이 여기저기 흩어짐
- 경로를 매번 길게 써야 해서 실수함
- 대표 이미지와 본문 이미지를 헷갈림
- 나중에 수정할 때 어디 있는지 찾기 어려움

즉, 처음부터 이미지 경로 구조를 정리해두면  
블로그 관리가 훨씬 쉬워집니다.

---

## 가장 추천하는 기본 폴더 구조

초보자에게 가장 추천하는 방식은  
이미지를 모두 `assets/img/posts/...` 아래에 모아두는 것입니다.

예를 들면 이런 구조입니다.

```text
_posts/2026-04-06-chirpy-image-path-guide.md
assets/img/posts/chirpy-image-path-guide/cover.jpg
assets/img/posts/chirpy-image-path-guide/example-01.jpg
assets/img/posts/chirpy-image-path-guide/example-02.jpg
```

이 구조의 장점은 분명합니다.

- 글 파일과 이미지 파일이 섞이지 않음
- 글마다 전용 이미지 폴더를 만들 수 있음
- 경로를 찾기 쉬움
- 대표 이미지와 본문 이미지를 함께 정리할 수 있음

Chirpy 관련 안내에서도  
정적 이미지 파일은 `_posts`가 아니라 `assets/img` 아래에 두는 방식이 일반적입니다.

---

## 대표 이미지와 본문 이미지 차이점

이 부분은 초보자가 가장 많이 헷갈리는 부분입니다.

쉽게 말하면 아래처럼 이해하면 됩니다.

### 대표 이미지
글 전체를 대표하는 이미지입니다.

주로 아래 위치에 쓰입니다.

- 메인 글 목록 썸네일
- 포스트 상단 대표 이미지
- SNS 공유용 미리보기 이미지

즉, 대표 이미지는  
**글의 얼굴** 같은 역할을 합니다.

### 본문 이미지
글 안에서 설명을 위해 넣는 이미지입니다.

예를 들면 이런 이미지입니다.

- 화면 캡처
- 사용 방법 설명 이미지
- 예시 사진
- 비교 이미지

즉, 본문 이미지는  
**글의 설명 자료** 역할을 합니다.

정리하면 아래처럼 생각하면 쉽습니다.

- 대표 이미지 = 글을 대표하는 썸네일
- 본문 이미지 = 글 내용을 설명하는 이미지

---

## 대표 이미지는 어떻게 넣을까?

Chirpy에서 대표 이미지는  
포스트 맨 위 front matter에 넣습니다. Chirpy는 `image`를 문자열 또는 `path`와 `alt`를 가진 객체 형식으로 받을 수 있습니다. [web:379][web:391]

가장 추천하는 방식은 아래와 같습니다.

```yaml
***
layout: post
title: "Chirpy 이미지 경로 정리하는 방법"
date: 2026-04-06 00:44:00 +0900
image:
  path: /assets/img/posts/chirpy-image-path-guide/cover.jpg
  alt: Chirpy 이미지 경로 정리 가이드
***
```

여기서 `cover.jpg` 는 대표 이미지입니다.

즉, 글 목록에서 썸네일처럼 보이거나  
포스트 상단에 보여줄 이미지는  
이렇게 front matter에 설정합니다.

---

## 본문 이미지는 어떻게 넣을까?

본문 이미지는 front matter가 아니라  
글 내용 안에서 Markdown으로 직접 넣습니다. Chirpy는 일반 Markdown 이미지 문법과 캡션, 이미지 속성 등을 지원합니다. [web:379][web:442]

예시는 아래와 같습니다.

```markdown


_설정 화면 예시_
```

즉, 설명용 이미지나 캡처 이미지는  
본문에서 직접 불러오면 됩니다.

---

## 경로는 가능하면 절대경로로 쓰기

본문 이미지든 대표 이미지든  
초보자라면 경로를 아래처럼 쓰는 것이 가장 안전합니다.

```text
/assets/img/posts/chirpy-image-path-guide/example-01.jpg
```

즉, 앞에 `/` 가 붙는  
**루트 기준 경로**를 쓰는 방식입니다.

이렇게 하면 경로가 꼬일 가능성이 줄어듭니다.

예를 들어 본문에서는 아래처럼 쓰면 됩니다.

```markdown

```

대표 이미지도 마찬가지입니다.

```yaml
image:
  path: /assets/img/posts/chirpy-image-path-guide/cover.jpg
  alt: Chirpy 대표 이미지 예시
```

초보자라면 처음에는 이 방식 하나만 익혀도 충분합니다.

---

## `media_subpath`를 쓰면 경로를 더 짧게 만들 수 있다

이미지가 많은 글이라면  
매번 긴 경로를 적는 것이 불편할 수 있습니다.

이럴 때 Chirpy에서는 `media_subpath`를 쓸 수 있습니다. Chirpy 문서는 `media_subpath`를 front matter에 두면 현재 글 범위에서 이미지 경로 접두어를 재사용할 수 있다고 설명합니다. [web:379][web:487]

예시는 아래와 같습니다.

```yaml
***
layout: post
title: "Chirpy 이미지 경로 정리하는 방법"
date: 2026-04-06 00:44:00 +0900
media_subpath: /assets/img/posts/chirpy-image-path-guide
image:
  path: cover.jpg
  alt: Chirpy 이미지 경로 정리 가이드
***
```

이렇게 하면 대표 이미지를 짧게 쓸 수 있습니다.

```yaml
image:
  path: cover.jpg
  alt: Chirpy 이미지 경로 정리 가이드
```

본문 이미지도 더 짧게 쓸 수 있습니다.

```markdown

```

즉, `media_subpath`를 쓰면  
공통 경로를 한 번만 써두고,  
뒤에는 파일명만 적어도 되는 구조입니다.

---

## 초보자에게는 어떤 방식이 더 좋을까?

처음 시작하는 분이라면  
사실 `media_subpath` 없이  
전체 경로를 직접 쓰는 방법이 더 안전합니다.

왜냐하면:

- 구조를 이해하기 쉽고
- 문제가 생겼을 때 찾기 쉽고
- 경로가 어떻게 연결되는지 바로 보이기 때문입니다

즉, 처음에는 아래 방식 추천입니다.

```yaml
image:
  path: /assets/img/posts/chirpy-image-path-guide/cover.jpg
  alt: Chirpy 이미지 경로 정리 가이드
```

```markdown

```

그리고 글이 많아지고 이미지가 많아졌을 때  
그다음 단계로 `media_subpath`를 써도 늦지 않습니다.

---

## 이미지 파일 이름은 어떻게 정리하면 좋을까?

이미지 경로를 정리할 때는  
파일 이름 규칙도 같이 정해두면 좋습니다.

추천 방식은 아래와 같습니다.

- `cover.jpg` : 대표 이미지
- `example-01.jpg` : 본문 예시 이미지 1
- `example-02.jpg` : 본문 예시 이미지 2
- `step-01.jpg` : 단계 설명 이미지 1
- `step-02.jpg` : 단계 설명 이미지 2

이렇게 해두면  
나중에 어떤 이미지가 어디에 쓰이는지 바로 알 수 있습니다.

가능하면 아래는 피하는 것이 좋습니다.

- `최종 이미지.jpg`
- `새폴더/스크린샷(1).png`
- `진짜최종최종.png`

한글, 띄어쓰기, 괄호, 특수문자가 많아질수록  
경로 실수 가능성도 커집니다.

---

## 초보자용 가장 추천하는 실제 예시

아래 구조는 가장 무난하고 실수도 적은 방식입니다.

### 파일 구조

```text
_posts/2026-04-06-chirpy-image-path-guide.md
assets/img/posts/chirpy-image-path-guide/cover.jpg
assets/img/posts/chirpy-image-path-guide/example-01.jpg
assets/img/posts/chirpy-image-path-guide/example-02.jpg
```

### front matter

```yaml
***
layout: post
title: "Chirpy 이미지 경로 정리하는 방법과 대표 이미지·본문 이미지 차이점"
date: 2026-04-06 00:44:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, image, thumbnail]
image:
  path: /assets/img/posts/chirpy-image-path-guide/cover.jpg
  alt: Chirpy 이미지 경로 정리 가이드
***
```

### 본문 이미지

```markdown


_본문 설명용 이미지 예시_
```

이렇게 하면  
대표 이미지와 본문 이미지를 한 폴더 안에서 정리하면서도  
역할은 확실하게 구분할 수 있습니다.

---

## 이미지가 안 뜰 때 같이 확인할 것

이미지 경로를 잘 정리해도  
아래 부분에서 문제가 생길 수 있습니다.

- 파일이 실제로 업로드되지 않음
- 경로 앞 `/` 를 빼먹음
- 파일명 대소문자가 다름
- 확장자가 `.jpg` 인데 `.png` 로 적음
- GitHub Actions 빌드가 아직 안 끝남

특히 Chirpy 관련 사례에서는  
정적 이미지가 `_posts`가 아니라 `assets` 아래에 있어야 관리가 쉽고, `image.path` 와 경로 조합 방식에 따라 preview image 동작이 달라질 수 있다고 언급됩니다. [web:426][web:468]

그래서 초보자라면  
일단 **복잡한 조합보다 단순한 절대경로 방식**으로 시작하는 것이 좋습니다.

---

## 마무리

Chirpy 이미지 경로를 정리할 때 가장 중요한 것은  
처음부터 규칙을 하나 정해두는 것입니다.

핵심만 정리하면 아래와 같습니다.

- 이미지는 `assets/img/posts/글이름폴더/` 아래에 정리하기
- 대표 이미지는 front matter의 `image` 로 설정하기
- 본문 이미지는 Markdown으로 직접 넣기
- 초보자는 절대경로 방식으로 시작하기
- 이미지가 많아지면 `media_subpath`를 활용하기

처음에는 조금 낯설 수 있지만,  
한 번 구조를 잡아두면 글을 쓸 때 훨씬 편해집니다.

특히 블로그를 꾸준히 운영할 계획이라면  
이미지 경로 정리는 초반에 꼭 해두는 것이 좋습니다.
