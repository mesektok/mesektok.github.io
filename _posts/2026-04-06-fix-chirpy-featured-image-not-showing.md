---
layout: post
title: "Chirpy 대표 이미지가 안 뜰 때 해결 방법"
date: 2026-04-06 00:40:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, image, thumbnail, error]
description: "Chirpy 블로그에서 포스트 대표 이미지가 안 보일 때 확인해야 할 원인과 해결 방법을 초보자 기준으로 정리했습니다."
image:
  path: /assets/img/posts/fix-chirpy-featured-image-not-showing/cover.jpg
  alt: Chirpy 대표 이미지 오류 해결 가이드
---

Chirpy 블로그에서 대표 이미지를 넣었는데  
이상하게 썸네일이 안 보이거나,  
포스트 상단 이미지가 안 뜨는 경우가 있습니다.

처음에는 “내가 뭘 잘못했지?” 싶어서 당황할 수 있지만,  
대부분은 몇 가지 자주 나오는 원인만 확인하면 해결됩니다.

오늘은 **Chirpy 대표 이미지가 안 뜰 때 해결하는 방법**을  
초보자 기준으로 쉽게 정리해보겠습니다.

---

## 대표 이미지가 안 뜨는 가장 흔한 이유

Chirpy에서 대표 이미지가 안 보일 때는 보통 아래 원인 중 하나인 경우가 많습니다.

- 이미지 경로를 잘못 적음
- 파일이 실제 위치에 없음
- 파일명 대소문자가 다름
- 경로에 슬래시(`/`)가 중복됨
- `img_path` 와 `image.path` 조합이 꼬임
- GitHub Actions 빌드가 아직 끝나지 않음

즉, 이미지 자체 문제라기보다  
**경로와 설정 문제**인 경우가 대부분입니다. 

---

## 1. 이미지 파일이 실제로 있는지 먼저 확인하기

가장 먼저 할 일은  
정말 그 이미지 파일이 저장소 안에 있는지 확인하는 것입니다.

예를 들어 front matter에 아래처럼 적었다면

```yaml
image:
  path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 예시
```

저장소에도 실제로 아래 파일이 있어야 합니다.

```text
assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

생각보다 자주 있는 실수가  
글 파일만 커밋하고 이미지 파일은 빠뜨리는 경우입니다.

즉, 경로를 잘 적어도 실제 파일이 없으면 당연히 이미지가 보이지 않습니다.

---

## 2. 경로 앞에 `/` 를 붙였는지 확인하기

대표 이미지 경로는 보통 아래처럼 쓰는 것이 안전합니다.

```yaml
image:
  path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 예시
```

여기서 중요한 것은  
`/assets/...` 처럼 **앞에 슬래시를 붙이는 것**입니다.

앞의 `/` 를 빼먹으면 현재 페이지 기준 상대경로처럼 처리되어  
이미지가 안 보일 수 있습니다. Jekyll에서도 루트 기준 경로와 baseurl 처리 문제는 이미지 미표시의 흔한 원인입니다.

---

## 3. 파일명 대소문자가 정확한지 확인하기

이 부분은 정말 자주 놓치는 문제입니다.

예를 들어 실제 파일이 아래라면

```text
assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

front matter도 정확히 이렇게 써야 합니다.

```yaml
path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

아래처럼 다르면 다른 파일로 인식됩니다.

- `cover.jpg`
- `Cover.jpg`
- `cover.jpeg`
- `cover.png`

Chirpy 관련 이슈에서도  
실제 파일명과 경로가 다르거나 확장자가 다른 경우가 문제 원인으로 지적되었습니다. 

---

## 4. 경로에 슬래시가 두 번 들어가지 않았는지 확인하기

Chirpy 관련 이슈 중에는  
이미지 경로가 `//assets/...` 형태로 잘못 생성되어  
브라우저가 잘못된 URL처럼 처리한 사례도 있었습니다. 

예를 들면 아래처럼 되면 문제가 될 수 있습니다.

```yaml
image:
  path: //assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

정상적인 예시는 아래입니다.

```yaml
image:
  path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
```

즉, 슬래시는 하나만 있어야 합니다.

특히 직접 경로를 수정하거나  
앞뒤 경로를 합치는 과정에서 실수하기 쉽습니다.

---

## 5. `img_path` 와 `image.path` 를 같이 쓸 때 주의하기

Chirpy에서는 이미지 관련 설정을 여러 방식으로 쓸 수 있는데,  
버전에 따라 `img_path` 와 `image.path` 조합이 대표 이미지 미리보기에서 다르게 동작했던 사례가 있었습니다. 
예를 들면 아래처럼 쓰는 방식입니다.

```yaml
img_path: /assets/
image:
  path: images/post-name/cover.jpg
