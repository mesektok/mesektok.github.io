---
layout: post
title: "Google 서치 콘솔에 mesektok.com 등록하기"
date: 2026-04-05 23:32:00 +0900
categories: [블로그, SEO]
tags: [google-search-console, seo, github-pages, chirpy]
---

`mesektok.com`을 Google **서치 콘솔**에 등록하는 방법을 정리했습니다.  
Chirpy 테마 + GitHub Pages 환경을 기준으로 설명합니다.

---

## 1. Search Console에서 속성 추가

1. 브라우저에서 `https://search.google.com/search-console` 접속 후 구글 계정으로 로그인합니다.  
2. 대시보드에서 **「속성 추가」** 버튼을 클릭합니다.  
3. 팝업이 뜨면 오른쪽 **「URL 접두어」**를 선택하고, 주소에 아래처럼 입력합니다.

   ```text
   https://mesektok.com
   ```

4. **「계속」** 버튼을 눌러 소유권 확인 단계로 넘어갑니다.

---

## 2. HTML 태그 방식으로 메타 태그 받기

1. 소유권 확인 방법 목록에서 **「HTML 태그」**(메타 태그) 방식을 선택합니다.  
2. 아래와 비슷한 태그가 나오는데, 전체를 복사해 둡니다.

   ```html
   <meta name="google-site-verification" content="여기에-긴-코드값" />
   ```

이때 실제로 필요한 것은 `content="..."` 안에 있는 **코드 값**입니다.

---

## 3. `_config.yml`에 메타 태그 값 넣기

Chirpy 테마는 `webmaster_verifications` 설정을 제공하므로, 여기만 수정하면 `<head>`에 메타 태그가 자동으로 들어갑니다.

1. GitHub 저장소에서 `_config.yml` 파일을 엽니다.  
2. 아래 부분을 찾습니다.

   ```yml
   webmaster_verifications:
     google:
     bing:
     alexa:
     yandex:
     baidu:
     facebook:
   ```

3. Search Console에서 받은 코드 값을 `google:` 항목에 넣습니다. 예시는 다음과 같습니다.

   ```yml
   webmaster_verifications:
     google: "여기에-긴-코드값을-붙여넣기"
     bing:
     alexa:
     yandex:
     baidu:
     facebook:
   ```

4. 수정 후 **Commit changes**를 눌러 저장합니다.  
   GitHub Pages는 커밋 후 자동으로 다시 빌드됩니다.

---

## 4. Search Console에서 소유권 확인

1. 커밋 후 1‒2분 정도 기다렸다가, 다시 Search Console 소유권 확인 페이지로 돌아갑니다.  
2. **「확인」** 버튼을 클릭합니다.  
3. `_config.yml`에 넣은 코드가 정상 반영되었다면 **소유권 확인 완료** 메시지가 뜹니다.

---

## 5. 사이트맵(sitemap.xml) 제출하기

Google에 사이트 구조를 빠르게 알려주려면 사이트맵을 제출하는 것이 좋습니다. Chirpy 테마는 기본적으로 `sitemap.xml`을 생성합니다.

1. Search Console 왼쪽 메뉴에서 **「Sitemaps」** 메뉴를 클릭합니다.  
2. `새 사이트맵 추가` 입력 칸에 아래처럼 적고 **제출**합니다.

   ```text
   sitemap.xml
   ```

3. 상태가 **성공**으로 표시되면 설정이 끝난 것입니다.

---

## 마무리

이 과정을 마치면 `https://mesektok.com`이 Google 서치 콘솔에 등록되고,  
구글이 블로그를 크롤링하면서 검색 결과에도 조금씩 노출되기 시작합니다.

이제부터는:

- 새로운 글을 꾸준히 발행하고  
- 서치 콘솔에서 **검색 성과 / 색인 현황 / 오류**만 주기적으로 확인하면 됩니다.
