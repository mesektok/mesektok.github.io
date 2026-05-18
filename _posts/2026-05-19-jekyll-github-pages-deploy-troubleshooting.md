---
title: "GitHub Pages에서 Jekyll 블로그가 자동 배포되지 않는 이유, 드디어 알았습니다"
slug: jekyll-github-pages-deploy-troubleshooting
date: 2026-05-19 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages, Troubleshooting]
description: "Jekyll 블로그를 GitHub Pages에 올렸는데 자동 배포가 안 되는 문제의 원인과 해결책을 실제 경험담으로 풀어냅니다."
---

대학에서 은퇴한 지 3년 차인 나이인데, 요즘은 취미로 블로깅을 시작했습니다. 처음엔 까다로운 Jekyll과 GitHub Pages의 관계가 정말 답답했어요. 특히 로컬에서는 완벽하게 작동하는데 GitHub에 push하면 배포가 안 되는 문제로 며칠을 고민했답니다. 이제 그 경험을 여러분과 나누고 싶습니다.

## GitHub Actions 워크플로우 파일의 존재 여부 확인하기

처음에 저는 GitHub Pages 설정이 자동으로 되는 줄 알았습니다. 그런데 이게 아니었어요. Jekyll 블로그가 정상적으로 배포되려면 `.github/workflows/` 디렉토리에 적절한 워크플로우 파일이 있어야 합니다. 저는 처음 이 폴더 자체를 만들지 않아서 한참을 헤맸어요.

올바른 워크플로우 파일(예: `jekyll.yml`)이 없으면 GitHub이 자동으로 빌드와 배포를 해주지 않습니다. 특히 개인 저장소의 경우 더욱 그렇습니다. 저는 GitHub의 공식 Jekyll 액션을 참고해서 `.github/workflows/jekyll.yml` 파일을 직접 만들었고, 그 순간부터 모든 게 정상작동했습니다. 아, 정말 그때의 쾌감이란!

## Ruby 버전과 Gemfile 호환성 문제 파악하기

두 번째로 마주친 문제는 Ruby 버전 문제였습니다. 로컬 환경에서는 Ruby 3.1 버전을 사용하고 있었는데, GitHub의 빌드 환경은 다를 수 있다는 걸 몰랐어요. 특히 `Gemfile.lock` 파일이 생성되지 않으면 GitHub이 의존성을 제대로 해결하지 못합니다.

저는 로컬에서 `bundle install` 명령어를 실행해 `Gemfile.lock` 파일을 생성한 뒤 이를 저장소에 커밋했습니다. 이건 정말 기초적인 부분이지만, 많은 초보자가 놓치는 부분이더라고요. 또한 `Gemfile`에 명시된 Jekyll 버전과 플러그인들이 모두 호환되는지 확인하는 것도 중요합니다. 특히 `jekyll-seo-tag`나 `jekyll-sitemap` 같은 플러그인들이 서로 충돌하는 경우도 있거든요.

## _config.yml 설정과 브랜치 배포 설정 확인하기

세 번째 실수는 정말 황당했습니다. GitHub 저장소의 Settings에서 Pages 배포 설정을 확인하지 않았어요. 저는 `_config.yml` 파일만 완벽하게 설정되면 된다고 생각했는데, 이건 절반의 진실이었습니다.

GitHub Settings → Pages 섹션에서 어느 브랜치에서 배포할 것인지, 그리고 빌드 소스가 무엇인지를 명확하게 지정해야 합니다. 저는 이 부분을 제대로 보지 않아서 계속 main 브랜치의 `/docs` 폴더에서 배포하도록 설정되어 있었어요. 결국 올바른 설정으로 변경해야 했습니다. 요즘은 `GitHub Actions`를 빌드 소스로 지정하는 것이 최신 방식입니다.

또한 `_config.yml`의 `baseurl` 설정도 중요한데, 만약 저장소 이름이 username.github.io가 아니라면 반드시 baseurl을 저장소 이름으로 설정해야 합니다. 저는 이 부분도 처음에 빈칸으로 두었다가 CSS와 이미지가 로드되지 않는 문제를 경험했습니다.

처음 블로깅을 시작할 때는 이런 기술적 부분이 정말 답답했지만, 하나하나 문제를 해결하면서 성취감을 느꼈어요. 여러분도 비슷한 문제로 고민하고 계신다면 위의 세 가지 항목을 차례대로 확인해 보세요. 분명 해결책이 그 안에 있을 겁니다. 

지금 바로 여러분의 저장소 Settings와 워크플로우 파일을 확인해 보세요. 혹시 배포가 안 되는 문제가 있다면 그 원인이 숨어 있을 수 있습니다!