---
title: "n8n으로 만드는 AI 구매대행 자동화 실무 전자책"
date: 2026-04-09 14:00:00 +0900
categories: [블로그, 자동화]
tags: [n8n, github, jekyll, infographic, data-visualization]
image:
  path: /assets/img/posts/n8n_ai_gumaedaehaeng_ebook/cover.jpg
  alt: n8n자동화 - ai구매대행 자동화실무
---

# n8n으로 만드는 AI 구매대행 자동화 실무 전자책

이 전자책은 아마존 상품 후보 발굴, AI 기반 상품 정리, n8n 자동화 루틴 설계, 마켓 등록 준비까지 이어지는 실무형 흐름을 한 권으로 정리한 입문·실전 가이드다. n8n은 트리거, 데이터 흐름, 조건 분기, 외부 API 호출을 시각적으로 설계할 수 있는 워크플로우 자동화 도구이며, 복잡한 반복 업무를 단계적으로 자동화하는 데 강점이 있다.
상품 연구와 자동화 구성에서는 후보 수집, 상품 상세 수집, 필터링, 마진 계산, 등록 데이터 변환을 각각 분리된 워크플로우로 설계하는 방식이 디버깅과 유지보수에 유리하다.[cite:29][cite:32][cite:35]

## 누구를 위한 책인가

- n8n을 설치했지만 워크플로우가 제대로 돌지 않는 초보 실무자.
- AI를 이용해 상품 후보 발굴, 제목 정리, 카테고리 분류를 자동화하고 싶은 셀러.
- 한 번에 완전 자동화가 아니라, 실제로 동작하는 최소 자동화부터 만들고 싶은 사용자.
## 자동화의 큰 구조

AI 구매대행 자동화는 한 개의 거대한 플로우가 아니라, 여러 개의 작은 서브플로우가 서로 연결되는 구조로 접근해야 안정적이다. n8n에서는 하나의 플로우에 많은 책임을 몰아넣을수록 어디서 오류가 났는지 찾기 어려워지므로, Schedule 또는 Webhook으로 시작하는 분리형 워크플로우가 권장된다.[cite:29][cite:32][cite:35]

권장 구조는 다음 네 단계다.

1. 후보 수집 워크플로우: 어떤 카테고리와 상품군을 오늘 소싱할지 결정.
2. 상품 상세 수집 워크플로우: URL 또는 ASIN을 바탕으로 가격, 리뷰 수, 브랜드, 이미지 등을 수집.
3. 등록 준비 워크플로우: 마진 계산, 한국어 제목 생성, 금칙어 필터링, 마켓 포맷 변환 수행.
4. 성과 점검 워크플로우: 유입·판매 없는 상품을 분리하고 교체 후보를 자동 준비.

## 하루 자동 루틴 설계

실무에서는 하루 루틴을 시간대별로 끊어 두면 관리가 쉬워진다. 예를 들어 새벽에는 후보 발굴과 수집을 돌리고, 저녁에는 성과 점검과 교체 루틴을 돌리는 방식이 효율적이다.

### 아침 루틴

- 05:00 Schedule Trigger 실행.
- 05:05 GPT 또는 Gemini에 카테고리별 후보 키워드 요청.
- 05:10 URL 또는 ASIN 후보 목록 저장.
- 05:20 HTTP Request 노드로 상품 상세 정보 수집.
- 05:40 Code 노드로 표준 스키마 정규화.
- 06:00 금칙어, 위험 카테고리, 브랜드 조건 필터링.
- 06:20 목표 마진 기준 판매가 계산.
- 06:40 등록용 JSON 또는 CSV 생성.
### 저녁 루틴

- 17:00 기존 등록 상품의 유입·판매·오류 현황 조회.
- 17:10 7일 이상 무유입 또는 저성과 상품 후보 분리.
- 17:20 삭제 후보 목록 저장.
- 17:30 신규 후보 상품으로 교체 리스트 생성.
- 17:40 승인 후 재등록 또는 자동 등록 수행.
## n8n 최소 동작 워크플로우

현재 워크플로우가 미동작이라면, 가장 먼저 만들어야 할 것은 "수동 실행해도 끝까지 도는 최소 플로우"다. 이 최소 플로우는 가짜 데이터 1건으로도 검증할 수 있어야 한다.

### 최소 플로우 순서

