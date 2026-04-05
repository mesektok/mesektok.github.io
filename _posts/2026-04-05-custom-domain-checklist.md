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