```

본문 이미지에서는 보이는데  
대표 이미지 미리보기에서는 안 뜨는 문제가 보고된 적이 있습니다. 

초보자라면 이 문제를 피하기 위해  
가장 단순하게 **전체 절대경로를 직접 쓰는 방법**을 추천합니다.

즉, 아래처럼 쓰는 쪽이 더 안전합니다.

```yaml
image:
  path: /assets/img/posts/chirpy-thumbnail-guide/cover.jpg
  alt: Chirpy 포스트 대표 이미지 예시
```

---

## 6. 대표 이미지 비율이 너무 이상하지 않은지 보기

이미지가 아예 안 뜨는 문제와는 조금 다르지만,  
대표 이미지가 잘려 보이거나 어색하게 보이면  
비율 문제일 수 있습니다.

Chirpy 문서는 대표 이미지로  
**1200 x 630** 비율을 권장합니다. [web:379]

즉, 아래처럼 준비하는 것이 좋습니다.

- 가로형 이미지
- 1200 x 630
- JPG 또는 WebP
- 텍스트를 가장자리까지 꽉 채우지 않기

너무 세로로 긴 이미지나 정사각형 이미지는  
썸네일 영역에서 어색하게 잘려 보일 수 있습니다.

---

## 7. GitHub Actions 빌드가 성공했는지 확인하기

이미지 파일과 경로가 다 맞아도  
빌드가 실패했으면 사이트에는 반영되지 않습니다.

그래서 대표 이미지가 안 보이면  
반드시 GitHub 저장소의 **Actions 탭**도 확인해 봐야 합니다.

확인할 것은 간단합니다.

- 최신 실행이 성공했는지
- `build` 가 초록 체크인지
- `deploy` 까지 완료되었는지

만약 빌드가 실패했다면  
이미지 문제라기보다 front matter 문법이나 다른 파일 오류일 수도 있습니다.

---

## 8. 공유 미리보기만 안 뜨는 경우도 있다

가끔은 블로그 안에서는 이미지가 보이는데  
카카오톡, 페이스북, X 같은 곳에 공유했을 때만  
대표 이미지가 안 보이는 경우가 있습니다.

Chirpy 이슈에서도  
포스트 본문 이미지는 보이지만 공유용 preview image가 정상적으로 처리되지 않던 사례가 있었습니다. 

이럴 때는 아래를 확인해보면 좋습니다.

- 대표 이미지가 front matter의 `image`로 정확히 지정되었는지
- 경로가 외부 저장소가 아니라 현재 사이트에서 직접 접근 가능한지
- 캐시 때문에 예전 정보가 보이는 것은 아닌지

공유 미리보기는 메타 태그와 캐시 영향을 받기 때문에  
사이트 내부에서 보이는 것과 다르게 동작할 수 있습니다. 

---

## 9. 초보자에게 가장 추천하는 안전한 설정 예시

대표 이미지가 안 뜨는 문제를 줄이려면  
가장 단순한 방식으로 설정하는 것이 좋습니다.

예시는 아래와 같습니다.

```yaml
***
layout: post
title: "Chirpy 대표 이미지가 안 뜰 때 해결 방법"
date: 2026-04-06 00:40:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, image, thumbnail]
image:
  path: /assets/img/posts/fix-chirpy-featured-image-not-showing/cover.jpg
  alt: Chirpy 대표 이미지 오류 해결 가이드
***
```

그리고 실제 파일은 아래 위치에 둡니다.

```text
assets/img/posts/fix-chirpy-featured-image-not-showing/cover.jpg
```

이 방식이 가장 단순하고,  
초보자가 실수할 가능성도 가장 적습니다.

---

## 10. 마지막 체크리스트

대표 이미지가 안 뜰 때는 아래 순서대로 확인해 보세요.

- 이미지 파일이 실제로 업로드되어 있는가
- front matter 문법이 맞는가
- `image.path` 경로가 정확한가
- 경로 앞에 `/` 가 붙어 있는가
- 파일명 대소문자와 확장자가 정확한가
- 경로에 `//` 같은 중복 슬래시가 없는가
- GitHub Actions 빌드가 성공했는가
- 공유 미리보기 캐시 문제는 아닌가

대부분은 이 체크리스트만 따라가도 해결됩니다.

---

## 마무리

Chirpy 대표 이미지가 안 뜨는 문제는  
처음에는 복잡해 보이지만, 실제로는 대부분  
경로나 파일명 같은 아주 기본적인 부분에서 발생합니다.

핵심만 다시 정리하면 아래와 같습니다.

- 이미지 파일이 실제로 있는지 확인하기
- `image.path` 를 절대경로로 정확히 쓰기
- 파일명 대소문자와 확장자까지 맞추기
- 빌드 성공 여부까지 꼭 확인하기

블로그를 처음 운영할 때는 이런 작은 오류 하나에도 시간이 꽤 걸릴 수 있지만,  
한 번 패턴을 익혀두면 이후에는 훨씬 빠르게 문제를 해결할 수 있습니다.
경험하며 묻고 답하고 고치고 이렇게 헤쳐갑니다.
