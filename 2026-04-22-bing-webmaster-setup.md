---
layout: post
title: "Bing Webmaster 연결하는 방법 (인증 코드 얻기)"
date: 2026-04-22
categories: [도전기, 도구]
tags: [bing, webmaster, seo, chirpy, jekyll, 블로그운영]
description: "Bing Webmaster Tools에 블로그를 등록하고 _config.yml 에 인증 코드를 연결하는 전체 과정을 정리합니다."
---

Google Search Console 은 구글 검색 노출을 위한 것이고,  
**Bing Webmaster** 는 빙(Bing) 검색 노출을 위한 것입니다.

빙은 마이크로소프트 검색엔진입니다.  
국내 사용자는 적지만 **ChatGPT 검색, Edge 브라우저** 사용자가 유입됩니다.  
등록해두면 손해 없습니다.

---

## 전체 순서

```
① Bing Webmaster 가입
② 사이트 추가
③ 인증 코드 복사
④ _config.yml 에 붙여넣기
⑤ Bing 에서 확인 버튼 클릭
⑥ 사이트맵 제출
```

---

## 1단계 — Bing Webmaster 접속

```
bing.com/webmasters 접속
→ "시작하기" 클릭
```

로그인 방법 선택:

```
① Microsoft 계정 (outlook.com 등)
② 구글 계정으로 로그인  ← 추천
③ Facebook 계정
```

구글 계정이 있으면 새로 가입할 필요 없습니다.

---

## 2단계 — 사이트 추가

```
로그인 후 첫 화면에서
"내 사이트 추가" 클릭

URL 입력:
https://mesektok.com

→ "추가" 클릭
```

---

## 3단계 — 인증 방법 선택

사이트 소유권 확인 화면이 나옵니다.  
3가지 방법 중 **"HTML 메타 태그"** 를 선택합니다.

```
옵션 1: XML 파일 업로드
옵션 2: HTML 메타 태그    ← 이것 선택
옵션 3: CNAME 레코드 추가
```

---

## 4단계 — 인증 코드 복사

HTML 메타 태그 선택 후 아래처럼 보입니다:

```html
<meta name="msvalidate.01" content="ABCDEF1234567890ABCDEF1234567890">
```

여기서 **content=" " 안의 값만** 복사합니다.

```
복사할 것:
ABCDEF1234567890ABCDEF1234567890
```

> 따옴표 없이 안의 코드만 복사합니다.

---

## 5단계 — `_config.yml` 수정

GitHub 저장소에서 `_config.yml` 열기 → ✏️ 클릭

아래 부분을 찾아서 수정합니다:

```yaml
# 수정 전
webmaster_verifications:
  google: ******        2H8tYGe0PrSIb87wwM-YtM****
  bing:        ????      # fill in your Bing verification code

# 수정 후
webmaster_verifications:
  google: ******        2H8tYGe0PrSIb87wwM-YtM****
  bing: ABCDEF1234567890ABCDEF1234567890
```

`ABCDEF1234567890ABCDEF1234567890` 자리에  
복사한 코드 붙여넣기

→ **"Commit changes"** 클릭

---

## 6단계 — Bing 에서 확인 버튼 클릭

```
① Commit 후 2~3분 대기
   (GitHub 배포 시간)

② Bing Webmaster 창으로 돌아가서
   → "확인" 버튼 클릭

③ 아래 메시지 확인:
   "사이트가 성공적으로 확인되었습니다" ✅
```

---

## 7단계 — 사이트맵 제출

인증 완료 후 사이트맵을 제출합니다.  
사이트맵은 블로그의 전체 글 목록을 검색엔진에 알려주는 파일입니다.

Chirpy 테마는 `jekyll-sitemap` 플러그인이 이미 설정되어 있어서  
**sitemap.xml 이 자동 생성**되어 있습니다.

```
Bing Webmaster → 왼쪽 메뉴 "사이트맵" 클릭
→ "사이트맵 제출" 클릭
→ 아래 주소 입력:

https://mesektok.com/sitemap.xml

→ "제출" 클릭
```

---

## 확인 방법

사이트맵 제출 후 1~2일 안에 아래에서 확인됩니다:

```
Bing Webmaster → "URL 검사"
→ mesektok.com 입력
→ 검색 결과에 블로그 표시 여부 확인
```

---

## Google + Bing 동시 적용 최종 코드

```yaml
webmaster_verifications:
  google: lWSgOsFTkT0P4k6DX2H8tYGe0PrSIb87wwM-YtMUxPM
  bing: 여기에_빙_인증코드
  alexa: # fill in your Alexa verification code
  yandex: # fill in your Yandex verification code
  baidu: # fill in your Baidu verification code
  facebook: # fill in your Facebook verification code
```

나머지 `alexa`, `yandex` 등은 비워두어도 됩니다.

---

## 마치며

Google + Bing 두 곳 모두 등록하면  
**검색 노출 기회가 2배**가 됩니다.

각각 사이트맵까지 제출해두면  
새 글을 올릴 때 검색엔진이 더 빠르게 인식합니다.

다음 글에서는 애드센스 신청 전 체크리스트를 정리합니다.

---

*📌 [도전기 시리즈 전체 보기](/categories/)*
