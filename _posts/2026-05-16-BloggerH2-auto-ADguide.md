---
title: "Blogger 템플릿 H2 자동 광고 삽입 완전 가이드 — 빈 공간 없이 운영하는 법"
date: 2026-054-16 09:00:00 +0900
categories: [blog]
tags: [chirpy, github-pages, jekyll]
---

> 이 글은 Easypress 계열 Blogger 템플릿을 사용하는 분들을 위한 실전 가이드입니다.  
> 포스트 본문의 H2 제목마다 AdSense 광고가 자동 삽입되는 구조와,  
> 광고 미로드 시 발생하는 **빈 공간 제거 방법**을 단계별로 정리합니다.


## ① 이 템플릿의 광고 자동 삽입 구조

Easypress 계열 Blogger 템플릿에는 **본문 내 H2 태그마다 광고를 자동으로 삽입하는 JavaScript**가 내장되어 있습니다.

포스트 구조가 다음과 같다면:

```
H2: "① 개요"           → 광고 자동 삽입
내용...

H2: "② 설정 방법"      → 광고 자동 삽입
내용...

H2: "③ 마무리"         → 광고 자동 삽입
```

H2 개수만큼 광고 컨테이너(`div.auto-inserted-ad`)가 자동 생성됩니다.

---

## ② 왜 빈 공간이 생기는가

AdSense는 한 페이지의 광고 노출 수를 알고리즘으로 제한합니다.

| 상황 | 결과 |
|------|------|
| H2가 5개 있음 | 광고 컨테이너 5개 생성 |
| AdSense 실제 노출 | 2~3개만 채움 |
| 나머지 컨테이너 | 광고 없이 **빈 공간으로 남음** |

즉, 광고 코드는 삽입되었지만 AdSense가 해당 슬롯에 광고를 채우지 않으면 빈 박스가 남는 것입니다.

---

## ③ 광고단위 설정 위치

`auto-inserted-ad`는 **CSS 클래스 이름**일 뿐입니다. 광고단위를 직접 넣는 곳이 아닙니다.

실제 광고단위(AdSense 슬롯 ID)는 **템플릿 레이아웃의 위젯**에서 설정합니다.

### 설정 경로

```
Blogger 대시보드 → 레이아웃 → "본문 내 광고" 위젯 → 편집
```

### 위젯 내부 코드

```html
<div id="adsense-info"
     data-ad-client="ca-pub-XXXXXXXXXXXXXXXX"   ← 내 게시자 ID (변경 불필요)
     data-ad-slot="XXXXXXXXXX"                   ← ✅ 광고단위 ID (여기만 교체)
     data-ad-format="auto"
     data-full-width-responsive="true">
</div>
```

### 신규 광고단위 교체 순서

1. AdSense → **광고** → **광고단위** → 새 광고단위 만들기
2. 유형: **디스플레이 광고 / 반응형** 선택
3. 생성된 `data-ad-slot` 번호 복사
4. 위젯 편집 화면에서 기존 슬롯 번호와 교체
5. 저장

---

## ④ 빈 공간 제거 코드 적용

**적용 위치:** 템플릿 HTML에서 `</body>` 태그 바로 위

### CSS — 광고 미로드 즉시 숨김

```html
<style>
/* AdSense가 채우지 않은 광고 슬롯 즉시 숨김 */
.auto-inserted-ad ins.adsbygoogle[data-ad-status="unfilled"] {
  display: none !important;
  height: 0 !important;
}
</style>
```

### JavaScript — 3초 후 빈 컨테이너 확인 후 제거

```html
<script>
setTimeout(function() {
  document.querySelectorAll('.auto-inserted-ad').forEach(function(ad) {
    var ins = ad.querySelector('ins.adsbygoogle');
    if (!ins || ins.offsetHeight === 0) {
      ad.style.cssText = 'display:none !important; margin:0 !important; padding:0 !important;';
    }
  });
}, 3000); // 광고 로드 대기 후 3초 체크
</script>
```

> **CSS와 JavaScript 두 가지를 함께 적용하는 것을 권장합니다.**  
> CSS는 `data-ad-status` 속성이 있을 때만 작동하고,  
> JavaScript는 높이(offsetHeight)로 판단하여 보완합니다.

---

## ⑤ 건드리지 않아도 되는 항목

| 항목 | 이유 |
|------|------|
| `auto-inserted-ad` 클래스 | JavaScript가 자동 생성, 수정 불필요 |
| 광고 삽입 스크립트 본체 | 정상 작동 중 |
| `data-ad-client` | 내 게시자 ID, 변경 불필요 |

---

## ⑥ 정리 요약

```
[H2 태그 감지]
     ↓
[auto-inserted-ad div 자동 생성]
     ↓
[AdSense 슬롯 광고 요청]
     ↓
[채워짐 → 광고 표시]  or  [미채움 → 빈 공간]
                                    ↓
                          [CSS + JS로 자동 숨김] ✅
```

광고단위를 바꾸고 싶을 때는 레이아웃 위젯에서 `data-ad-slot` 번호만 교체하면 전체 H2 광고 슬롯에 일괄 반영됩니다.

---

*이 글이 도움이 되셨다면 댓글로 알려주세요.*  
*Blogger 템플릿 커스터마이징 관련 질문은 언제든지 환영합니다.*