1. Manual Trigger
2. Set 노드: 테스트용 상품 데이터 입력
3. Code 노드: 판매가 계산
4. OpenAI 또는 Gemini 노드: 제목 정리
5. Set 노드: 출력 형식 통일
6. Google Sheets Append 또는 Notion 저장

이 플로우가 정상 동작하면 그다음부터 HTTP Request, Split In Batches, IF, DB 저장, 등록 API 호출을 순서대로 추가하는 방식이 안전하다.

## 워크플로우 1: 후보 수집

후보 수집 워크플로우의 목적은 "오늘 수집할 상품 후보군을 만드는 것"이다. 여기서 LLM은 실시간 상품 데이터 제공자가 아니라, 검색 후보를 설계하는 조사관 역할로 쓰는 것이 안정적이다.

### 노드 구성

- Schedule Trigger
- Set
- OpenAI Chat Model 또는 Gemini
- Code
- Google Sheets Append Row

### Set 노드 예시 값

```json
{
  "target_category": "자동차용품",
  "target_market": "쿠팡",
  "max_candidates": 30,
  "target_margin": 0.30,
  "exclude_keywords": ["건강", "의약", "성인", "배터리"]
}
```

### GPT 후보 발굴 프롬프트

```text
당신은 해외구매대행 상품 소싱 어시스턴트다.
목표는 한국 마켓에서 판매 가능한 아마존 상품 후보를 찾기 위한 검색 후보군을 만드는 것이다.

조건:
- 카테고리: {{$json.target_category}}
- 판매 채널: {{$json.target_market}}
- 최대 후보 수: {{$json.max_candidates}}
- 제외 키워드: {{$json.exclude_keywords}}
- 기능이 직관적이고 리뷰 검증이 가능한 상품 위주
- 출력은 JSON 배열만

필드:
keyword_en, keyword_ko, search_intent, expected_price_band, reason
```

### Gemini 후보 발굴 프롬프트

```text
역할: 당신은 아마존 카테고리 리서처다.
목표: 아래 카테고리에서 한국 오픈마켓 판매 가능성이 있는 검색 후보를 만든다.

입력:
카테고리={{$json.target_category}}
제외키워드={{$json.exclude_keywords}}
후보수={{$json.max_candidates}}

평가 기준:
- 기능 설명이 쉬운가
- 위험 카테고리가 아닌가
- 리뷰 기반 검증이 가능한가
- 반복 수요 가능성이 있는가

반드시 JSON 배열만 출력.
```

### Code 노드 예시

```javascript
const txt = items[0].json.text || items[0].json.output || '[]';
let arr;
try {
  arr = JSON.parse(txt);
} catch (e) {
  arr = [];
}
return arr.map(x => ({ json: x }));
```

## 워크플로우 2: 상품 상세 수집

아마존은 직접 단순 크롤링이 자주 막히기 때문에, n8n의 HTTP Request 노드로 외부 스크래핑 API 또는 상품 데이터 API를 호출해 JSON을 받는 패턴이 많이 쓰인다.

### 노드 구성

- Execute Workflow Trigger 또는 Webhook
- Google Sheets Read
- Split In Batches
- HTTP Request
- Code
- IF
- Data Store 또는 Airtable 저장

### HTTP Request에서 받아야 할 핵심 필드

- asin
- title
- brand
- price
- currency
- rating
- reviewCount
- availability
- imageUrls
- category
- features
- productUrl[cite:33][cite:36]

### Code 정규화 예시

```javascript
return items.map(item => {
  const d = item.json;
  return {
    json: {
      asin: d.asin || '',
      title: d.title || '',
      brand: d.brand || '',
      price: Number(d.price || 0),
      rating: Number(d.rating || 0),
      reviewCount: Number(d.reviewCount || 0),
      category: d.category || '',
      features: d.features || [],
      productUrl: d.productUrl || '',
      imageUrl: Array.isArray(d.imageUrls) ? d.imageUrls[0] : ''
    }
  };
});
```

## 워크플로우 3: 등록 준비

등록 준비 단계에서는 실데이터를 마켓에 맞는 판매 데이터로 바꾸는 작업을 한다. 핵심은 계산과 문장 생성을 분리하는 것이다. 가격 계산은 Code 노드가 맡고, 제목 정리와 핵심 포인트 요약은 GPT 또는 Gemini가 맡게 설계하는 편이 안정적이다.

### 판매가 계산 로직 예시

판매가 계산에서는 상품 원가, 환율, 배송비, 수수료, 목표 마진을 반영해야 한다. 수식 예시는 다음과 같다.

