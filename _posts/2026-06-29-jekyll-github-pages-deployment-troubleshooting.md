---
title: "GitHub Pages에서 Jekyll 블로그가 갑자기 배포 안 될 때 확인해야 할 5가지"
slug: jekyll-github-pages-deployment-troubleshooting
date: 2026-06-29 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그 배포 실패의 원인을 찾고 빠르게 해결하는 실전 가이드"
---

## 서론: 나도 겪었던 그 답답함

은퇴 후 취미로 시작한 기술 블로깅이 벌써 3년이다. GitHub Pages에 Jekyll로 블로그를 운영하면서 경험한 가장 답답한 순간이 바로 '배포가 안 될 때'였다. 로컬에서는 멀쩡하게 빌드되고 `jekyll serve`로 확인해도 문제없는데, GitHub에 푸시하면 조용히 실패해버린다. 그 원인을 찾기 위해 헤매던 시절이 있었다. 지금은 경험을 통해 문제 해결 패턴을 알게 되었고, 이를 공유하고자 한다.

## GitHub Actions 로그를 먼저 확인하자

많은 사람들이 놓치는 첫 번째 단계가 바로 이것이다. GitHub Pages 배포 실패 시 나는 항상 저장소의 'Actions' 탭을 연다. 여기서 배포 워크플로우의 상세 로그를 확인할 수 있다. 빨간 X 표시가 보이면 그 워크플로우를 클릭하고, 'pages build and deployment' 작업을 살펴본다.

로그에는 정확한 오류 메시지가 기록되어 있다. "Liquid Exception", "undefined variable", "invalid YAML" 같은 구체적인 에러를 볼 수 있다. 나는 처음 3개월간 이 로그를 무시했고, 그 결과 같은 실수를 반복했다. 로그를 읽는 습관을 들인 후부터 문제 해결 시간이 90% 단축되었다.

## Gemfile과 Gemfile.lock의 버전 충돌

두 번째로 자주 만나는 문제가 루비 의존성 문제다. 특히 Jekyll 테마를 업그레이드하거나 플러그인을 추가할 때 발생한다. 내 경우 Chirpy 테마를 업데이트했을 때 `jekyll-paginate-v2` 플러그인의 버전이 맞지 않아서 배포에 실패했었다.

해결책은 간단하다. 로컬에서 `bundle update`를 실행한 후 생성된 `Gemfile.lock` 파일을 GitHub에 커밋해야 한다. GitHub Actions는 이 파일을 읽고 동일한 버전의 gem을 설치한다. 또한 `_config.yml`의 `plugins` 섹션이 실제로 설치된 gem과 일치하는지 확인해야 한다. 한 번은 플러그인 이름을 오타낸 덕분에 30분을 낭비한 적도 있다.

## _config.yml의 YAML 문법 오류

세 번째 원인은 설정 파일의 YAML 문법 오류다. GitHub Pages는 Ruby의 YAML 파서가 조금 엄격한 편이다. 나는 `description` 필드에 특수 문자를 포함시켰다가 배포에 실패한 경험이 있다. 콜론(`:`)이나 큰따옴표(`"`) 같은 문자를 포함하면 반드시 전체 문자열을 쌍따옴표로 감싸야 한다.

또한 `baseurl`과 `url` 설정을 제대로 하지 않으면 CSS와 JavaScript가 로드되지 않는다. 로컬 서버에서는 작동하지만 실제 배포 환경에서 이미지가 깨지는 현상이 바로 이것이다. 내 블로그는 `baseurl: ""` (빈 문자열)로 설정해야 제대로 작동했다.

## 포스트의 front matter 검증

네 번째는 개별 포스트 파일의 문제다. YAML front matter의 날짜 형식이 올바르지 않으면 Jekyll이 그 파일을 인식하지 못한다. 나는 몇 번이나 `2026-06-29 09:00:00`을 `2026-6-29 09:00:00`으로 잘못 썼다. 이런 작은 실수도 전체 빌드를 실패하게 만든다.

또한 `categories`와 `tags` 필드의 형식도 중요하다. YAML 리스트 형식이 일관되어야 한다. 내가 추천하는 형식은 `[카테고리1, 카테고리2]` 방식이다. 각 포스트를 작성하기 전에 이전 포스트의 front matter를 복사하는 것을 습관화하면 실수를 줄일 수 있다.

## 저장소 설정에서 GitHub Pages 활성화 확인

다섯 번째는 의외로 많이 간과하는 저장소 설정이다. GitHub 저장소의 'Settings' → 'Pages' 메뉴에서 배포 소스가 제대로 설정되어 있는지 확인해야 한다. 브랜치가 `main`인지 `master`인지, 빌드 소스가 저장소의 루트 디렉토리인지 확인하는 것이다.

특히 조직 계정으로 운영하는 블로그라면 저장소 이름이 정확해야 한다. 일반 계정이면 `username.github.io`, 조직이면 `organization.github.io` 형식이어야만 `https://username.github.io`나 `https://organization.github.io`로 배포된다. 다른 이름으로 설정하면 `https://username.github.io/repository-name/`으로 배포되는데, 이 경우 `baseurl`을 `/repository-name`으로 설정해야 한다.

## 마무리: 경험이 가장 좋은 선생님

지난 3년간 나는 이 다섯 가지 문제로 몇십 번 배포 실패를 경험했다. 하지만 매번 실패할 때마다 GitHub의 로그를 읽고 원인을 파악하는 과정 속에서 Jekyll과 GitHub Pages의 동작 원리를 더 깊이 이해하게 되었다. 은퇴 후 시작한 이 취미 활동이 오늘날 내 기술 블로그를 든든하게 지탱하는 자산이 되었다.

혹시 당신도 지금 Jekyll 블로그 배포 문제로 헤매고 있다면, 먼저 GitHub Actions의 로그를 확인하고 위의 다섯 가지를 하나씩 체크해보길 바란다. 대부분의 경우 이 중 하나에서 문제의 원인을 찾을 수 있을 것이다. 댓글로 당신이 겪은 배포 문제와 해결 방법을 공유해주면 더 많은 사람들이 도움을 받을 수 있을 것 같다.