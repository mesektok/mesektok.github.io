---
title: "GitHub Pages에서 알려주는 '네이버 블로그 자동 포스팅 - 쿠키 만료 대응 절차'"
date: 2026-05-14 09:00:00 +0900
categories: [도구]
tags: [GitHub Pages, Jekyll, 블로그, 빌드 실패, GitHub Actions]
description: "67세 교수가 겪은 n8n 자동화 문제해결 방법"
---
# 네이버 블로그 자동 포스팅 - 쿠키 만료 대응 절차

> 작성일: 2026-05-14  
> 대상: 워플-C (네이버 블로그 자동 포스팅)  
> 발생 주기: 약 14시간 이상 미사용 시

---

## 1. 문제 증상

워플-C 실행 시 아래 에러 발생:

| 에러 메시지 | 의미 |
|------------|------|
| `"session expired"` | 네이버 로그인 세션 만료 |
| `"no cookies"` | 쿠키 파일 없음 또는 삭제됨 |

**n8n 워크플로우에서는:**
- `HTTP - 네이버 포스팅 요청` 노드 → 실패
- `시트 업데이트 - 발행 실패` 분기로 이동

---

## 2. 원인

네이버 블로그는 공식 REST API가 없어 **쿠키 기반 세션 인증** 방식 사용.  
일정 시간(약 14시간) 경과 시 세션이 자동 만료됨.

---

## 3. 대응 절차

### STEP 1. 현재 서버 종료
```
Python 서버 실행 중인 터미널 창에서:
Ctrl + C
```

### STEP 2. 쿠키 재발급
```bash
cd C:\Users\박성태\Downloads
python naver_save_cookies.py
```

- 브라우저 자동 실행됨
- 네이버 로그인 진행
- 로그인 완료 → 쿠키 자동 저장
- 터미널에 `cookies saved` 메시지 확인

### STEP 3. 포스팅 서버 재시작
```bash
cd C:\Users\박성태
python naver_poster_final.py
```

아래 메시지 확인:
```
Naver Blog Posting API Server / Port: 5001
* Running on http://127.0.0.1:5001
```

### STEP 4. 워플-C 재실행
```
n8n → 워플-C
→ Execute Workflow 버튼 클릭
```

---

## 4. 빠른 대응 요약

```
① Ctrl+C (서버 종료)
② python naver_save_cookies.py (쿠키 재발급)
③ python naver_poster_final.py (서버 재시작)
④ n8n 워플-C 재실행
```

---

## 5. 파일 위치 정리

| 파일명 | 위치 | 역할 |
|--------|------|------|
| `naver_save_cookies.py` | `C:\Users\박성태\Downloads\` | 쿠키 재발급 |
| `naver_poster_final.py` | `C:\Users\박성태\` | 포스팅 서버 (포트 5001) |

---

## 6. 장기 해결 방안 (선택)

매번 수동 대응이 번거로울 경우:

### 방법 A: Windows 작업 스케줄러 자동화
```
매일 07:50 자동 실행:
1. naver_save_cookies.py
2. naver_poster_final.py
```

### 방법 B: BIOS Wake + 자동 실행
```
기존 BIOS Wake (07:55) 활용
→ 시작프로그램에 서버 등록
→ PC 켜지면 자동 서버 시작
```

### 방법 C: 쿠키 유효기간 연장
```
naver_save_cookies.py 수정
→ 로그인 유지(30일) 옵션 체크
→ 쿠키 만료 주기 늘리기
```

---

## 7. 에러 체크리스트

워플-C 실패 시 순서대로 확인:

```
□ Python 서버 실행 중? (포트 5001)
  → docker ps 또는 터미널 확인

□ 쿠키 만료?
  → Python 터미널 로그 확인
  → "session expired" / "no cookies" 메시지 여부

□ 네이버 계정 상태?
  → 로그인 제한 또는 보안 알림 여부

□ 시트 데이터 정상?
  → 발행대기 행 존재 여부 확인
```

---

*오늘도  화이팅입니다.!!! *
