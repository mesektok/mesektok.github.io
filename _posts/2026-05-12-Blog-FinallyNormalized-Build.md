---
title: "다-메세톡 블로그, 드디어 정상화 완료- 블로그 홈판 구축하라"
date: 2026-05-12 09:00:00 +0900
categories: [blog,n8n]
tags: [chirpy, github-pages, jekyll]
---



# n8n 호스팅 이전 분석 보고서
## 개인 PC → Hostinger VPS 이원화 전략

> 작성일: 2026-05-12

---

## 1. 현재 환경 vs 호스팅 환경 비교

| 항목 | 현재 (개인 PC) | Hostinger VPS |
|------|---------------|---------------|
| **운영 시간** | PC 켜진 시간만 | 24시간 365일 |
| **안정성** | 재부팅/정전 취약 | 매우 안정적 |
| **속도** | 가정용 인터넷 | 전용 서버 |
| **보안** | 포트 개방 위험 | 방화벽 설정 가능 |
| **Flask 서버** | 수동 실행 필요 | 자동 실행 유지 |
| **쿠키 갱신** | 수동 (브라우저) | 동일하게 수동 필요 |
| **비용** | 전기세만 | 월 $6~15 (VPS 플랜) |
| **유지관리** | 직접 관리 | 직접 관리 (동일) |

---

## 2. Hostinger 신규 이용 시 장단점

### ✅ 장점

**① 24시간 무중단 운영**
- PC 꺼져도 n8n, Flask 서버 계속 실행
- 스케줄 트리거가 정확한 시간에 실행
- 포스팅 누락 없음

**② Flask 서버 자동 유지**
- 현재 가장 큰 문제인 "Flask 서버 꺼짐" 문제 해결
- `systemd` 서비스로 등록 → 서버 재시작 시 자동 실행

**③ 안정적인 네트워크**
- 가정용 IP 변동 없음
- 네이버 API 요청 안정화

**④ PC 성능 해방**
- 개인 PC에서 Docker, n8n 부담 제거

### ❌ 단점

**① 네이버 쿠키 갱신 불편**
- 서버에 브라우저가 없으므로 Playwright headless 모드 필요
- 로그인 자동화 어려움 (네이버 보안)
- → **이원화 운영이 필요한 핵심 이유**

**② 비용 발생**
- Hostinger VPS: 월 $6~15 (연 $72~180)
- 현재는 전기세 외 무료

**③ Linux 서버 관리 필요**
- SSH 접속, 리눅스 명령어 기본 지식 필요
- 초기 설정 복잡

**④ Playwright 설치 어려움**
- VPS에 Chromium 설치 필요
- naver_worker.py 수정 필요 (headless=True)
- 단, 쿠키 갱신은 여전히 수동 필요

---

## 3. 이원화 운영 방안 (권장)

### 구조도

```
[개인 PC]                          [Hostinger VPS]
    │                                     │
    ├─ naver_save_cookies.py         ├─ n8n (24시간)
    │  (브라우저 로그인 → 쿠키 저장)   ├─ Workflow A (콘텐츠 생성)
    │                                │ ├─ Workflow C (네이버 포스팅)
    └─ 쿠키 파일 → VPS로 전송 ──────→ └─ Flask 서버 (24시간)
```

### 이원화의 장점

| 항목 | 내용 |
|------|------|
| **쿠키 갱신** | PC에서 브라우저로 로그인 (현재 방식 유지) |
| **자동화 실행** | VPS에서 24시간 안정적으로 운영 |
| **비용 최소화** | PC는 쿠키 갱신 시에만 사용 |
| **안정성** | VPS 장애 시 PC로 백업 운영 가능 |

### 쿠키 동기화 방법

PC에서 쿠키 갱신 후 VPS로 자동 전송:

```cmd
:: PC에서 실행 (쿠키 갱신 후)
scp naver_session.json root@VPS주소:/root/naver_session.json
```

또는 **Google Drive** 경유:
```
PC → naver_session.json → Google Drive → VPS 자동 다운로드
```

---

## 4. Hostinger VPS 이전 시나리오

### Phase 1 - VPS 준비 (1~2일)

```
① Hostinger VPS 가입
   - 플랜: KVM 2 ($8.99/월) 권장
   - OS: Ubuntu 22.04 선택

② SSH 접속
   ssh root@VPS_IP

③ 기본 환경 설치
   apt update && apt upgrade -y
   apt install -y docker.io docker-compose python3 python3-pip git
```

### Phase 2 - n8n 이전 (1일)

```
① n8n Docker 설치
   docker run -d \
     --name n8n \
     -p 5678:5678 \
     -v n8n_data:/home/node/.n8n \
     n8nio/n8n

② 기존 워크플로우 내보내기 (PC)
   n8n → Settings → Export all workflows

③ VPS n8n에 가져오기
   n8n → Settings → Import workflows
```

### Phase 3 - Flask 서버 이전 (1일)

```
① 파일 업로드
   scp naver_poster_final.py root@VPS_IP:/root/
   scp naver_worker.py root@VPS_IP:/root/
   scp naver_session.json root@VPS_IP:/root/

② Playwright 설치 (VPS)
   pip3 install playwright flask
   playwright install chromium
   playwright install-deps

③ naver_worker.py 수정
   headless=False → headless=True

④ systemd 서비스 등록 (자동 시작)
   /etc/systemd/system/naver-flask.service 생성
   systemctl enable naver-flask
   systemctl start naver-flask
```

### Phase 4 - 테스트 (1일)

```
① VPS n8n에서 Workflow C 실행
② 네이버 블로그 포스팅 확인
③ Gmail 알림 테스트
④ 이상 없으면 PC n8n 중단
```

---

## 5. 권장 사항

### 단계적 접근 권장

```
현재 → 1개월 후 → 3개월 후
 PC    PC+VPS 병행   VPS 단독
      (이원화)      (안정화 후)
```

### Hostinger 추천 플랜

| 플랜 | 가격 | 사양 | 적합도 |
|------|------|------|--------|
| KVM 1 | $4.99/월 | 1CPU, 4GB RAM | ⭐⭐ (최소 사양) |
| **KVM 2** | **$8.99/월** | **2CPU, 8GB RAM** | **⭐⭐⭐ (권장)** |
| KVM 4 | $15.99/월 | 4CPU, 16GB RAM | ⭐⭐ (과사양) |

---

## 6. 결론

| 상황 | 권장 방향 |
|------|-----------|
| 당장 안정화 필요 | VPS 이전 시작 |
| 비용 부담 | 현재 PC 운영 유지 + Flask 자동시작 설정 |
| **최선의 방법** | **이원화: VPS(자동화) + PC(쿠키 갱신)** |

> 💡 핵심: VPS는 "항상 켜진 PC" 역할만 합니다.
> 네이버 쿠키 갱신은 어떤 환경이든 사람이 필요합니다.
