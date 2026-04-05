---
layout: post
title: "Chirpy + GitHub Pages에 Google AdSense 등록하는 방법"
date: 2026-04-06 00:26:00 +0900
categories: [블로그, 수익화]
tags: [chirpy, github-pages, google-adsense, jekyll]
---

Chirpy 테마와 GitHub Pages를 사용하는 블로그에 Google AdSense를 붙이는 방법을 정리해 봤습니다.

이번 글에서는 다음 4가지를 순서대로 진행합니다.

- AdSense 기본 스크립트를 사이트 전체에 넣기
- 광고가 표시될 위치에 광고 단위 코드 넣기
- ads.txt 파일 추가하기
- 적용 후 정상 작동 여부 확인하기

---

## 0. 전제 조건

이 글은 아래 조건을 기준으로 설명합니다.

- AdSense 계정 승인이 완료된 상태
- `ca-pub-xxxxxxxxxxxx` 형식의 퍼블리셔 ID를 받은 상태
- AdSense에서 사이트 연결용 코드와 광고 단위 코드를 받을 수 있는 상태

---

## 1. AdSense 기본 스크립트 넣기

AdSense에서 사이트 연결 시 제공하는 기본 스크립트는 보통 아래와 같습니다.

```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXX"
        crossorigin="anonymous"></script>
```

예전에는 `_includes/head.html`을 직접 건드리는 방식을 생각할 수 있지만,  
Chirpy에서는 이 방법이 빌드 오류를 만들거나 기존 head 구성을 덮어쓸 수 있어서 조심해야 합니다.

그래서 더 안전한 방법으로 `_includes/metadata-hook.html` 파일을 사용하는 것을 추천합니다.

### 적용 방법

1. 저장소 루트에 `_includes` 폴더가 없다면 새로 만듭니다.
2. `_includes/metadata-hook.html` 파일을 생성합니다.
3. 아래 코드를 그대로 넣습니다.

```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXX"
        crossorigin="anonymous"></script>
```

### 주의할 점

아래처럼 넣으면 안 됩니다.

```html
<script async src= https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-"1372015701075050"
     crossorigin="anonymous"></script>
```

이유는 다음과 같습니다.

- `src` 값은 따옴표로 정확히 감싸야 합니다.
- `ca-pub-1372015701075050` 는 한 덩어리 문자열이어야 합니다.
- 중간에 따옴표가 들어가면 잘못된 코드가 됩니다.

즉, 올바른 형식은 아래처럼 작성해야 합니다.

```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-1372015701075050"
        crossorigin="anonymous"></script>
```

---

## 2. 광고가 들어갈 위치에 ad unit 코드 넣기

AdSense에서 광고 단위를 만들면 아래와 비슷한 코드가 생성됩니다.

```html
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-XXXXXXXXXXXX"
     data-ad-slot="YYYYYYYYYY"
     data-ad-format="auto"
     data-full-width-responsive="true"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
```

이 코드는 재사용하기 쉽게 include 파일로 따로 빼는 것이 좋습니다.

### 파일 만들기

`_includes/adsense_post_bottom.html` 파일을 만들고 아래 코드를 넣습니다.

```html
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-XXXXXXXXXXXX"
     data-ad-slot="YYYYYYYYYY"
     data-ad-format="auto"
     data-full-width-responsive="true"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
```

### 포스트 하단에 불러오기

이후 `_layouts/post.html` 파일을 수정해서, 본문 아래에 광고 include를 넣습니다.

예시는 아래와 같습니다.

```liquid
<div class="post-content">
  {{ content }}
</div>

{% if jekyll.environment == 'production' %}
  {% include adsense_post_bottom.html %}
{% endif %}
```

이렇게 하면 개발 중에는 광고가 나오지 않고, 실제 배포 환경에서만 광고가 노출되도록 설정할 수 있습니다.

---

## 3. ads.txt 파일 추가하기

AdSense에서 제공하는 `ads.txt` 내용도 함께 등록해 두는 것이 좋습니다.

### 저장 위치

여기서 말하는 “저장소 루트”는  
GitHub 저장소에 들어갔을 때 가장 처음 보이는 최상단 위치를 말합니다.

예를 들어 아래 파일들이 보이는 자리입니다.

- `_config.yml`
- `_posts`
- `_tabs`
- `assets`
- `README.md`

바로 이 위치에 `ads.txt` 파일을 만들면 됩니다.

### 만드는 방법

1. GitHub 저장소 첫 화면으로 이동합니다.
2. **Add file → Create new file** 클릭
3. 파일명에 `ads.txt` 입력
4. AdSense에서 제공한 내용을 그대로 붙여 넣기
5. Commit 해서 저장

등록 후에는 아래 주소로 확인합니다.

```text
https://mesektok.com/ads.txt
```

브라우저에서 해당 내용이 보이면 정상입니다.

---

## 4. 적용 후 확인하기

모든 파일을 저장한 뒤에는 아래 순서로 점검합니다.

1. GitHub에 커밋합니다.
2. GitHub Actions에서 빌드가 성공했는지 확인합니다.
3. 사이트에 접속해서 페이지 소스를 봅니다.
4. `<head>` 안에 AdSense 기본 스크립트가 들어갔는지 확인합니다.
5. 포스트 본문 아래에 `adsbygoogle` 코드가 들어갔는지 확인합니다.
6. `https://mesektok.com/ads.txt` 주소가 열리는지도 확인합니다.

광고는 코드를 넣자마자 바로 보이지 않을 수 있습니다.  
보통 수십 분에서 1~2일 정도 반영 시간이 걸릴 수 있습니다.

---

## 정리

Chirpy + GitHub Pages에서 AdSense를 붙일 때 핵심은 아래 3가지입니다.

- 전체 사이트용 AdSense 스크립트는 `_includes/metadata-hook.html`에 넣기
- 광고 단위 코드는 `_includes/adsense_post_bottom.html`로 분리하기
- `ads.txt`는 저장소 루트에 만들기

처음에는 조금 복잡해 보이지만, 한 번만 구조를 잡아두면 이후에는 광고 단위만 추가하면서 계속 확장할 수 있습니다.