판매가 = ((원가달러 × 환율) + 예상배송비) ÷ (1 - 수수료율 - 목표마진율)

이 계산은 Code 노드에서 처리하는 것이 안전하다.

```javascript
return items.map(item => {
  const costUsd = Number(item.json.price || 0);
  const rate = 1380;
  const ship = 12000;
  const fee = 0.13;
  const margin = 0.30;
  const sale = Math.round(((costUsd * rate) + ship) / (1 - fee - margin));
  return {
    json: {
      ...item.json,
      exchangeRate: rate,
      shippingCost: ship,
      feeRate: fee,
      marginRate: margin,
      salePrice: sale
    }
  };
});
```

### GPT 상품명 정제 프롬프트

```text
당신은 쿠팡 상품명 편집자다.
입력된 해외 상품 정보를 바탕으로 한국 판매용 상품명을 만든다.

규칙:
- 브랜드명 + 핵심기능 + 사용대상 + 수량 순서 우선
- 과장 광고 금지
- 특수문자 남용 금지
- 45자 이내 3개 버전
- JSON만 출력

입력:
상품명={{$json.title}}
브랜드={{$json.brand}}
카테고리={{$json.category}}
특징={{$json.features}}
```

### Gemini 상세 요약 프롬프트

```text
당신은 이커머스 상세 요약 편집자다.
아래 상품 정보를 한국 소비자가 빠르게 이해할 수 있게 정리하라.

규칙:
- 4개 bullet 포인트
- 각 포인트 22자 이내
- 의료 효능처럼 보이는 표현 금지
- JSON으로만 출력

입력:
title={{$json.title}}
features={{$json.features}}
description={{$json.description}}
```

## 워크플로우 4: 성과 점검과 교체

자동화의 품질은 상품을 얼마나 많이 올리느냐보다, 저성과 상품을 얼마나 빠르게 정리하느냐에서 갈린다. n8n에서는 저녁 시간대에 유입 수, 클릭 수, 주문 수, 오류 상태를 읽어 와서 교체 후보를 만드는 루틴을 별도 운영하는 것이 좋다.

### 삭제 후보 판단 기준 예시

- 7일 이상 유입 0.
- 14일 이상 판매 0.
- 등록 오류 반복 발생.
- 금칙어, 인증 문제, 상표권 위험 감지.

### 위험 판별 프롬프트

```text
당신은 해외구매대행 상품 검수 담당자다.
아래 상품이 한국 오픈마켓 업로드 시 주의가 필요한지 판단하라.

판단 기준:
- 의약품 또는 건강기능식품 오인 가능성
- 인증 필요 가능성
- 위험물 가능성
- 성인용품 여부
- 유명 브랜드 상표권 분쟁 가능성

출력 JSON:
{
  "risk_level": "low|medium|high",
  "should_upload": true,
  "reasons": ["..."],
  "warning_tags": ["..."]
}
```

## 미동작 워크플로우 디버깅

n8n이 안 도는 가장 흔한 이유는 노드 로직이 아니라 데이터 형식 불일치다. Trigger는 되는데 다음 노드가 원하는 JSON 구조를 못 받거나, LLM 응답이 문자열인데 다음 노드는 배열을 기대하는 식의 문제들이 반복된다.

- Trigger는 실행되지만 Set 또는 Expression 참조 필드가 비어 있음.
- LLM 응답이 텍스트인데 다음 노드가 JSON 객체를 기대함.
- HTTP Request 응답이 배열이 아닌데 Split In Batches를 잘못 적용함.
- Code 노드의 return 형식이 n8n item 배열 형식이 아님.
- 하나의 워크플로우에 너무 많은 책임이 들어가 어디서 죽는지 추적이 안 됨.

### 가장 먼저 해야 할 점검

1. Manual Trigger로 시작하는 테스트 플로우를 만든다.
2. Set 노드로 더미 JSON 1건을 직접 넣는다.
3. Code 노드가 정상적으로 값을 계산하는지 확인한다.
4. LLM 노드를 붙이고 응답이 JSON으로 파싱되는지 확인한다.
5. 마지막에 저장 또는 API 호출을 붙인다.

## 실습 1: 30분 안에 살아 있는 테스트 플로우 만들기

### 목표

더미 상품 데이터 1개를 넣고, 제목 생성과 판매가 계산까지 완료한 뒤 Google Sheets에 저장한다.

