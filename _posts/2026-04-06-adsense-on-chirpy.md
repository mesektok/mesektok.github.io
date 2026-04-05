---
layout: post
title: "Chirpy 블로그에 구글 애드센스 등록하는 방법"
date: 2026-04-06 00:28:00 +0900
categories: [블로그, 애드센스]
tags: [chirpy, jekyll, github-pages, adsense]
description: "초보자도 따라할 수 있게 Chirpy 블로그에 Google AdSense를 붙이는 방법을 쉽게 정리했습니다."
---

GitHub Pages와 Chirpy 테마로 블로그를 운영하다 보면, 어느 순간 “이제 애드센스도 붙여볼까?” 하는 생각이 듭니다.

저도 처음에는 파일 위치가 헷갈리고, `_includes`가 뭔지, `ads.txt`는 어디에 만들어야 하는지 잘 몰라서 많이 헤맸습니다.

그래서 오늘은 **완전 초보자 기준으로**, Chirpy 블로그에 Google AdSense를 연결하는 방법을 아주 쉽게 정리해보겠습니다.

---

## 먼저 알아둘 것

애드센스를 붙이려면 크게 3가지만 하면 됩니다.

- 사이트 전체에 애드센스 기본 스크립트 넣기
- 광고가 나올 자리에 광고 코드 넣기
- `ads.txt` 파일 추가하기

처음 보면 복잡해 보이지만, 하나씩 따라 하면 어렵지 않습니다.

---

## 1. 애드센스 기본 코드 넣기

먼저 Google AdSense에서 사이트 연결용 코드를 받으면 보통 이런 식으로 나옵니다.

```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXX"
        crossorigin="anonymous"></script>
```

이 코드는 쉽게 말하면  
“내 사이트에서 애드센스 광고를 불러올게요”  
라고 Google에 알려주는 기본 코드라고 생각하시면 됩니다.

### 어디에 넣어야 할까?

예전에는 `_includes/head.html` 파일을 직접 수정하는 방법도 많이 이야기됐습니다.

그런데 Chirpy에서는 이 방법이 잘못하면 빌드 오류를 일으키거나, 기존 head 설정을 건드릴 수 있어서 초보자에게는 조금 위험합니다.

그래서 더 안전하게 **`_includes/metadata-hook.html`** 파일을 사용하는 방법을 추천합니다.

---

## 2. `_includes/metadata-hook.html` 파일 만들기

이제 GitHub 저장소에서 파일을 하나 만들면 됩니다.

순서는 이렇습니다.

1. 내 GitHub 저장소로 들어갑니다.
2. `_includes` 폴더가 없으면 새로 만듭니다.
3. 그 안에 `metadata-hook.html` 파일을 만듭니다.
4. 아래 코드를 붙여 넣습니다.

```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXX"
        crossorigin="anonymous"></script>
```

여기서 중요한 점은 `ca-pub-XXXXXXXXXXXX` 자리에  
내 애드센스 퍼블리셔 ID를 정확히 넣는 것입니다.

예를 들어 내 코드가 `ca-pub-1372015701075050` 라면 이렇게 써야 합니다.

```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-1372015701075050"
        crossorigin="anonymous"></script>
```

### 주의할 점

아래처럼 쓰면 안 됩니다.

```html
<script async src= https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-"1372015701075050"
     crossorigin="anonymous"></script>
```

이 코드는 따옴표 위치가 잘못되어 있어서 빌드 오류나 광고 미노출 문제가 생길 수 있습니다.

즉, 퍼블리셔 ID는 반드시 **한 덩어리로 정확하게** 넣어야 합니다.

---

## 3. 광고가 실제로 보일 위치 정하기

기본 코드만 넣는다고 광고가 바로 본문에 보이는 것은 아닙니다.

광고가 실제로 나올 위치에는 **광고 단위(ad unit) 코드**를 따로 넣어줘야 합니다.

애드센스에서 광고 단위를 만들면 보통 이런 코드가 나옵니다.

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

