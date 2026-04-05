**---
layout: post
title: "새 포스트 작성 후 GitHub Actions 확인하는 방법"
date: 2026-04-06 00:30:00 +0900
categories: [블로그, GitHub]
tags: [github-actions, github-pages, chirpy, jekyll]
description: "Chirpy 블로그에서 새 글을 올린 뒤 GitHub Actions로 빌드 성공 여부를 확인하는 방법을 초보자용으로 쉽게 정리했습니다."
---

블로그에 새 글을 올렸는데,  
“정말 정상적으로 올라간 걸까?”  
“왜 내 글이 안 보이지?”  
이런 생각이 들 때가 있습니다.

이럴 때 가장 먼저 확인해야 하는 곳이 바로 **GitHub Actions** 입니다.

오늘은 초보자도 쉽게 따라할 수 있게,  
**새 포스트 작성 후 GitHub Actions 확인하는 방법**을 정리해보겠습니다.

---

## GitHub Actions는 왜 확인해야 할까?

Chirpy + GitHub Pages 블로그는  
글을 올리면 자동으로 사이트를 다시 빌드합니다.

즉, 새 포스트를 하나 올렸다고 바로 사이트에 보이는 것이 아니라,  
GitHub가 먼저 파일에 문제가 없는지 확인하고 빌드한 뒤 배포까지 끝내야 합니다.

이 과정을 확인하는 곳이 바로 **Actions 탭**입니다.

---

## 언제 확인하면 될까?

새 글을 작성한 뒤 아래 과정을 했다면 바로 확인해 보면 됩니다.

- `_posts` 폴더에 새 글 파일 업로드
- 글 내용 수정
- Commit 또는 Push 완료

이 작업이 끝나면 GitHub가 자동으로 워크플로를 실행합니다.

---

## 1. GitHub 저장소로 들어가기

먼저 내 블로그 저장소로 들어갑니다.

예를 들어 저장소 이름이 `mesektok.github.io` 라면  
그 저장소 메인 화면으로 들어가면 됩니다.

상단 메뉴를 보면 아래처럼 여러 탭이 보입니다.

- Code
- Issues
- Pull requests
- Actions

여기서 **Actions** 를 클릭하면 됩니다. [web:420]

---

## 2. Actions 탭에서 최신 실행 보기

Actions 탭으로 들어가면  
최근 실행된 워크플로 목록이 보입니다.

여기에서 방금 글을 올린 시간과 비슷한 실행 항목을 클릭하면 됩니다. [web:420][web:400]

보통 이름은 아래처럼 보일 수 있습니다.

- `pages build and deployment`
- `Deploy Jekyll site to Pages`
- `Build and Deploy`

이름은 조금 다를 수 있지만,  
방금 커밋한 시간의 최신 실행을 보면 거의 맞습니다.

---

## 3. build 와 deploy 상태 확인하기

실행 화면에 들어가면 보통 `build` 와 `deploy` 같은 단계가 보입니다.

여기서 가장 중요한 것은 색깔입니다.

### 초록 체크 표시
초록색 체크가 뜨면 성공입니다.

즉,

- 글 파일이 문제 없이 읽혔고
- 사이트 빌드가 정상 완료되었고
- 배포도 잘 끝났다는 뜻입니다. [web:420][web:423]

### 빨간 X 표시
빨간 X가 뜨면 실패입니다.

이 경우는 보통

- Markdown 문법 오류
- front matter 오류
- 파일명 형식 오류
- `_config.yml` 설정 오류
- include 파일 경로 오류

같은 문제가 있는 경우가 많습니다. [web:420][web:373]

### 회색 또는 노란 표시
이건 아직 실행 중이라는 뜻입니다.

잠깐 기다렸다가 새로고침하면 결과가 바뀔 수 있습니다. [web:420]

---

## 4. 실패했을 때는 build 로그 보기

빨간 X가 떴다고 너무 당황할 필요는 없습니다.

실패한 실행을 클릭한 뒤 **build** 를 누르면  
어디에서 문제가 생겼는지 로그를 볼 수 있습니다. GitHub는 workflow 실행 기록과 각 job 로그를 Actions 화면에서 확인할 수 있게 제공합니다. [web:420][web:401]

로그에서 자주 보이는 오류는 아래와 같습니다.

- `Invalid YAML`
- `Liquid Exception`
- `could not locate the included file`
- `Process completed with exit code 1`

이런 문구가 보이면,  
그 파일이나 줄 근처를 다시 수정하면 됩니다. [web:401]

---

## 5. 초보자가 가장 자주 틀리는 부분

처음 블로그를 만들면 보통 아래 부분에서 많이 막힙니다.

### 파일 이름 형식
Chirpy 포스트 파일은 `_posts` 폴더 안에  
`YYYY-MM-DD-title.md` 형식으로 만들어야 합니다. [web:379][web:385]

예를 들면 이런 식입니다.

```text
2026-04-06-my-first-post.md
```

### front matter 누락
포스트 파일 맨 위에는 반드시 `---` 로 감싼 YAML front matter가 있어야 합니다. Jekyll은 파일 맨 처음의 front matter를 기준으로 포스트를 처리합니다. [web:373][web:374]

예시는 아래와 같습니다.

```yaml
***
layout: post
title: "내 첫 글"
date: 2026-04-06 00:30:00 +0900
categories: [블로그, 시작]
tags: [chirpy, jekyll]
***
```

### include 파일 경로 오류
`_includes` 나 `_layouts` 파일을 수정할 때 경로가 틀리면  
빌드가 실패할 수 있습니다. 특히 잘못된 include 구문은 Jekyll에서 바로 오류로 이어질 수 있습니다. [web:401]

---

## 6. 정상 배포됐는지 마지막 확인하기

Actions에서 초록 체크가 떴다면  
이제 실제 블로그에 들어가 보면 됩니다.

확인할 것은 간단합니다.

- 메인 화면에 새 글이 보이는지
- 태그나 카테고리가 잘 표시되는지
- 글을 클릭했을 때 본문이 정상적으로 열리는지

만약 Actions는 성공했는데 글이 안 보인다면  
파일 날짜, 파일 위치, front matter 내용을 다시 확인해 보는 것이 좋습니다. Chirpy는 `_posts` 폴더 아래의 올바른 이름 형식과 front matter를 기준으로 포스트를 렌더링합니다. [web:379][web:385]

---

## 정리

새 포스트를 올린 뒤에는 아래 순서만 기억하면 됩니다.

1. GitHub 저장소 들어가기
2. **Actions** 탭 클릭
3. 최신 실행 열기
4. `build` 와 `deploy` 상태 확인
5. 실패 시 `build` 로그 확인

처음에는 조금 낯설 수 있지만,  
몇 번 해보면 어디서 확인해야 하는지 금방 익숙해집니다.

블로그 글이 안 보일 때는 괜히 테마 문제부터 의심하기보다,  
먼저 **GitHub Actions에서 빌드 성공 여부부터 확인하는 습관**을 들이면 훨씬 수월합니다.**