### 준비물

- n8n 설치 환경
- OpenAI 또는 Gemini API 키
- Google Sheets 연결 계정

### 실습 순서

1. Manual Trigger 추가.
2. Set 노드에 아래 JSON 입력.
3. Code 노드에서 판매가 계산.
4. LLM 노드에서 제목 정리.
5. Code 또는 Set 노드에서 응답 정규화.
6. Google Sheets Append Row로 저장.

### 더미 JSON

```json
{
  "title": "Portable Car Vacuum Cleaner High Power Handheld",
  "brand": "ExampleBrand",
  "category": "자동차용품",
  "features": ["휴대용", "차량 청소", "강한 흡입력"],
  "price": 24.99
}
```

### 성공 기준

- salePrice 필드가 계산됨.
- cleanedTitle 또는 titleCandidates가 생성됨.
- 시트에 한 줄이 저장됨.

## 실습 2: 후보 리스트를 여러 건으로 확장하기

최소 플로우가 성공하면, 다음 단계는 후보 1건이 아니라 10건 이상을 처리하는 것이다. 이때부터 Split In Batches를 도입하고, 각 배치마다 HTTP Request와 필터 노드를 연결한다.
### 확장 체크리스트

- Code 노드가 item 배열을 올바르게 반환하는가.
- Split In Batches 크기가 과하지 않은가.
- 실패한 요청을 IF 노드로 분기해 별도 로깅하는가.
- 재시도 정책이 있는가.

## 실무 운영 팁

실무에서는 완전 자동화보다 반자동이 더 오래 간다. 특히 삭제나 등록은 처음부터 완전자동으로 가지 말고, 승인 시트나 검수 큐를 한 단계 두는 편이 사고를 줄인다.

운영 원칙은 다음과 같다.

- LLM은 판단과 문장화에 쓰고, 실데이터는 HTTP Request로 가져온다.
- 등록 전에는 반드시 금칙어, 위험 카테고리, 상표권 위험 필터를 거친다.
- 삭제는 자동 실행보다 삭제 후보 생성부터 시작한다.
- 데이터 원본, 가공 데이터, 등록 데이터를 분리 저장한다.
## 전자책 활용법

이 책은 한 번 읽고 끝내는 설명서보다, 옆에 두고 하나씩 따라 치는 실습서에 가깝다. 가장 좋은 사용법은 후보 수집 플로우부터 직접 만들고, 각 노드 출력 JSON을 확인하면서 이 책의 예시 프롬프트와 코드 조각을 붙여 넣어 보는 방식이다.

처음부터 "아마존 상품 자동 수집부터 쿠팡 등록까지 전부"를 만들려고 하면 거의 반드시 멈춘다. 반대로 Manual Trigger 기반 1건 테스트 → 10건 확장 → Schedule 자동화 → 성과 점검 플로우 분리 순서로 가면 실무 자동화가 훨씬 빨리 살아난다.

## 체크리스트

### 시작 전

- n8n이 실행 중인가.
- OpenAI 또는 Gemini API 키가 정상 연결되었는가.
- Google Sheets 또는 저장소 계정 연결이 완료되었는가.
- 테스트용 더미 JSON 1건을 준비했는가.

### 구축 중

- 각 노드의 입력과 출력 JSON을 확인했는가.
- Code 노드가 `return [{ json: ... }]` 형식을 지켰는가.
- LLM 응답을 JSON.parse 가능한 구조로 강제했는가.
- HTTP Request 실패 분기를 만들었는가.

### 운영 전

- 금칙어 필터를 넣었는가.
- 삭제는 후보 생성 단계로만 두었는가.
- 판매가 계산식에 수수료와 배송비를 반영했는가.
- 로그 저장 위치를 정했는가.

## 마무리

AI 구매대행 자동화는 결국 "좋은 상품을 찾는 능력"과 "반복 업무를 구조화하는 능력"의 결합이다. n8n은 그 구조화를 눈에 보이게 만드는 도구이고, GPT와 Gemini는 그 안에서 후보 발굴, 제목 정리, 위험 판단, 카테고리 분류를 도와주는 조력자 역할을 한다.

실무에서 중요한 것은 거창한 자동화가 아니라, 오늘 한 건이라도 끝까지 도는 플로우를 만드는 것이다. 그 첫 플로우가 돌아가기 시작하면, 그다음부터는 자동화가 아니라 확장 문제로 바뀐다.
