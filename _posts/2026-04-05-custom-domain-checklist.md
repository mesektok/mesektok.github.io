---
title: "GitHub Pages 커스텀 도메인 연결 체크리스트"
date: 2026-04-05 09:00:00 +0900
categories: [deployment]
tags: [domain, github-pages, dns]
---

GitHub Pages에 커스텀 도메인을 연결하면 블로그의 완성도가 확실히 올라갑니다.

하지만 도메인을 연결한 뒤에도 접속 지연이나 HTTPS 반영 지연 때문에 바로 정상 작동하지 않는 경우가 있습니다.

DNS 설정, `CNAME`, 저장소 설정, HTTPS 상태를 함께 확인해야 안정적으로 연결됩니다.

이 글은 실제 연결 과정에서 놓치기 쉬운 항목을 빠르게 확인할 수 있도록 정리한 체크리스트입니다.

## 도메인 연결 구체 체크리스트

### 1. 도메인 구입·네임서버 상태 확인

- 도메인 만료일이 지나지 않았는지 확인한다.
- 도메인 관리 페이지에서 어떤 DNS를 쓰는지 먼저 파악한다.
  - 예: 도메인 업체 기본 DNS, Cloudflare, Route53 등
- 이후 A레코드·CNAME은 이 DNS 관리 화면에서 수정하게 된다.

---

### 2. GitHub Pages 저장소 설정 확인

1. GitHub에서 블로그 저장소로 이동한다.
2. **Settings → Pages** 메뉴로 들어간다.
3. Source가 `GitHub Actions` 또는 `Deploy from a branch` 로 설정되어 있는지 확인한다.
4. `Custom domain` 입력란에 연결할 도메인을 입력하고 **Save** 한다.
   - 예: `mesektok.com`, `blog.mesektok.com`
5. 저장 후 저장소 루트에 `CNAME` 파일이 생성되었는지 확인한다.

---

### 3. 루트 도메인(A 레코드) 설정

루트 도메인(`example.com`)을 GitHub Pages에 연결하려면 A 레코드가 필요하다.

DNS 관리 화면에서 다음과 같이 A 레코드를 추가한다.

- 이름(Name/Host): `@`
- 타입(Type): `A`
- 값(Value/IP):

  - `185.199.108.153`
  - `185.199.109.153`
  - `185.199.110.153`
  - `185.199.111.153`

이미 다른 A 레코드가 있다면 GitHub Pages용 레코드만 남기고 나머지는 삭제하거나 비활성화한다.

---

### 4. 서브도메인(CNAME) 설정

`blog.example.com` 처럼 서브도메인을 사용할 때는 CNAME 레코드를 추가한다.

- 이름(Name/Host): `blog` (사용하려는 서브도메인)
- 타입(Type): `CNAME`
- 값(Value/Target): `username.github.io`
  - 예: `mesektok.github.io`

주의: 루트 도메인(`@`)은 CNAME이 아니라 **A 레코드**를 사용해야 한다.

---

### 5. `CNAME` 파일 내용 확인

저장소 루트의 `CNAME` 파일을 열어 내용을 확인한다.

- 파일 위치: 저장소 루트의 `CNAME`
- 내용은 한 줄만 있어야 한다.
  - 예: `mesektok.com`
  - 서브도메인을 쓴다면 `blog.mesektok.com`

DNS 설정에 입력한 도메인과 **완전히 동일**해야 한다.  
`https://` 나 `/` 는 쓰지 않고, 앞뒤 공백도 없어야 한다.

---

### 6. HTTPS(SSL) 상태 확인

1. 다시 **Settings → Pages** 로 이동한다.
2. `Custom domain` 아래 `Enforce HTTPS` 체크 박스를 확인한다.
3. 처음 연결 직후에는 `Certificate being provisioned` 와 같이 인증서 발급 중 메시지가 뜰 수 있다.
4. 보통 10~30분, 길면 몇 시간 후 체크 박스를 선택할 수 있게 된다.
5. 체크하면 HTTP 요청이 자동으로 HTTPS로 리다이렉트된다.

---

### 7. DNS 전파 상태 확인

DNS 변경 사항은 전파까지 시간이 걸린다.

- 일반적으로 5~10분 이내에 반영되지만, 최대 24시간이 걸릴 수 있다.
- 아래 도구들로 전파 상태를 확인할 수 있다.
  - `nslookup example.com`
  - `dig example.com`
  - 온라인 DNS 검사 사이트(whatsmydns.net 등)

---

### 8. 브라우저 캐시·SSL 캐시 정리

이전에 다른 서버에 연결했던 도메인이라면 브라우저 캐시 때문에 예전 화면이 보일 수 있다.

- 시크릿(인프라이빗) 모드에서 접속해 본다.
- 필요하면 브라우저 캐시를 비우고 다시 접속한다.
- `http://도메인`, `https://도메인` 둘 다 시도해 보고 차이를 확인한다.

---

### 9. 최종 체크 포인트

아래 항목을 순서대로 확인하면 된다.

1. GitHub Pages 설정에서 `Custom domain` 이 정확한가
2. 루트에 있는 `CNAME` 파일 내용이 도메인과 완전히 같은가
3. 루트 도메인의 A 레코드가 GitHub IP로 향하는가
4. 서브도메인의 CNAME이 `username.github.io` 를 가리키는가
5. DNS 전파 시간을 충분히 기다렸는가
6. `Enforce HTTPS` 가 활성화되어 있는가
7. 시크릿 모드에서도 정상 접속되는가
