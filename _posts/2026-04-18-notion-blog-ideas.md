---
layout: post
title: "Notion으로 블로그 글감 관리하는 방법 (초보, 시니어용)"
date: 2026-04-18
categories: [도구, Notion]
tags: [노션, 블로그, 글감관리, 초보, 시니어블로그]
---

블로그를 시작하고 가장 먼저 막힌 곳이 있습니다.

**"다음에 뭘 써야 하지?"**

글 하나 올리고 나면 머릿속이 하얗게 됩니다.  
그 문제를 Notion으로 해결했습니다.
그럼 노~션 뭔소리지...

---

## Notion이 뭔가요?

쉽게 말하면 **온라인 메모장 + 표 + 할일 관리**가 합쳐진 무료 도구입니다.  
PC, 스마트폰 어디서든 쓸 수 있습니다.

👉 [notion.com](https://notion.so) — 구글 계정으로 가입 가능

---
<svg width="100%" viewBox="0 0 680 720" xmlns="http://www.w3.org/2000/svg" style="max-width:680px;display:block;margin:2rem auto;">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
    <style>
      .ig-bg-header { fill:#0F6E56; }
      .ig-bg-card   { fill:#f5f5f3; }
      .ig-bg-row1   { fill:#ffffff; }
      .ig-bg-row2   { fill:#f0f0ee; }
      .ig-pill-done { fill:#1D9E75; }
      .ig-pill-wip  { fill:#BA7517; }
      .ig-pill-idea { fill:#185FA5; }
      .ig-pill-pub  { fill:#534AB7; }
      .ig-step1     { fill:#534AB7; }
      .ig-step2     { fill:#BA7517; }
      .ig-step3     { fill:#993C1D; }
      .ig-card-str  { fill:none; stroke:#cccbc4; stroke-width:0.5; }
      .ig-divider   { stroke:#cccbc4; stroke-width:0.5; }
      .ig-dash      { stroke:#cccbc4; stroke-width:1; stroke-dasharray:4 4; }
      .ig-arr       { stroke:#888780; stroke-width:1.5; fill:none; }
      .ig-th        { font-family:sans-serif; font-size:14px; font-weight:600; fill:#2C2C2A; }
      .ig-ts        { font-family:sans-serif; font-size:12px; font-weight:400; fill:#5F5E5A; }
      .ig-tw        { font-family:sans-serif; font-size:12px; font-weight:500; fill:#ffffff; }
      .ig-footer    { font-family:sans-serif; font-size:11px; fill:#B4B2A9; }
    </style>
  </defs>
  <!-- Header -->
  <rect x="40" y="28" width="600" height="64" rx="12" class="ig-bg-header"/>
  <text class="ig-th" x="340" y="53" text-anchor="middle" dominant-baseline="central" font-size="15" fill="#ffffff">Notion으로 블로그 글감 관리하는 방법</text>
  <text class="ig-ts" x="340" y="75" text-anchor="middle" dominant-baseline="central" fill="#9FE1CB">딱 3단계 — 초보자용</text>
  <!-- Vertical guide line -->
  <line x1="340" y1="120" x2="340" y2="660" class="ig-dash"/>
  <!-- ── STEP 1 badge ── -->
  <rect x="260" y="114" width="160" height="36" rx="18" class="ig-step1"/>
  <text class="ig-tw" x="340" y="132" text-anchor="middle" dominant-baseline="central">1단계 — 테이블 만들기</text>
  <!-- Step 1 card -->
  <rect x="40" y="162" width="600" height="210" rx="10" class="ig-bg-card ig-card-str"/>
  <!-- icon + desc -->
  <rect x="60" y="180" width="36" height="36" rx="8" class="ig-step1"/>
  <text x="78" y="198" text-anchor="middle" font-size="15">📋</text>
  <text class="ig-th" x="108" y="191" dominant-baseline="central" font-size="13" fill="#2C2C2A">Notion 새 페이지 → Table 선택</text>
  <text class="ig-ts" x="108" y="208" dominant-baseline="central">아래처럼 컬럼 3개를 만듭니다</text>
  <!-- Table header row -->
  <rect x="60" y="225" width="560" height="28" rx="4" class="ig-step1"/>
  <text class="ig-tw" x="175" y="239" text-anchor="middle" dominant-baseline="central" font-size="12">제목 아이디어</text>
  <text class="ig-tw" x="390" y="239" text-anchor="middle" dominant-baseline="central" font-size="12">상태</text>
  <text class="ig-tw" x="550" y="239" text-anchor="middle" dominant-baseline="central" font-size="12">메모</text>
  <line x1="290" y1="225" x2="290" y2="319" class="ig-divider"/>
  <line x1="470" y1="225" x2="470" y2="319" class="ig-divider"/>
  <!-- Table row 1 -->
  <rect x="60" y="253" width="560" height="22" class="ig-bg-row1" style="stroke:#cccbc4;stroke-width:0.5;"/>
  <text class="ig-ts" x="80" y="264" dominant-baseline="central">Git 처음 설치한 날</text>
  <rect x="325" y="257" width="108" height="16" rx="8" class="ig-pill-done"/>
  <text class="ig-tw" x="379" y="265" text-anchor="middle" dominant-baseline="central" font-size="10">작성완료</text>
  <text class="ig-ts" x="482" y="264" dominant-baseline="central">삽질 3시간</text>
  <!-- Table row 2 -->
  <rect x="60" y="275" width="560" height="22" class="ig-bg-row2" style="stroke:#cccbc4;stroke-width:0.5;"/>
  <text class="ig-ts" x="80" y="286" dominant-baseline="central">애드센스 신청 후기</text>
  <rect x="325" y="279" width="108" height="16" rx="8" class="ig-pill-wip"/>
  <text class="ig-tw" x="379" y="287" text-anchor="middle" dominant-baseline="central" font-size="10">작성중</text>
  <text class="ig-ts" x="482" y="286" dominant-baseline="central">반려 대비 추가</text>
  <!-- Table row 3 -->
  <rect x="60" y="297" width="560" height="22" class="ig-bg-row1" style="stroke:#cccbc4;stroke-width:0.5;"/>
  <text class="ig-ts" x="80" y="308" dominant-baseline="central">Notion 쓰는 이유</text>
  <rect x="325" y="301" width="108" height="16" rx="8" class="ig-pill-idea"/>
  <text class="ig-tw" x="379" y="309" text-anchor="middle" dominant-baseline="central" font-size="10">아이디어</text>
  <text class="ig-ts" x="482" y="308" dominant-baseline="central">이 글!</text>
  <!-- Status flow -->
<text class="ig-ts" x="340" y="344" text-anchor="middle">상태 4단계 :</text>
<rect x="58"  y="354" width="108" height="22" rx="11" class="ig-pill-idea"/>
<text class="ig-tw" x="112" y="365" text-anchor="middle" dominant-baseline="central">아이디어</text>
<line x1="168" y1="365" x2="188" y2="365" class="ig-arr" marker-end="url(#arrow)"/>
<rect x="190" y="354" width="108" height="22" rx="11" class="ig-pill-wip"/>
<text class="ig-tw" x="244" y="365" text-anchor="middle" dominant-baseline="central">작성중</text>
<line x1="300" y1="365" x2="320" y2="365" class="ig-arr" marker-end="url(#arrow)"/>
<rect x="322" y="354" width="108" height="22" rx="11" class="ig-pill-done"/>
<text class="ig-tw" x="376" y="365" text-anchor="middle" dominant-baseline="central">작성완료</text>
<line x1="432" y1="365" x2="452" y2="365" class="ig-arr" marker-end="url(#arrow)"/>
<rect x="454" y="354" width="108" height="22" rx="11" class="ig-pill-pub"/>
<text class="ig-tw" x="508" y="365" text-anchor="middle" dominant-baseline="central">발행</text>
  <!-- ── STEP 2 badge ── -->
  <rect x="260" y="398" width="160" height="36" rx="18" class="ig-step2"/>
  <text class="ig-tw" x="340" y="416" text-anchor="middle" dominant-baseline="central">2단계 — 즉시 입력</text>
  <!-- Step 2 card -->
  <rect x="40" y="446" width="600" height="96" rx="10" class="ig-bg-card ig-card-str"/>
  <rect x="60" y="464" width="36" height="36" rx="8" class="ig-step2"/>
  <text x="78" y="488" text-anchor="middle" font-size="15">📱</text>
  <text class="ig-th" x="108" y="477" dominant-baseline="central" font-size="13" fill="#2C2C2A">산책 중에도, 잠들기 전에도</text>
  <text class="ig-ts" x="108" y="494" dominant-baseline="central">떠오른 주제를 바로 스마트폰 Notion에 입력</text>
  <rect x="60" y="510" width="560" height="22" rx="6" fill="#ffffff" style="stroke:#cccbc4;stroke-width:0.5;"/>
  <text class="ig-ts" x="340" y="521" text-anchor="middle" dominant-baseline="central">PC에서 열면 그대로 있습니다 — 현재 글감 23개 쌓아뒀습니다</text>
  <!-- ── STEP 3 badge ── -->
  <rect x="260" y="556" width="160" height="36" rx="18" class="ig-step3"/>
  <text class="ig-tw" x="340" y="574" text-anchor="middle" dominant-baseline="central">3단계 — 매주 1개 선택</text>
  <!-- Step 3 card -->
  <rect x="40" y="604" width="600" height="80" rx="10" class="ig-bg-card ig-card-str"/>
  <rect x="60" y="622" width="36" height="36" rx="8" class="ig-step3"/>
  <text x="78" y="646" text-anchor="middle" font-size="15">📅</text>
  <text class="ig-th" x="108" y="635" dominant-baseline="central" font-size="13" fill="#2C2C2A">매주 월요일 — 아이디어 목록에서 1개 선택</text>
  <text class="ig-ts" x="108" y="652" dominant-baseline="central">상태를 "작성중"으로 바꾸고 씁니다 → 막막함이 사라집니다</text>
  <!-- Footer -->
<text class="ig-footer" x="340" y="702" text-anchor="middle">Git도 몰랐던 교수의 애드센스 도전기 — mesektok.com</text>
</svg>
## 제가 쓰는 방법 — 딱 3단계

### 1단계 — 글감 수집 테이블 만들기

Notion에서 새 페이지 열고 → **Table** 선택

아래처럼 컬럼을 만듭니다:

| 제목 아이디어 | 상태 | 메모 |
|---|---|---|
| Git 처음 설치한 날 | 작성완료 | 삽질 3시간 |
| 애드센스 신청 후기 | 작성중 | 반려 대비 내용 추가 |
| Notion 쓰는 이유 | 아이디어 | 이 글! |

상태 컬럼은 **아이디어 → 작성중 → 작성완료 → 발행** 4단계로 설정합니다.

---

### 2단계 — 글감이 생기면 즉시 입력

산책 중에도, 잠들기 전에도  
**떠오른 주제를 바로 스마트폰 Notion에 입력**합니다.

나중에 PC에서 열면 그대로 있습니다.  
저는 이렇게 현재 글감을 **23개** 쌓아뒀습니다.

---

### 3단계 — 매주 월요일 1개 선택

테이블을 열고 **"아이디어" 상태인 것 중 1개** 골라서  
상태를 "작성중"으로 바꾸고 씁니다.

계획이 생기니 막막함이 사라집니다.

---

## 초보에게 딱 좋은 이유

- 설치 불필요 — 브라우저에서 바로 사용
- 무료로 충분
- 표 만드는 것만 알면 됩니다
- AI(ChatGPT, Claude)에게 제목 아이디어 받아서 바로 붙여넣기 가능
- 또 없던가?
---

## 마치며

처음엔 메모앱, 카카오톡 나에게 보내기, 포스트잇으로 관리했습니다.  
다 흩어졌습니다. 지금도 하조!!!

Notion 하나로 모은 뒤로  
**"다음에 뭘 쓰지"라는 고민이 없어졌습니다.**

다음 글에서는 AI로 초안을 만드는 방법을 공개합니다.

---

*📌 [도전기 시리즈 전체 보기](/categories/도전기)*
