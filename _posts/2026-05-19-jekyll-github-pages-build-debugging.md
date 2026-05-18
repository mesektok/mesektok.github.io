---
title: "GitHub Pages 배포 실패? Jekyll 빌드 오류 디버깅의 정석"
slug: jekyll-github-pages-build-debugging
date: 2026-05-19 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그를 GitHub Pages에 배포할 때 자주 발생하는 빌드 오류를 해결하는 실전 디버깅 방법"
---

## 서론: 나의 블로그 배포 악몽

저는 대학에서 30년간 컴퓨터공학을 가르쳤습니다. 은퇴 후 첫 프로젝트로 기술 블로그를 시작했는데, GitHub Pages에 Jekyll을 올렸을 때 정말 난감한 경험을 했습니다. 로컬에서는 완벽하게 작동하는데 GitHub에서는 빌드 실패 메일만 계속 받았습니다. 그 시절 저는 GitHub Actions의 존재도 몰랐고, 오류 메시지를 제대로 읽는 방법조차 몰랐으니까요. 지난 2년간 이 문제를 완전히 정복했고, 그 과정에서 배운 노하우를 여러분과 나누고 싶습니다.

## GitHub Actions 로그에서 실제 오류 찾기

가장 먼저 깨달은 것은 "GitHub가 보내주는 빌드 실패 메일"만 가지고는 아무것도 알 수 없다는 사실입니다. 정말 중요한 정보는 GitHub Actions 탭에 있었습니다.

저는 처음 3개월간 이 사실을 모르고 같은 오류를 반복했습니다. 이제 제 GitHub 저장소의 Actions 탭으로 가면, 각 커밋마다 빌드 과정의 상세한 로그를 볼 수 있다는 걸 압니다. 빨간 X 표시가 있는 워크플로우를 클릭하면 "build" 항목이 나타나고, 거기서 "Run jekyll build" 섹션을 열면 정확한 오류 메시지를 확인할 수 있습니다.

제 경우 가장 흔했던 오류는 "Liquid Exception: Undefined variable"이었습니다. Chirpy 테마의 프론트매터(frontmatter)에 필수 필드를 빼먹었던 것입니다. 예를 들어 `categories`나 `tags`를 빼먹으면 Jekyll이 템플릿을 렌더링할 때 오류를 던집니다. 로컬 환경에서는 strict 모드가 아니어서 경고만 나왔는데, GitHub Pages는 더 엄격합니다.

## 로컬 개발 환경을 GitHub와 동일하게 설정하기

두 번째 깨달음은 로컬과 GitHub의 환경이 다르다는 것입니다. 저는 Mac에서 Jekyll을 설치했는데, Ruby 버전이나 gem 버전에 따라 빌드 결과가 달라질 수 있었습니다.

이 문제를 해결하기 위해 저는 GitHub Pages의 공식 추천 방식을 따르기로 했습니다. 바로 `Gemfile`을 정확하게 설정하는 것입니다. GitHub Pages용 Gemfile은 다음과 같아야 합니다:

```ruby
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins
gem "jekyll-theme-chirpy"
```

이렇게 하면 로컬에서 `bundle install`을 실행할 때 GitHub Pages가 사용하는 정확한 버전의 모든 의존성을 설치합니다. 그 이후로 로컬에서 `bundle exec jekyll serve`로 테스트하면, GitHub에서도 동일하게 빌드됩니다.

제가 놓쳤던 또 다른 중요한 설정은 `_config.yml`의 `baseurl` 설정입니다. 저장소 이름이 단순히 `username.github.io`가 아니라 다른 이름이라면, baseurl을 `/repository-name`으로 설정해야 CSS와 JavaScript 파일을 제대로 로드합니다.

## 일반적인 오류 패턴과 해결책

지난 2년간 다양한 Jekyll 오류를 만났습니다. 그중 가장 흔한 것들을 정리하면 다음과 같습니다.

**첫째, 파일 인코딩 문제입니다.** 특히 한글이 포함된 포스트에서 문제가 발생했습니다. 저는 모든 markdown 파일을 UTF-8로 저장하도록 설정했습니다. VSCode에서는 오른쪽 아래의 인코딩 표시를 클릭해서 확인할 수 있습니다.

**둘째, YAML 프론트매터의 형식 오류입니다.** 블로그 초기에 제목이나 설명에 콜론(:)이 포함되어 있으면 YAML 파싱이 실패했습니다. 이제는 문제가 있을 수 있는 텍스트를 따옴표로 감싸거나, `title: "제목: 부제목"` 형식으로 작성합니다.

**셋째, 플러그인 호환성 문제입니다.** GitHub Pages에서는 보안상의 이유로 특정 플러그인만 허용합니다. 저는 처음에 이를 모르고 임의의 플러그인을 사용하려다가 계속 실패했습니다. 공식 허용 목록을 확인하고 그 안에서만 선택해야 합니다.

## 마무리

제 블로그 여정은 정말 시행착오의 연속이었습니다. 하지만 이 과정을 통해 배운 것들이 지금 얼마나 큰 자산이 되었는지 모릅니다. 특히 오류 메시지를 정확히 읽고 해석하는 능력은 단순히 블로그 운영을 넘어 모든 개발 작업의 기초입니다. 

여러분도 이 글에서 소개한 방법들을 따르면, 저처럼 수개월을 낭비하지 않고도 안정적인 Jekyll 블로그를 GitHub Pages에 배포할 수 있을 것입니다. 지금 바로 여러분의 GitHub Actions 로그를 확인해보세요. 그곳이 모든 문제 해결의 시작점입니다.