---
layout: post
title: "Chirpy 포스트 대표 이미지(썸네일) 넣는 방법"
date: 2026-04-06 00:38:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, image, thumbnail]
description: "Chirpy 블로그에서 포스트 대표 이미지와 썸네일을 넣는 방법을 초보자 기준으로 쉽게 정리했습니다."
image:
  path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 설정 예시
---

Chirpy 블로그를 쓰다 보면  
글 목록에 썸네일처럼 보이는 대표 이미지를 넣고 싶을 때가 있습니다.

처음에는 “이미지를 어디에 넣어야 하지?”,  
“front matter에는 어떻게 적어야 하지?”  
이 부분이 꽤 헷갈릴 수 있습니다.

그래서 오늘은 **Chirpy에서 포스트 대표 이미지(썸네일)를 넣는 방법**을  
초보자 기준으로 아주 쉽게 정리해보겠습니다.

---

## Chirpy 대표 이미지는 어디에 쓰일까?

Chirpy에서 대표 이미지는 단순히 글 맨 위에만 보이는 것이 아닙니다.

상황에 따라 아래처럼 활용될 수 있습니다.

- 홈 화면 글 목록 썸네일
- 포스트 상단 대표 이미지
- SNS나 메신저 공유용 미리보기 이미지

즉, 대표 이미지는  
글의 첫인상을 보여주는 중요한 요소라고 생각하면 됩니다.

---

## 가장 쉬운 방법부터 정리하면

초보자에게 가장 쉬운 방법은 아래 구조로 정리하는 것입니다.

- 글 파일은 `_posts` 폴더에 넣기
- 대표 이미지는 `assets/img/posts/...` 에 넣기
- 포스트 맨 위 front matter에 `image` 설정 추가하기

예를 들면 파일 구조는 이렇게 두면 됩니다.

```text
_posts/2026-04-06-chirpy-thumbnail-guide.md
assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

중요한 점은  
이미지 파일을 `_posts` 폴더 안에 넣는 것이 아니라,  
보통 `assets/img` 아래에 따로 두는 방식이 더 일반적이라는 점입니다.

이렇게 해야 나중에 이미지 관리도 훨씬 편합니다.

---

## 1. 대표 이미지 파일은 어디에 저장하면 좋을까?

가장 추천하는 위치는 아래와 같습니다.

```text
assets/img/posts/글이름폴더/cover.jpg
```

예를 들어 이 글이라면 이렇게 만들 수 있습니다.

```text
assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

이 구조의 장점은 아주 분명합니다.

- 글별로 이미지가 섞이지 않음
- 나중에 이미지 찾기가 쉬움
- 본문 이미지와 대표 이미지를 같이 관리하기 좋음

파일명은 가능하면 아래처럼 단순하게 만드는 것이 좋습니다.

- `cover.jpg`
- `thumbnail.jpg`
- `featured-image.jpg`

가능하면 한글, 공백, 특수문자, 괄호는 줄이는 것이 좋습니다.

---

## 2. front matter에 대표 이미지 넣는 법

Chirpy에서는 포스트 맨 위 front matter에  
대표 이미지 정보를 적어주면 됩니다.

가장 많이 쓰는 기본형은 아래와 같습니다.

```yaml
***
title: "Chirpy 포스트 대표 이미지 넣는 방법"
date: 2026-04-06 00:33:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, image, thumbnail]
image:
  path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 예시
***
```

이 코드에서 중요한 부분은 `image:` 아래의 두 항목입니다.

### `path`
대표 이미지의 파일 경로입니다.

```yaml
path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

여기서 중요한 점은  
경로 앞에 `/` 를 붙여 절대경로로 쓰는 것입니다.

### `alt`
이미지 설명 문구입니다.

```yaml
alt: Chirpy 포스트 대표 이미지 예시
```

이 값은 접근성에도 도움이 되고,  
이미지가 보이지 않을 때 어떤 이미지인지 설명하는 역할도 합니다.

초보자라도 가능하면 `alt`를 같이 적어두는 습관을 들이면 좋습니다.

---

## 3. 더 간단한 축약형도 가능하다

Chirpy에서는 더 짧게 아래처럼 쓸 수도 있습니다.

```yaml
***
title: "Chirpy 포스트 대표 이미지 넣는 방법"
date: 2026-04-06 00:33:00 +0900
image: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
***
```

이 방법은 간단해서 편하지만,  
`alt` 문구를 함께 넣기 어렵기 때문에  
초보자라도 가능하면 아래처럼 **객체형 방식**을 추천합니다.

```yaml
image:
  path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 예시
```

즉, 짧게 쓰는 것도 가능하지만  
조금 더 제대로 쓰려면 `path + alt` 조합이 더 좋습니다.

---

## 4. 대표 이미지 권장 크기

Chirpy 문서는 대표 이미지로  
**1200 x 630 해상도**를 권장합니다.

이 비율은 대략 **1.91:1** 입니다. [web:379][web:465]

왜 이 크기가 좋을까요?

- 포스트 상단에서 보기 좋고
- 목록 썸네일로도 잘 맞고
- SNS 공유 이미지 비율에도 무난하기 때문입니다

즉, 대표 이미지는 가능하면  
**가로형 썸네일 이미지**로 준비하는 것이 가장 안전합니다.

추천 기준을 정리하면 아래와 같습니다.

- 1200 x 630
- JPG 또는 WebP
- 가로형 이미지
- 텍스트가 가장자리까지 너무 붙지 않게 제작

특히 이미지 안에 글자를 넣을 경우  
가장자리와 너무 붙으면 잘려 보일 수 있으니  
여백을 조금 두는 것이 좋습니다.

---

## 5. `media_subpath`를 쓰면 더 편할 때

이미지가 한 장만 있으면 전체 경로를 직접 적어도 충분합니다.

하지만 한 글에서  
대표 이미지 + 본문 이미지 여러 장을 함께 쓸 예정이라면  
`media_subpath`를 같이 쓰는 방법도 꽤 편합니다. [web:379]

예시는 아래와 같습니다.

```yaml
***
title: "Chirpy 포스트 대표 이미지 넣는 방법"
date: 2026-04-06 00:33:00 +0900
media_subpath: /assets/img/posts/chirpy-thumbnail-guide
image:
  path: cover.jpg
  alt: Chirpy 대표 이미지 예시