이 코드는 글 아래, 글 중간, 사이드바 같은 위치에 광고를 보여주는 역할을 합니다.

---

## 4. 광고 코드는 따로 파일로 빼두는 게 편하다

초보자도 관리하기 쉽게 하려면 광고 코드를 include 파일로 따로 만들어 두는 것이 좋습니다.

예를 들어 `_includes/adsense_post_bottom.html` 파일을 만들고, 그 안에 광고 코드를 넣어두면 됩니다.

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

이렇게 해두면 나중에 광고 위치를 바꾸거나 수정할 때도 훨씬 편합니다.

---

## 5. 포스트 아래에 광고 넣기

이제 포스트 본문 아래쪽에 광고가 나오도록 연결해 줍니다.

보통은 `_layouts/post.html` 파일 쪽에서 본문 아래에 include를 추가하는 방식으로 넣습니다.

예시는 아래와 같습니다.

```liquid
<div class="post-content">
  {{ content }}
</div>

{% if jekyll.environment == 'production' %}
  {% include adsense_post_bottom.html %}
{% endif %}
```

이 설정은 개발 중에는 광고를 숨기고, 실제 배포된 사이트에서만 광고가 보이도록 도와줍니다.

처음에는 이 코드가 낯설어 보여도, 그냥  
“글 본문 아래에 광고 파일을 불러온다”  
정도로 이해하시면 충분합니다.

---

## 6. `ads.txt` 파일도 꼭 추가하자

애드센스를 설정하다 보면 `ads.txt` 이야기가 꼭 나옵니다.

이 파일은 Google에게  
“이 사이트는 이 퍼블리셔가 광고를 운영하는 게 맞아요”  
라고 알려주는 확인용 파일이라고 생각하시면 됩니다.

### 어디에 만들어야 할까?

여기서 말하는 **저장소 루트**는  
내 GitHub 저장소에 들어갔을 때 제일 처음 보이는 화면입니다.

예를 들어 이런 파일들이 보이는 자리입니다.

- `_config.yml`
- `_posts`
- `assets`
- `README.md`

바로 이 위치에 `ads.txt` 파일을 만들면 됩니다.

### 만드는 방법

1. GitHub 저장소 첫 화면으로 갑니다.
2. **Add file → Create new file** 를 누릅니다.
3. 파일 이름에 `ads.txt` 를 입력합니다.
4. AdSense에서 준 내용을 그대로 붙여 넣습니다.
5. 커밋해서 저장합니다.

저장 후에는 아래 주소로 확인할 수 있습니다.

```text
https://mesektok.com/ads.txt
```

이 주소에 내용이 보이면 정상입니다.

---

## 7. 적용 후 꼭 확인하기

파일을 다 만들었다고 바로 끝난 것은 아닙니다.

아래 3가지는 꼭 확인해 보세요.

- GitHub Actions에서 빌드가 성공했는지
- 사이트 소스에 애드센스 기본 코드가 들어갔는지
- `ads.txt` 주소가 정상적으로 열리는지

그리고 광고는 바로 나타나지 않을 수도 있습니다.

보통은 몇 시간 안에 반영되기도 하지만, 경우에 따라 1~2일 정도 걸릴 수도 있습니다.

처음에는 “왜 안 뜨지?” 싶을 수 있는데, 조금 기다려 보는 것도 필요합니다.

---

## 마무리

정리하면 Chirpy 블로그에 애드센스를 붙이는 핵심은 정말 딱 3가지입니다.

- 기본 스크립트는 `_includes/metadata-hook.html`에 넣기
- 광고 단위 코드는 include 파일로 따로 빼기
- `ads.txt`는 저장소 루트에 만들기

처음에는 폴더 이름도 낯설고 파일 위치도 헷갈리지만,  
한 번만 해보면 구조가 금방 이해됩니다.

저처럼 처음에 `head.html` 때문에 빌드 오류를 겪는 분들이라면,  
무리해서 복잡하게 건드리기보다 **안전한 방식으로 하나씩 적용하는 것**이 훨씬 편합니다.
