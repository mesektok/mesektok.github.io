---
title: "다-메세톡 블로그, 드디어 정상화 완료- 블로그 홈판 구축하라"
date: 2026-05-11 19:00:00 +0900
categories: [blog]
tags: [chirpy, github-pages, jekyll]
---

# Sheets에 쌓인 글을 블로그에 자동 발행한다 — n8n B루틴 만들기

---

## "아침에 일어났더니 블로그에 글이 올라가 있었습니다"

3편에서 A루틴을 만들었습니다.

매일 아침 8시, AI가 글을 써서 Google Sheets에 저장해둡니다.
그런데 Sheets에 쌓이기만 하면 — 독자는 볼 수가 없습니다.

오늘 만들 **B루틴**이 그 글을 꺼내서 블로그에 올립니다.

> B루틴 = Google Sheets에서 "대기중" 글을 꺼내 → 블로그에 발행 → 상태를 "발행완료"로 업데이트

A루틴이 글을 쓰고, B루틴이 올립니다.
둘이 합쳐지면 — 완전 자동 블로그가 됩니다.

오늘이 마지막 편입니다. 끝까지 같이 가봅시다.

---

## 1. B루틴 전체 흐름 한눈에

```
① 매일 아침 8시 30분 → 자동으로 깨어남  (Schedule Trigger)
        ↓
② Google Sheets에서 "대기중" 글 한 편 가져오기  (Google Sheets 노드)
        ↓
③ 블로그에 글 발행하기  (WordPress 또는 Blogger 노드)
        ↓
④ 해당 행 상태를 "발행완료"로 업데이트  (Google Sheets 노드)
```

노드 4개짜리 워크플로우입니다.
A루틴(8시)보다 30분 늦게 시작하는 이유 — A루틴이 글을 다 써놓은 뒤에 가져와야 하니까요.

---

## 2. 준비물 확인

---

### ✅ 첫째, 3편에서 만든 Google Sheets

A루틴이 글을 저장하는 그 시트입니다.
1행에 날짜 / 제목 / 내용 / 상태 가 있어야 합니다.
그리고 2행부터 **상태 열에 "대기중"** 이 들어있는 데이터가 있어야 합니다.

아직 없다면 → 3편 워크플로우를 한 번 수동 실행해서 데이터를 채워두세요.

---

### ✅ 둘째, 블로그 연동 준비

사용하는 블로그 플랫폼에 따라 준비물이 다릅니다.

**WordPress 사용하신다면:**
- 워드프레스 주소 (예: `https://내블로그.com`)
- 관리자 아이디 / 비밀번호
- 또는 Application Password (WordPress 관리자 → 사용자 → 프로필 → 하단 앱 비밀번호)

**Blogger 사용하신다면:**
- 구글 계정 (Blogger 연결된 계정)
- Blog ID (Blogger 주소창에서 확인 가능)
- n8n에서 Google OAuth2 연동

> 💡 **어떤 블로그를 쓰시나요?**
> 이 가이드는 두 가지 모두 다룹니다.
> WordPress → STEP 3-A
> Blogger → STEP 3-B
> 해당하는 것만 보시면 됩니다.

---

## 3. 워크플로우 만들기

---

### STEP 1. 새 워크플로우 열기

n8n 화면 오른쪽 상단 **"+ New Workflow"** 클릭.

워크플로우 이름을 지어주세요.

```
B루틴_블로그자동발행
```

---

### STEP 2. 첫 번째 노드 — Schedule Trigger

**"+"** 버튼 클릭 → **"Schedule Trigger"** 선택.

| 항목 | 입력값 |
|---|---|
| Trigger Interval | Days |
| Hour | 8 |
| Minute | 30 |

> A루틴(8:00)보다 30분 뒤인 8시 30분으로 설정합니다.

> 📸 *[화면 캡처 — Schedule Trigger 8:30 설정]*

---

### STEP 3. 두 번째 노드 — Google Sheets (대기중 글 가져오기)

Schedule Trigger 오른쪽 **"+"** 클릭 → **"Google Sheets"** → **"Get Row(s)"** 선택.

설정 패널에서:

| 항목 | 입력값 |
|---|---|
| Credential | 구글 계정 연동 |
| Spreadsheet | A루틴에서 쓰는 그 시트 |
| Sheet | Sheet1 |
| Filters | 상태 = 대기중 |
| Return | First Row Only (첫 번째 행만) |

> 📸 *[화면 캡처 — Google Sheets Get Row 설정]*

> ⚠️ **Filters 설정이 핵심입니다**
> "상태" 열의 값이 "대기중"인 행만 가져옵니다.
> 이미 발행된 글(발행완료)은 건드리지 않습니다.
> 중복 발행을 막는 안전장치입니다.

---

### STEP 4-A. 세 번째 노드 — WordPress 발행

**WordPress 사용자만** 이 단계를 따라하세요.
Blogger 사용자는 → STEP 4-B로 가세요.

**"+"** 클릭 → 검색창에 입력:

```
WordPress
```

**"WordPress"** → **"Create Post"** 선택.

설정 패널에서:

| 항목 | 입력값 |
|---|---|
| Credential | WordPress 주소 + 아이디/비밀번호 입력 |
| Title | ``$json['제목']`` |
| Content | ``$json['내용']`` |
| Status | `publish` (즉시 발행) 또는 `draft` (임시저장) |

> 📸 *[화면 캡처 — WordPress 노드 설정]*

> 💡 **처음엔 `draft`(임시저장)으로 테스트하세요**
> 내용을 확인한 뒤 괜찮으면 `publish`로 바꾸면 됩니다.
> 실수로 이상한 글이 올라가는 걸 막을 수 있습니다.

