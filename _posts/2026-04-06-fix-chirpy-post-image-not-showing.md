---
layout: post
title: "Chirpy 본문 이미지가 안 뜰 때 해결 방법"
date: 2026-04-06 00:41:00 +0900
categories: [블로그, Chirpy]
tags: [chirpy, jekyll, image, markdown, error]
description: "Chirpy 블로그 글 본문에 넣은 이미지가 보이지 않을 때 확인해야 할 원인과 해결 방법을 초보자 기준으로 정리했습니다."
image:
  path: /assets/img/posts/fix-chirpy-post-image-not-showing/cover.jpg
  alt: Chirpy 본문 이미지 오류 해결 가이드
---

Chirpy 블로그 글을 작성하다 보면  
분명히 이미지를 넣었는데 본문에서 안 뜨는 경우가 있습니다.

Markdown 편집기에서는 잘 보이는데  
실제 블로그에 들어가면 이미지가 깨져 있거나 아예 안 나와서 당황할 때가 있죠.

오늘은 **Chirpy 본문 이미지가 안 뜰 때 해결하는 방법**을  
초보자 기준으로 쉽게 정리해보겠습니다.

---

## 본문 이미지가 안 뜨는 가장 흔한 이유

Chirpy 본문 이미지가 안 보일 때는 보통 아래 원인 중 하나입니다.

- 이미지 파일을 잘못된 위치에 넣음  
- 이미지 경로를 잘못 적음  
- 경로 앞 `/` 를 빼먹음  
- 파일명 대소문자나 확장자가 다름  
- 외부 이미지 주소가 403 또는 차단 상태임  
- `media_subpath` 를 쓸 때 경로가 꼬임  
- GitHub Pages 빌드가 아직 반영되지 않음  

즉, 대부분은 이미지 문법 자체보다  
**파일 위치와 경로 문제**인 경우가 많습니다. 

---

## 1. 이미지 파일은 `assets` 아래에 두는 것이 가장 안전하다

초보자에게 가장 쉬운 방법은  
이미지를 `_posts` 폴더 안이 아니라 `assets/img/posts/...` 아래에 두는 것입니다.  
Jekyll 기반 사이트에서는 이미지 같은 정적 파일을 저장소 루트의 `assets` 아래에 두고  
루트 상대경로로 불러오는 방식이 일반적입니다. 

예를 들면 아래처럼 두면 됩니다.

```text
_posts/2026-04-06-fix-chirpy-post-image-not-showing.md
assets/img/posts/fix-chirpy-post-image-not-showing/example-01.jpg
```

이 구조가 좋은 이유는 아래와 같습니다.

- 글과 이미지가 섞이지 않음  
- 경로를 기억하기 쉬움  
- 나중에 다른 글 이미지와 구분하기 편함  

---

## 2. 본문에서는 루트 기준 경로로 넣기

본문 이미지는 보통 Markdown 문법으로 넣습니다.

```markdown
![예시 이미지](/assets/img/posts/fix-chirpy-post-image-not-showing/example-01.jpg)
```

여기서 중요한 점은  
경로 앞에 `/` 를 붙여 **루트 기준 경로**로 쓰는 것입니다.  
정적 사이트에서는 루트 상대경로를 쓰는 방식이  
이미지 미표시 문제를 줄이는 데 도움이 됩니다. 
잘 안 되는 예시는 보통 이런 형태입니다.

```markdown
![예시 이미지](assets/img/posts/fix-chirpy-post-image-not-showing/example-01.jpg)
```

이렇게 쓰면 현재 글 경로 기준으로 해석되어  
실제 사이트에서는 이미지가 안 뜰 수 있습니다.

---

## 3. 파일명과 확장자를 정확히 확인하기

생각보다 자주 발생하는 실수입니다.

예를 들어 실제 파일이 아래라면

```text
example-01.jpg
```

본문에서도 정확히 이렇게 적어야 합니다.

```markdown
![예시 이미지](/assets/img/posts/fix-chirpy-post-image-not-showing/example-01.jpg)
```

다음처럼 조금이라도 다르면 안 뜰 수 있습니다.

- `Example-01.jpg`  
- `example-01.JPG`  
- `example-01.png`  

GitHub Pages와 Jekyll 환경에서는  
대소문자와 파일 확장자 차이가 그대로 반영되기 때문에  
이 부분은 꼭 정확히 맞춰야 합니다. 

---

## 4. Markdown 미리보기에서는 보이는데 실제 사이트에서는 안 뜰 수 있다

이 부분도 초보자가 자주 헷갈립니다.

에디터 미리보기에서는 이미지가 잘 보여도,  
실제 Chirpy 블로그에서는 경로가 달라져서 안 뜨는 경우가 있습니다.  
GitHub 커뮤니티에서도 Markdown 미리보기에서는 보이지만  
배포 후에는 보이지 않는 사례가 보고되었고,  
이때 `assets` 위치와 경로 방식 점검이 중요하다고 안내합니다. 

즉, “미리보기에서 보이니까 경로가 맞겠지”라고 생각하면 안 됩니다.

실제 사이트에서 안 보인다면  
반드시 아래를 다시 확인해야 합니다.

- 이미지가 `assets` 아래 있는지  
- 경로 앞에 `/` 가 있는지  
- 실제 URL로 접속했을 때 열리는지  

---

## 5. 외부 이미지 주소를 쓸 때는 403 오류를 조심하기

본문 이미지에 외부 링크를 넣는 경우도 있습니다.

