---
title: "네이버-포스팅-실패-Gmail-알림설정-완료보고서-다메세톡블로그"
date: 2026-05-13 08:00:00 +0900
categories: [blog]
tags: [chirpy, github-pages, jekyll]
---

---

## 📌 추가된 기능 요약

네이버 블로그 자동 포스팅 실패 시 **Gmail로 자동 알림**이 발송되도록
Workflow C에 노드 3개를 추가했습니다.

---

## 🔧 추가된 노드 구조

```
HTTP - 네이버 포스팅 요청
        ↓
      If1 ($json.success = true)
      ├─ true  → 시트 업데이트 - 발행 성공 → 30분 대기
      └─ false → 시트 업데이트 - 발행 실패
                        ↓
                If ($json.message contains "no cookies")
                ├─ true  → Gmail 알림 발송 → 30분 대기
                └─ false → 30분 대기 (기타 오류)
```
<img width="786" height="602" alt="image" src="https://github.com/user-attachments/assets/255d9bf7-8fe3-4d7d-b8df-753472e378b0" />

---

## 📧 Gmail 알림 설정 내용

| 항목 | 내용 |
|------|------|
| 발송 계정 | enfpguru@gmail.com |
| 수신 주소 | enfpguru@gmail.com |
| 제목 | [긴급] 네이버 쿠키 만료 - 갱신 필요 |
| 발송 조건 | 포스팅 실패 + `no cookies` 메시지 감지 시 |

---

## 📬 알림 수신 후 조치 절차

Gmail에서 **"[긴급] 네이버 쿠키 만료"** 메일 수신 시:

**① CMD 열기**
```cmd
cd C:\Users\박성태
```

**② 쿠키 갱신 스크립트 실행**
```cmd
python naver_save_cookies.py
```

**③ 브라우저에서 네이버 로그인**
- 자동으로 Chrome 브라우저 열림
- 네이버 로그인 진행
- 로그인 완료 후 CMD 창에서 **Enter** 입력

**④ 쿠키 저장 확인**
```
✅ 쿠키 저장 완료! (XX개)
```
메시지 확인

**⑤ Flask 서버 재시작**
```cmd
Ctrl+C
python naver_poster_final.py
```

**⑥ n8n Workflow C 재실행**
- `localhost:5678` 접속
- Workflow C 열기
- **Execute workflow** 클릭
- 포스팅 성공 확인

---

## ⚙️ Gmail OAuth2 설정 정보

| 항목 | 내용 |
|------|------|
| Google Cloud 프로젝트 | n8n-blog-2 |
| OAuth 클라이언트 이름 | n8n Gmail |
| 리디렉션 URI | http://localhost:5678/rest/oauth2-credential/callback |
| n8n Credential 이름 | Gmail account |

---
<img width="962" height="617" alt="image" src="https://github.com/user-attachments/assets/08aa9181-94dd-4640-8989-344d4aef061a" />

## 📅 전체 자동화 운영 체계 (최종)

| 시간 | 자동화 내용 |
|------|-------------|
| 매일 오전 (PC 자동 켜짐) | A루틴: 콘텐츠 생성 → 구글 시트 저장 |
| 매일 오전 | Workflow C: 시트에서 읽어 네이버 포스팅 (1시간 간격) |
| 포스팅 실패 시 | Gmail 자동 알림 발송 |
| 알림 수신 시 | naver_save_cookies.py 실행 → 쿠키 갱신 (약 3분) |

---

## ✅ 현재 정상 작동 확인 항목

- [x] Workflow A (콘텐츠 생성) 정상 실행 중
- [x] Workflow C (네이버 포스팅) 정상 실행 중
- [x] naver_session.json 쿠키 갱신 완료
- [x] Gmail 알림 노드 추가 완료
- [x] naver_save_cookies.py 준비 완료
