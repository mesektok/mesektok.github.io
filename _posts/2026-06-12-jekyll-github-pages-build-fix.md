---
title: "GitHub Pages에서 Jekyll 빌드 실패? 숨겨진 _config.yml 설정이 답이었다"
slug: jekyll-github-pages-build-fix
date: 2026-06-12 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "로컬에서는 잘 되는데 GitHub Pages에서 빌드 실패하는 문제를 해결하는 _config.yml 설정법"
---

## 내가 겪은 정말 답답한 문제

은퇴 후 블로깅을 취미로 삼으면서 Jekyll과 GitHub Pages를 만났습니다. 처음엔 설렜어요. 로컬 환경에서 `bundle exec jekyll serve` 명령으로 완벽하게 실행되는 블로그를 GitHub에 올렸는데, 며칠이 지나도 빌드 이메일이 안 오길래 확인해보니 빨간 오류 표시만 떠 있었습니다. 당시 제게는 디버깅 경험이 충분하지 않았기에 정말 막막했습니다.

처음엔 마크다운 문법 오류인 줄 알고 일일이 검토했고, 플러그인 호환성 문제인 줄 생각해서 Gemfile까지 뜯어고쳤습니다. 하지만 여전히 실패했습니다. 결국 GitHub Actions 로그를 꼼꼼히 읽어보니 `_config.yml` 파일의 작은 설정 차이가 원인이었습니다. 그 이후로 많은 초보자분들이 같은 실수를 하는 것을 봤습니다.

## GitHub Pages 환경에서 요구하는 _config.yml 설정

GitHub Pages는 로컬 환경과 다른 조건에서 Jekyll을 실행합니다. 가장 중요한 차이는 `baseurl`과 `url` 설정입니다. 저도 처음엔 이 두 설정의 차이를 몰랐습니다. `baseurl`은 저장소명이 `username.github.io`가 아닐 때 필수입니다. 예를 들어 저장소가 `my-blog`라면 `baseurl: "/my-blog"`로 설정해야 합니다.

또한 `url`은 절대 경로입니다. `url: "https://username.github.io"`처럼 설정하면 됩니다. 이 두 값이 제대로 설정되지 않으면 CSS, JavaScript, 이미지 경로가 모두 깨져서 빌드 오류가 발생합니다. 제 경험상 이 문제로 3주를 헤맸습니다. 정말 답답했어요.

또 하나 놓치기 쉬운 설정은 `markdown` 엔진입니다. GitHub Pages는 기본적으로 `kramdown`을 사용합니다. 로컬에서 다른 마크다운 엔진을 썼다면 문법 해석이 달라져서 오류가 발생할 수 있습니다. `markdown: kramdown`을 명시적으로 추가하는 것이 좋습니다. 그리고 `plugins` 섹션에서 `github-pages` gem이 포함한 플러그인들만 사용해야 합니다. 아무리 좋은 플러그인이어도 GitHub Pages에서 지원하지 않으면 실패합니다.

## 실제로 작동하는 _config.yml 템플릿

제가 수년간 문제없이 사용하는 기본 설정을 공개합니다. 저는 이 템플릿을 신규 프로젝트마다 복사해서 시작합니다. 

```yaml
title: My Blog
description: 블로그 설명
author: Your Name
url: "https://username.github.io"
baseurl: ""
lang: ko-KR
timezone: Asia/Seoul

markdown: kramdown
highlighter: rouge
permalink: /:year/:month/:day/:title/

plugins:
  - jekyll-feed
  - jekyll-seo-tag
  - jekyll-archives

exclude:
  - .gitignore
  - Gemfile
  - Gemfile.lock
  - README.md
```

저장소명이 `username.github.io`가 아니면 `baseurl: "/저장소명"`으로 수정하면 됩니다. 이 설정 하나로 제 모든 빌드 오류가 사라졌습니다. 

## GitHub Actions로 빌드 과정 직접 확인하기

GitHub Pages에서 빌드가 실패했을 때 가장 먼저 할 일은 Actions 탭을 확인하는 것입니다. 저는 처음에 이것을 몰랐어요. 이제는 어떤 환경에서든 문제가 생기면 Actions 로그부터 봅니다. 보통 5~10분 내에 빌드 결과가 나옵니다.

로그에서 정확한 오류 메시지를 볼 수 있습니다. "CSS 파일을 찾을 수 없다"는 메시지가 나온다면 `baseurl`을 의심해보세요. "플러그인이 지원되지 않는다"는 메시지가 나온다면 `_config.yml`의 plugins 섹션을 정리해야 합니다. 

저 같은 시니어 개발자도 처음엔 이 로그를 무시했는데, 이제는 이것이 가장 중요한 디버깅 도구라고 생각합니다. 여러분도 빌드 실패가 나면 꼭 Actions 탭을 확인하세요. 대부분의 답이 거기 있습니다.

지금 GitHub Pages 빌드 오류로 고민하고 계신다면, 위 설정들을 확인해보시고 자신의 환경에 맞게 수정한 후 다시 push 해보세요. 아마 이번에는 성공할 겁니다.