---

### STEP 4-B. 세 번째 노드 — Blogger 발행

**Blogger 사용자만** 이 단계를 따라하세요.

**"+"** 클릭 → 검색창에 입력:

```
HTTP Request
```

**"HTTP Request"** 선택.

Blogger는 n8n 전용 노드가 없어서 HTTP 방식으로 연결합니다.
복잡해 보이지만 — 아래 값만 그대로 넣으면 됩니다.

| 항목 | 입력값 |
|---|---|
| Method | POST |
| URL | `https://www.googleapis.com/blogger/v3/blogs/여기에BlogID/posts/` |
| Authentication | OAuth2 → Google 계정 연동 |
| Body (JSON) | 아래 참고 |

**Body 입력값:**

```json
{
  "title": "`$json['제목']`",
  "content": "`$json['내용']`"
}
```

> 📸 *[화면 캡처 — HTTP Request 노드 설정]*

> 💡 **Blog ID는 어디서 찾나요?**
> Blogger 관리자 화면 주소창을 보세요.
> `blogger.com/blog/posts/` **숫자** 이 숫자가 Blog ID입니다.

---

### STEP 5. 네 번째 노드 — Google Sheets 상태 업데이트

발행이 끝나면 — Sheets의 해당 행 상태를 "발행완료"로 바꿔야 합니다.
안 바꾸면 내일 B루틴이 같은 글을 또 발행합니다.

**"+"** 클릭 → **"Google Sheets"** → **"Update Row"** 선택.

설정 패널에서:

| 항목 | 입력값 |
|---|---|
| Credential | 구글 계정 연동 |
| Spreadsheet | 같은 시트 |
| Sheet | Sheet1 |
| Row Number | ``$json['row_number']`` |
| 상태 | `발행완료` |

> 📸 *[화면 캡처 — Google Sheets Update Row 설정]*

> ⚠️ **row_number가 중요합니다**
> STEP 3에서 가져온 행 번호를 그대로 씁니다.
> 이게 맞아야 정확한 행이 업데이트됩니다.

---

### STEP 6. 전체 연결 확인

캔버스가 이렇게 되어 있어야 합니다.

```
[Schedule Trigger] → [Google Sheets: 가져오기] → [WordPress 또는 Blogger: 발행] → [Google Sheets: 상태 업데이트]
```

> 📸 *[화면 캡처 — 완성된 B루틴 전체 워크플로우]*

---

### STEP 7. 테스트 실행

**"Test Workflow"** 버튼 클릭.

성공 시 확인할 것 두 가지:

1. 블로그에 글이 올라갔는지 확인
2. Google Sheets 해당 행의 상태가 "발행완료"로 바뀌었는지 확인

둘 다 됐으면 — 완성입니다. 🎉

---

## 4. 오류가 났을 때

---

### ❌ "대기중" 글이 없다고 오류 날 때

Sheets에 데이터가 없거나 상태 열 값이 정확하지 않은 겁니다.
Sheets를 열어서 상태 열에 **"대기중"** 이 정확히 입력됐는지 확인하세요.
(띄어쓰기, 오타 주의)

---

### ❌ WordPress 연결 오류

Application Password가 정확한지 확인하세요.
WordPress 주소 끝에 `/` 슬래시가 있는지 확인하세요.
예: `https://내블로그.com/` (슬래시 있어야 함)

---

### ❌ Blogger 403 오류 (권한 없음)

Google OAuth2 Credential을 다시 연동해보세요.
Blog ID가 정확한지 다시 확인하세요.

---

### ❌ 상태가 "발행완료"로 안 바뀔 때

Update Row 노드의 row_number 값이 제대로 연결됐는지 확인하세요.
``$json['row_number']`` 가 정확히 입력됐는지 보세요.

---

## 5. 워크플로우 저장 및 활성화

테스트 성공 후 두 가지 마무리:

**저장** → 오른쪽 상단 **"Save"** 클릭

**활성화** → 오른쪽 상단 토글 스위치 **켜짐(Active)** 으로 변경

```
● Active
```

> 📸 *[화면 캡처 — Active 토글 스위치]*

---

## 완성 — 진짜 자동 블로그가 됐습니다

4편에 걸쳐서 만든 것을 한 번 정리해봅시다.

```
매일 아침 8:00  →  A루틴 시작
                    Gemini가 블로그 글을 씁니다
                    Google Sheets에 저장합니다
                            ↓
매일 아침 8:30  →  B루틴 시작
                    Sheets에서 "대기중" 글을 꺼냅니다
                    블로그에 자동 발행합니다
                    상태를 "발행완료"로 바꿉니다
```

코딩 한 줄 없이 만든 겁니다.
API 키가 뭔지도 몰랐던 분이 여기까지 왔습니다.

저도 처음엔 그랬습니다.

---

## 4편 시리즈 마무리 — 함께 걸어온 길

| 편 | 제목 | 핵심 |
|---|---|---|
| 1편 | Gemini API 키 발급 | 출입증 만들기 |
| 2편 | n8n에 Gemini 연결 | 시동 걸기 |
| 3편 | A루틴 — AI가 글 쓰기 | 엔진 작동 |
| 4편 | B루틴 — 블로그 자동 발행 | 완전 자동화 |

---

이 시리즈가 도움이 되셨다면 — 댓글 한 줄이 큰 힘이 됩니다.

저와 같은 시니어 독자분들에게도 공유해주시면 더 좋고요.
함께 배우는 사람이 많을수록 — 저도 더 자세히 쓸 수 있습니다.

다음엔 더 쉽게, 더 유용한 내용으로 돌아오겠습니다.

---

*다메섹 드림*