***
```

이 방식의 장점은  
이미지 경로를 매번 길게 쓰지 않아도 된다는 점입니다.

즉, 같은 글 안에서 이미지가 여러 장일수록  
`media_subpath`를 써두면 관리가 훨씬 편해집니다.

---

## 6. 대표 이미지가 안 뜰 때 먼저 확인할 것

이미지를 넣었는데도 대표 이미지가 안 보이면  
아래 항목부터 확인해보는 것이 좋습니다.

### 이미지 파일이 실제로 있는지
가장 기본적인 확인입니다.

예를 들어 front matter에서 아래 경로를 썼다면

```yaml
path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

실제 저장소에도 진짜 그 파일이 있어야 합니다.

```text
assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

글만 올리고 이미지 파일을 빼먹는 경우가 생각보다 많습니다.

### `path` 앞에 `/` 를 넣었는지
아래처럼 쓰는 것이 더 안전합니다.

```yaml
path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

앞의 `/` 를 빼먹으면 경로가 꼬일 수 있습니다.

### 파일명 대소문자가 정확한지
이 부분도 자주 놓칩니다.

예를 들면 아래는 서로 다릅니다.

- `cover.jpg`
- `Cover.jpg`

GitHub Pages 환경에서는 이런 차이 때문에 이미지가 안 뜰 수 있습니다.

### 이미지 비율이 너무 이상하지 않은지
너무 세로로 길거나, 너무 정사각형이면  
썸네일처럼 보일 때 잘려 보이거나 어색할 수 있습니다.

그래서 대표 이미지는 가능하면  
처음부터 1200 x 630 비율로 준비하는 것이 좋습니다.

### 슬래시가 중복되지 않았는지
예를 들어 경로를 합치는 과정에서 아래처럼 잘못 쓰는 경우도 있습니다.

```yaml
path: //assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

이런 작은 실수도 이미지가 정상적으로 안 뜨는 원인이 될 수 있습니다.

실제로 Chirpy 관련 이슈에서도  
preview image 경로 문제 사례가 있었습니다. [web:443]

---

## 7. 초보자에게 가장 추천하는 실제 예시

처음 시작하는 분이라면  
아래 예시를 거의 그대로 써도 됩니다.

```yaml
***
layout: post
title: "Chirpy 포스트 대표 이미지(썸네일) 넣는 방법"
date: 2026-04-06 00:38:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, image, thumbnail]
description: "Chirpy 블로그에서 포스트 대표 이미지와 썸네일을 넣는 방법을 초보자 기준으로 쉽게 정리했습니다."
image:
  path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 설정 예시
***
```

그리고 실제 파일은 아래 위치에 두면 됩니다.

```text
assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

이 방식이 가장 단순하고,  
나중에 봐도 어디를 수정해야 할지 바로 이해하기 쉽습니다.

---

## 8. 대표 이미지와 본문 이미지는 다르다

초보자 입장에서는  
대표 이미지와 본문 이미지가 헷갈릴 수 있습니다.

### 대표 이미지
글 전체를 대표하는 이미지입니다.

예를 들면:

- 글 목록 썸네일
- 글 상단 메인 이미지
- 공유용 미리보기 이미지

### 본문 이미지
글 안에서 설명을 위해 넣는 이미지입니다.

예를 들면:

- 설정 화면 캡처
- 예시 사진
- 사용 방법 설명용 이미지

즉, 쉽게 말하면

- 대표 이미지는 글의 얼굴
- 본문 이미지는 글의 설명 자료

라고 생각하면 이해가 쉽습니다.

---

## 9. 마지막 체크리스트

대표 이미지를 넣은 뒤에는 아래만 확인해 보세요.

- 이미지 파일이 실제로 업로드되어 있는가
- front matter 문법이 맞는가
- `image.path` 경로가 정확한가
- 경로 앞에 `/` 가 붙었는가
- 파일명 대소문자가 정확한가
- GitHub Actions 빌드가 성공했는가
- 블로그에서 실제로 썸네일이 보이는가

이 정도만 체크해도 대부분의 문제는 해결됩니다.

---

## 마무리

Chirpy에서 포스트 대표 이미지(썸네일)를 넣는 방법은  
처음에는 복잡해 보여도 사실 구조만 알면 어렵지 않습니다.

핵심은 딱 3가지입니다.

- 대표 이미지는 `assets/img/posts/...` 아래에 저장하기
- 글 맨 위 front matter에 `image.path` 와 `alt` 넣기
- 가능하면 1200 x 630 가로형 이미지로 준비하기

처음부터 이미지 폴더 구조를 잘 정리해 두면  
이후에 글을 쓸 때 훨씬 편해집니다.

특히 블로그를 꾸준히 운영할 계획이라면  
대표 이미지 관리 방식도 처음부터 습관처럼 잡아두는 것이 좋습니다.****
