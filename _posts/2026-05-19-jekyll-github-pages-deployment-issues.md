---
title: "GitHub Pages에서 Jekyll 블로그가 자꾸 배포 실패하는 이유, 드디어 찾았다"
slug: jekyll-github-pages-deployment-issues
date: 2026-05-19 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그를 GitHub Pages에 배포할 때 마주치는 일반적인 문제들과 해결 방법을 경험담으로 풀어냈습니다."
---

은퇴 후 취미로 시작한 블로깅이 벌써 2년이 되었다. 처음에는 네이버 블로그로 시작했지만, 기술 블로그를 운영해야 한다는 주변의 조언을 받아 Jekyll과 GitHub Pages로 갈아탔다. 그런데 정말 신경 쓸 게 많았다. 특히 배포 과정에서 계속 실패를 반복했는데, 지금은 그 원인들을 거의 다 파악했다. 오늘은 내가 겪었던 문제들과 해결 과정을 여러분과 나누고 싶다.

## GitHub Actions 설정이 가장 흔한 원인이었다

처음으로 막혔던 부분이 바로 GitHub Actions였다. Jekyll 블로그를 만들고 GitHub에 푸시했는데, 자동으로 배포되지 않았다. 어리둥절해서 며칠을 헤맸다. 나중에 알고 보니 repository의 Settings에서 Pages 설정을 제대로 안 해놨던 것이다. Build and deployment 섹션에서 Source를 "GitHub Actions"로 변경해야 하는데, 나는 이 중요한 단계를 스킵해버렸다. 

또한 `.github/workflows` 디렉토리 안에 올바른 워크플로우 파일이 없으면 자동 배포가 작동하지 않는다. Chirpy 테마를 사용한다면, 공식 저장소에서 제공하는 `build.yml` 파일을 그대로 복사해서 사용하는 게 가장 안전하다. 이 파일이 있어야 Git push를 할 때마다 자동으로 빌드되고 배포된다.

## Ruby 버전과 Gemfile 충돌 문제

두 번째 난관은 로컬 환경과 GitHub Actions 환경의 불일치였다. 내 컴퓨터에서는 완벽하게 작동하는 블로그가 GitHub에 푸시되면 빌드 실패 메시지를 띄웠다. 배포 로그를 자세히 살펴보니 Ruby 버전 문제였다. 내 로컬 머신에는 Ruby 3.2가 설치되어 있었지만, GitHub Actions의 기본 Ruby 버전은 다를 수 있다. 이를 해결하려면 `.github/workflows/build.yml` 파일에서 ruby-version을 명시적으로 설정해야 한다.

더불어 Gemfile의 내용도 중요하다. Jekyl Chirpy 테마는 특정 gem들에 의존하는데, Gemfile에 모든 필수 gem이 올바르게 기록되어 있어야 한다. 특히 `webrick` gem은 Ruby 3.0 이상에서 필수인데, 이를 누락시키면 로컬에서만 작동하고 서버에서는 실패한다. 나는 이 문제로 한 주를 낭비했다. 이제는 `bundle update`를 정기적으로 실행해서 모든 의존성이 최신으로 유지되도록 한다.

## 파일명과 인코딩이 숨겨진 함정

세 번째 문제는 정말 황당했다. 블로그 포스트의 파일명이 올바르지 않아서 배포가 실패했던 것이다. Jekyll은 포스트 파일명에 엄격한 규칙이 있다. 반드시 `YYYY-MM-DD-제목.md` 형식이어야 한다. 나는 처음에 이 규칙을 제대로 이해하지 못해서 파일명을 마음대로 지었고, 결국 포스트가 나타나지 않았다.

또한 파일의 인코딩도 UTF-8이어야 한다. 특히 한국어를 사용하는 경우, 인코딩 문제로 인해 특수문자가 깨지거나 빌드가 실패할 수 있다. VS Code를 사용한다면 우측 하단의 인코딩 표시를 클릭해서 "UTF-8"으로 설정되어 있는지 항상 확인하자. YAML front matter의 문법도 중요한데, 따옴표나 콜론의 위치가 한 글자만 틀려도 배포가 실패한다. 나는 이 작은 실수들 때문에 몇 번이나 배포 오류를 겪었다.

Jekyll 블로그 운영이 처음에는 복잡해 보였지만, 이런 문제들을 하나씩 해결하다 보니 지금은 아주 편하게 운영하고 있다. 여러분도 비슷한 문제로 고민하고 있다면, 이 글의 해결책들을 차근차근 시도해보기 바란다. 당신의 GitHub Pages 블로그가 완벽하게 작동할 수 있도록 응원한다.