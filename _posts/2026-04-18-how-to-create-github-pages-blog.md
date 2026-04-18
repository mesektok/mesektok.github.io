---
layout: post
title: "GitHub Pages 블로그 처음 만드는 전체 과정 (Chirpy 테마)"
date: 2026-04-20
categories: [도전기, 도구]
tags: [github-pages, jekyll, chirpy, 블로그만들기, 초보]
description: "Git도 몰랐던 교수가 GitHub Pages + Chirpy 테마로 블로그를 만든 전체 과정을 단계별로 공개합니다."
---

이전 글에서 제가 GitHub 블로그를 시작한 이유를 썼습니다.

이번 글에서는 **실제로 어떻게 만들었는지** 전체 과정을 공개합니다.

어려운 용어는 최대한 쉽게 풀어 설명합니다.  
저처럼 Git을 처음 접하는 분도 따라 할 수 있습니다.

---

## 준비물

| 항목 | 내용 | 비용 |
|---|---|---|
| GitHub 계정 | github.com 가입 | 무료 |
| 도메인 | 선택사항 (없어도 됨) | 유료 |
| 브라우저 | Chrome 추천 | 무료 |

PC에 아무것도 설치하지 않아도 됩니다.  
**GitHub 웹사이트에서 전부 가능합니다.**

---

## 1단계 — GitHub 계정 만들기

```
① github.com 접속
② "Sign up" 클릭
③ 이메일, 비밀번호, 사용자명 입력
④ 이메일 인증 완료
```

사용자명은 나중에 블로그 주소가 됩니다.  
예) 사용자명이 `mesektok` → 블로그 주소 `mesektok.github.io`

---

## 2단계 — Chirpy 테마 저장소 복사하기

Chirpy 테마를 처음부터 만들 필요가 없습니다.  
공식 템플릿을 내 계정으로 복사(Fork)하면 됩니다.

```
① 아래 주소 접속
   github.com/cotes2020/chirpy-starter

② 오른쪽 상단 "Fork" 버튼 클릭

③ Repository name 입력
   반드시 이 형식으로: 사용자명.github.io
   예) mesektok.github.io

④ "Create fork" 클릭
```

> **주의** — 저장소 이름을 `사용자명.github.io` 형식으로 정확히 입력해야 합니다.  
> 다르게 입력하면 블로그가 작동하지 않습니다.

---

## 3단계 — GitHub Pages 활성화

```
① 방금 만든 저장소 클릭
② 상단 메뉴 "Settings" 클릭
③ 왼쪽 메뉴 "Pages" 클릭
④ Source → "Deploy from a branch" 선택
⑤ Branch → "main" / "(root)" 선택
⑥ "Save" 클릭
```

2~3분 후 `사용자명.github.io` 주소로 접속하면  
기본 블로그가 보입니다.

---

## 4단계 — `_config.yml` 기본 설정

저장소 루트에서 `_config.yml` 파일을 클릭합니다.  
✏️ 연필 아이콘 클릭 후 아래 항목을 수정합니다.

```yaml
title: 블로그 이름
tagline: "한 줄 소개"

description: >-
  블로그 설명을 여기에 씁니다.

url: "https://사용자명.github.io"

author:
  name: 내 이름
  bio: 한 줄 자기소개
  location: South Korea

lang: ko
timezone: Asia/Seoul
```

수정 후 맨 아래 "Commit changes" 클릭.

---

## 5단계 — 첫 포스팅 올리기

`_posts` 폴더에 파일을 만듭니다.

```
① 저장소에서 _posts 폴더 클릭
② "Add file" → "Create new file" 클릭
③ 파일명 입력 형식:
   YYYY-MM-DD-제목.md
   예) 2026-04-20-hello-world.md
```

파일 내용:

```markdown
---
layout: post
title: "첫 번째 글입니다"
date: 2026-04-20
categories: [일상]
tags: [시작, 블로그]
---

안녕하세요. 첫 번째 글입니다.
```

"Commit changes" 클릭 → 1~2분 후 블로그에 글이 올라갑니다.

---

## 6단계 — 도메인 연결 (선택사항)

별도로 구입한 도메인이 있다면 연결할 수 있습니다.

```
① Settings → Pages → Custom domain
② 도메인 입력 (예: mesektok.com)
③ "Save" 클릭
④ 도메인 구입처에서 DNS 설정
   A 레코드 4개 추가:
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
⑤ 24시간 이내 자동 연결
```

---

## 전체 과정 요약

```
① GitHub 계정 만들기
② Chirpy 템플릿 Fork → 저장소 이름: 사용자명.github.io
③ Settings → Pages → main 브랜치 활성화
④ _config.yml 기본 정보 수정
⑤ _posts 폴더에 첫 글 올리기
⑥ (선택) 도메인 연결
```

**총 소요시간: 30분**

---

## 제가 처음에 했던 실수들

- 저장소 이름을 `사용자명.github.io` 가 아닌 다른 이름으로 만들었습니다 → 블로그가 안 보임
- `_config.yml` 에서 `url:` 을 수정 안 했습니다 → 이미지가 안 보임
- 포스팅 파일명 날짜 형식을 틀리게 썼습니다 → 글이 안 올라감

실수해도 괜찮습니다.  
GitHub는 수정하고 다시 Commit하면 바로 반영됩니다.

---

## 마치며

처음엔 막막했지만 단계별로 따라가면  
**30분 안에 블로그가 만들어집니다.**

다음 글에서는 `_config.yml` 전체 설정을 하나씩 뜯어봅니다.

---

*📌 [도전기 시리즈 전체 보기](/categories/)*
