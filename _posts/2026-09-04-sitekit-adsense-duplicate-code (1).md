---
layout: post
title: "Site Kit이 '이미 AdSense 코드가 있다'고 할 때, 테마 functions.php 어디를 봐야 하나"
date: 2026-09-04 15:30:00 +0900
categories: [트러블슈팅, 워드프레스]
tags: [Site Kit, AdSense, functions.php, ads.txt, 워드프레스, 애드센스승인]
description: "직접 만든 워드프레스 테마에 Site Kit을 붙였더니 '이미 AdSense 코드가 있다'는 경고. header.php에는 없었다. 범인은 functions.php의 wp_head 훅이었다."
---

## 증상

워드프레스 사이트에 Google Site Kit을 설치하고 AdSense를 연결하는데, 이런 경고가 떴다.

> 이 계정의 사이트에 이미 AdSense 코드가 있습니다. AdSense를 최대한 활용하려면 Site Kit을 사용하여 코드를 삽입하는 것이 좋습니다. Site Kit에서 삽입한 코드와 충돌하지 않도록 기존 AdSense 코드를 제거해야 합니다.

애드센스 사이트 목록에서는 한 달 가까이 "준비 중"에 멈춰 있었고, Ads.txt 상태는 "찾을 수 없음"이었다.

"기존 코드를 제거하라"는 말은 알겠는데, 문제는 **그 코드가 어디 있는지**였다. 몇 달 전에 넣은 걸 잊어버린 것이다.

## 헤맨 순서

**1. header.php부터 열었다.** 애드센스 코드는 `<head>` 안에 넣으라고 하니 당연히 여기 있을 줄 알았다. 없었다. `<head>` 안에는 `<?php wp_head(); ?>` 한 줄뿐이었다.

**2. footer.php도 확인했다.** 없었다.

**3. 광고 로직이 있는 inc/ads.php를 열었다.** 본문 중간에 광고 단위를 넣는 코드는 있었지만, `adsbygoogle.js`를 불러오는 로더 스크립트는 없었다.

여기서 잠깐 막혔다. 파일 세 개를 다 봤는데 없다면 플러그인인가?

**4. functions.php를 열었다.** 맨 아래에 있었다.

```php
/* -------------------------------------------------
 * 4) 애드센스 헤더 스크립트 (전역, wp_head)
 * ------------------------------------------------- */
function gt_adsense_head_script() {
	if ( empty( GT_ADSENSE_CLIENT_ID ) ) return;
	printf(
		'<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=%s" crossorigin="anonymous"></script>' . "\n",
		esc_attr( GT_ADSENSE_CLIENT_ID )
	);
}
add_action( 'wp_head', 'gt_adsense_head_script' );
```

## 원인

`header.php`의 `<?php wp_head(); ?>`는 빈 줄이 아니다. 워드프레스가 이 자리에 **다른 파일들이 등록해 둔 내용을 모아서 출력**한다. `add_action( 'wp_head', ... )`로 등록된 함수는 전부 여기로 흘러들어온다.

그러니 `<head>` 안에 뭔가 들어가 있는데 header.php에 안 보인다면, 후보는 두 곳이다.

| 후보 | 찾는 방법 |
|---|---|
| functions.php (및 그 안에서 `require`하는 inc/ 파일들) | `wp_head`, `adsbygoogle`, `ca-pub` 세 단어로 검색 |
| 헤더 삽입 플러그인 (WPCode, Insert Headers and Footers, Ad Inserter 등) | 플러그인 목록에서 확인 |

내 경우는 테마를 직접 만들면서 functions.php에 넣어 둔 것이었다. 몇 달 지나니 내가 넣은 것도 기억이 안 났다.

## 해결

### 1) functions.php의 헤더 스크립트 블록 비활성화

삭제하지 않고 주석으로 막았다. 나중에 Site Kit을 빼고 싶을 때 되돌리기 쉽다.

```php
/* -------------------------------------------------
 * 4) 애드센스 헤더 스크립트 (전역, wp_head) — Site Kit으로 대체, 비활성화
 * -------------------------------------------------
function gt_adsense_head_script() {
	if ( empty( GT_ADSENSE_CLIENT_ID ) ) return;
	printf(
		'<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=%s" crossorigin="anonymous"></script>' . "\n",
		esc_attr( GT_ADSENSE_CLIENT_ID )
	);
}
add_action( 'wp_head', 'gt_adsense_head_script' );
*/
```

첫 줄의 `*/`를 지우고 블록 끝에 `*/`를 넣어 통째로 주석 처리한 형태다. 안에 `*/`가 또 없으니 문제없다.

**건드리지 않은 것:**
- `inc/ad-settings.php`의 `GT_ADSENSE_CLIENT_ID` 상수 — 본문 광고 단위가 이 값을 쓴다.
- `inc/ads.php`의 `<ins class="adsbygoogle">` 출력 코드 — 이건 "광고 자리"다. 로더 스크립트를 Site Kit이 헤더에 한 번 넣어 주면 그대로 동작한다.

### 2) 확인

캐시를 비우고, 시크릿 창에서 사이트를 열어 `Ctrl+U` → `adsbygoogle.js` 검색. **0건**이면 정리된 것이다.

Site Kit 화면으로 돌아가니 경고 문구와 토글이 사라져 있었다. "AdSense에서 사이트 검토" 버튼을 눌렀다.

### 3) 내친김에 ads.txt

Ads.txt "찾을 수 없음"은 별개 문제지만 같이 잡았다. 호스팅 파일 관리자에서 `public_html`(`wp-config.php`가 있는 최상위 폴더)에 `ads.txt`를 만들고 한 줄:

```
google.com, pub-XXXXXXXXXXXXXXXX, DIRECT, f08c47fec0942fa0
```

`pub-` 뒤 16자리는 애드센스 코드의 `ca-pub-` 뒤 숫자를 그대로 쓴다(`ca-`만 뺀다). 마지막 값은 구글 공통이라 모든 사이트가 같다.

브라우저에서 `https://사이트주소/ads.txt`를 열어 그 줄이 보이면 끝.

## 정리

- Site Kit이 "이미 코드가 있다"고 하는데 header.php에 없으면 → **functions.php에서 `wp_head` 검색**
- `<?php wp_head(); ?>`는 다른 파일이 등록한 내용의 **출구**다. 원인은 그 파일들에 있다.
- 지우지 말고 주석으로 막아라. 되돌릴 일이 생긴다.
- 광고 단위(`<ins>`)와 로더 스크립트(`adsbygoogle.js`)는 다른 것이다. Site Kit이 대신하는 건 로더 쪽이다.
- 같은 화면에 있는 Ads.txt 경고는 이참에 같이 처리하자. 파일 하나, 한 줄이다.

검토 결과는 며칠에서 몇 주 걸린다고 한다. 결과가 나오면 이 글에 덧붙이겠다.

— 다메섹 교수
