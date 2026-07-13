---
title: "GitHub Pages에서 Jekyll 빌드 실패? 로컬 환경 동기화로 해결하는 법"
slug: jekyll-github-pages-build-sync
date: 2026-07-13 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "GitHub Pages와 로컬 Jekyll 환경의 버전 불일치로 인한 빌드 실패를 해결하는 실전 노하우"
---

## 나를 괴롭혔던 22시간의 빌드 에러

30년을 대학에서 컴퓨터공학을 가르친 후 은퇴한 지금, 나는 개인 기술 블로그를 운영하며 여유로운 일상을 즐기고 있습니다. 작년 초 GitHub Pages로 Jekyll 블로그를 개설했을 때의 일입니다. 로컬 환경에서는 완벽하게 작동하던 포스트가 GitHub에 푸시하는 순간 빌드 에러가 발생했습니다. 

"Build failed"라는 차가운 메시지는 대학 강의실에서의 코드 리뷰보다 더 냉정했습니다. 당시 나는 로컬 Ruby 버전이 3.1.0이었는데, GitHub Pages가 지원하는 버전과 맞지 않았던 것입니다. 이 문제를 해결하기까지 무려 22시간이 걸렸고, 그 과정에서 배운 것들을 지금부터 공유하겠습니다.

## GitHub Pages의 숨은 환경 제약 이해하기

GitHub Pages는 자체 Jekyll 버전(현재 3.9.x)과 Ruby 버전 제한이 있습니다. 많은 개발자들이 간과하는 부분이 바로 이것입니다. 로컬 환경에서는 최신 버전의 Ruby와 Jekyll을 사용할 수 있지만, GitHub Pages의 서버는 보안과 안정성을 위해 특정 버전으로 고정되어 있습니다.

내 경우, `Gemfile`을 제대로 설정하지 않아서 로컬에서는 잘 작동하는데 서버에서만 실패하는 상황이 반복되었습니다. 이는 마치 자신의 강의노트로 강의는 잘하는데, 학생들 교재와 다른 부분이 있어서 시험 문제가 틀리는 것과 같은 이치입니다.

해결책은 `Gemfile`에 명시적으로 Jekyll과 의존성 패키지 버전을 지정하는 것입니다. 특히 `github-pages` gem을 추가하면, GitHub에서 실제로 사용하는 버전들이 자동으로 설치됩니다:

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
gem "jekyll-theme-chirpy"
```

## 로컬 테스트 환경 완벽하게 구축하기

로컬 환경이 GitHub Pages 서버와 100% 동일해야 한다는 것을 깨닫는 데 시간이 걸렸습니다. 대학 시절 기계실의 모든 컴퓨터를 동일하게 세팅해야 했던 경험이 여기서 다시 떠올랐습니다.

우선 Ruby 버전을 관리해야 합니다. 나는 rbenv를 사용하여 Ruby 3.1.0으로 통일했습니다:

```bash
rbenv install 3.1.0
rbenv local 3.1.0
ruby --version
```

그 다음 `Gemfile.lock`을 생성하고 정확히 같은 의존성으로 빌드하는 것이 중요합니다:

```bash
bundle install
bundle exec jekyll serve
```

`bundle exec`를 항상 사용하는 것이 핵심입니다. 이것이 `Gemfile.lock`에 명시된 정확한 버전의 gem들을 사용하게 해줍니다. 처음에는 단순히 `jekyll serve`로 실행했다가, 로컬 테스트와 실제 배포 환경의 차이로 인해 몇 가지 플러그인이 작동하지 않는 경험을 했습니다.

## GitHub Actions를 통한 사전 검증 활용

문제를 사후에 대처하기보다는 사전에 예방하는 것이 현명합니다. 이것은 50년간 소프트웨어 개발과 교육을 하면서 배운 원칙입니다.

GitHub Actions를 설정하면, 푸시 전에 자동으로 빌드를 검증할 수 있습니다. Jekyll 공식 문서에서 제공하는 워크플로우를 `.github/workflows/jekyll.yml`에 추가하면 됩니다:

```yaml
name: Build and Deploy
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build
        run: |
          bundle install
          bundle exec jekyll build
```

이 설정으로 나는 GitHub에 푸시한 후 즉시 빌드 결과를 확인할 수 있게 되었습니다. 실패하면 바로 알림을 받고 로컬에서 수정할 수 있게 된 것입니다. 마치 학생 과제를 자동 채점 시스템으로 검증하는 것처럼요.

## 플러그인 호환성 문제 해결

GitHub Pages가 지원하는 플러그인 목록은 제한적입니다. 내가 사용하던 몇몇 플러그인들이 실제 배포 환경에서는 작동하지 않았습니다. 로컬에서는 잘 보이는데 웹사이트에서는 제대로 렌더링되지 않는 경험도 했습니다.

공식 문서에서 지원하는 플러그인 목록을 확인하고, 그 안에서만 선택하는 것이 중요합니다. `_config.yml`에서 사용할 플러그인을 명시할 때, `safe: true` 옵션으로 GitHub Pages와 동일하게 동작하는지 검증할 수 있습니다.

지금 내 블로그는 안정적으로 운영되고 있습니다. 이 경험들을 바탕으로 지금까지 추가 빌드 에러 없이 200개 이상의 포스트를 배포했습니다. GitHub Pages와 로컬 Jekyll 환경을 정확히 동기화하는 것이 얼마나 중요한지를 깨달을 수 있었던 소중한 시간이었습니다.

혹시 당신도 같은 문제로 고민 중이라면, 오늘 바로 로컬 환경을 점검해보고 `Gemfile`을 업데이트하시기 바랍니다.