예를 들면 아래처럼요.

```markdown
![외부 이미지 예시](https://example.com/path/to/image.jpg)
```

이 방식은 편해 보이지만,  
외부 서버가 직접 이미지 접근을 막으면 이미지가 안 보일 수 있습니다.  
실제 Chirpy 사용자 사례에서도 외부 이미지는 콘솔에 `403` 오류가 뜨며  
표시되지 않는 경우가 있었습니다. 

즉, 초보자라면 외부 이미지보다  
내 저장소의 `assets/img` 안에 이미지를 넣고 쓰는 방법이 훨씬 안전합니다.

---

## 6. `media_subpath`를 쓸 때는 경로 기준을 정확히 알아야 한다

Chirpy에서는 `media_subpath`를 front matter에 설정해  
본문 이미지 경로를 짧게 쓰는 방법도 지원합니다.

예를 들어 아래처럼 쓸 수 있습니다.

```yaml
***
layout: post
title: "본문 이미지 테스트"
date: 2026-04-06 00:41:00 +0900
media_subpath: /assets/img/posts/fix-chirpy-post-image-not-showing
***
```

그러면 본문에서는 이렇게 짧게 쓸 수 있습니다.

```markdown
layout: post
title: "본문 이미지 테스트"
date: 2026-04-06 00:41:00 +0900
media_subpath: /assets/img/posts/fix-chirpy-post-image-not-showing
```

하지만 이 기능을 잘못 이해하면  
오히려 경로가 꼬여서 이미지가 안 뜰 수 있습니다.

그래서 초보자라면 처음에는  
`media_subpath` 없이 전체 경로를 직접 쓰는 방법이 더 안전합니다.

즉, 아래처럼 쓰는 것이 가장 단순합니다.

```markdown
![예시 이미지](/assets/img/posts/fix-chirpy-post-image-not-showing/example-01.jpg)
```

---

## 7. `img_path` 와 `image.path` 방식은 대표 이미지와 본문 이미지가 다르게 동작할 수 있다

Chirpy 관련 이슈에서는  
`img_path` 와 `image.path` 조합이 대표 이미지 미리보기에서는  
예상과 다르게 동작했던 사례가 있었습니다.
본문 이미지와 대표 이미지는 처리 방식이 조금 다를 수 있기 때문에,  
본문 이미지를 넣을 때는 복잡한 조합보다  
**그냥 완전한 절대경로를 직접 쓰는 방식**이 초보자에게 더 안전합니다. [web:468][web:379]

---

## 8. GitHub Actions 빌드 성공 여부도 꼭 확인하기

이미지 파일과 경로를 다 고쳤는데도 안 보인다면  
빌드가 아직 반영되지 않았을 수도 있습니다.

Chirpy + GitHub Pages는  
글이나 이미지 파일을 수정하면 다시 빌드 과정을 거칩니다.

따라서 아래를 꼭 확인해 보세요.

- GitHub 저장소의 **Actions** 탭으로 이동  
- 최신 실행이 성공했는지 확인  
- `build` 와 `deploy` 가 완료되었는지 확인  

빌드가 실패하면 이미지뿐 아니라  
글 전체가 제대로 반영되지 않을 수도 있습니다.

---

## 9. 초보자에게 가장 추천하는 안전한 본문 이미지 예시

처음에는 가장 단순한 방식으로 넣는 것이 좋습니다.

실제 파일 위치:

```text
assets/img/posts/fix-chirpy-post-image-not-showing/example-01.jpg
```

본문 작성 예시:

```markdown
## 본문 이미지 예시

아래는 Chirpy 글 본문에 이미지를 넣는 가장 쉬운 방법입니다.

![Chirpy 본문 이미지 예시](/assets/img/posts/fix-chirpy-post-image-not-showing/example-01.jpg)

_이미지는 assets/img 아래에 저장하고 절대경로로 불러오면 가장 헷갈리지 않습니다._
```

이 방식이 가장 단순하고,  
문제가 생겨도 어디를 확인해야 할지 바로 알 수 있습니다.

---

## 10. 마지막 체크리스트

본문 이미지가 안 뜰 때는 아래 순서대로 확인해 보세요.

- 이미지 파일이 실제로 업로드되어 있는가  
- 이미지가 `assets/img` 아래에 있는가  
- 경로 앞에 `/` 를 붙였는가  
- 파일명 대소문자와 확장자가 정확한가  
- 외부 이미지 주소가 403으로 막히지 않았는가  
- `media_subpath` 설정 때문에 경로가 꼬인 것은 아닌가  
- GitHub Actions 빌드가 성공했는가  

이 정도만 확인해도 대부분 해결됩니다.

---

## 마무리

Chirpy 본문 이미지가 안 뜨는 문제는  
처음에는 복잡해 보여도 대부분 아주 기본적인 원인에서 시작됩니다.

핵심만 다시 정리하면 아래와 같습니다.

- 이미지는 `assets/img` 아래에 저장하기  
- 본문에서는 `/assets/img/...` 경로로 불러오기  
- 외부 이미지보다 로컬 이미지 사용하기  
- 빌드 성공 여부까지 꼭 확인하기  

처음에는 경로와 폴더 구조가 낯설 수 있지만,  
한두 번만 해보면 금방 익숙해집니다.

특히 블로그를 꾸준히 운영할 생각이라면  
이미지 저장 위치와 경로 작성 방식을 처음부터 통일해 두는 것이 정말 중요합니다.
