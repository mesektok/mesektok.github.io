---
title: "GitHub Pages에서 Jekyll 빌드 실패? 로컬 환경 동기화가 정답입니다"
slug: jekyll-github-pages-local-sync
date: 2026-05-19 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그를 GitHub Pages에 배포할 때 발생하는 빌드 오류의 근본 원인과 해결 방법을 실제 경험담으로 소개합니다."
---

## 교수 생활을 마치고 블로깅을 시작하다

지난해 30년간의 대학 강의를 마감했습니다. 제2의 인생을 시작하며 그동안 쌓아온 개발 경험을 공유하고 싶었고, 많은 사람들이 추천하는 Jekyll과 GitHub Pages 조합으로 블로그를 구축하기로 결심했습니다. 당시 저는 마크다운 문법과 Git의 기본을 알고 있었지만, 로컬 환경과 GitHub 서버 환경의 차이를 제대로 이해하지 못했습니다. 그 결과 배포 후 깨진 레이아웃, 사라진 스타일, 작동하지 않는 플러그인 등 다양한 문제를 겪었습니다.

처음에는 GitHub Actions의 빌드 로그를 읽으며 "왜 로컬에서는 잘 되는데 서버에서만 안 될까?"라고 한숨을 쉬었습니다. 하지만 이 문제를 해결하면서 배운 노하우가 정말 중요하다는 걸 깨달았고, 같은 고민을 하는 분들을 위해 이 글을 작성하게 되었습니다.

## Ruby 버전과 Gemfile의 중요성

가장 처음 마주친 문제는 Ruby 버전 불일치였습니다. 제 노트북에는 Ruby 3.2가 설치되어 있었는데, GitHub Pages의 기본 환경은 Ruby 2.7을 지원하고 있었습니다. 로컬에서 `bundle exec jekyll serve`로 완벽하게 실행되던 사이트가 GitHub에 푸시되는 순간 빌드 오류가 발생했습니다.

해결책은 간단하지만 필수적입니다. 프로젝트 루트에 `Gemfile`을 작성하여 의존성을 명시적으로 선언하는 것입니다. 저는 다음과 같이 설정했습니다:

```
source "https://rubygems.org"

gem "jekyll", "~> 4.3.0"
gem "github-pages", group: :jekyll_plugins
gem "jekyll-feed", "~> 0.12"
gem "jekyll-seo-tag"
gem "jekyll-sitemap"
```

`github-pages` gem을 직접 명시하면 GitHub에서 공식 지원하는 플러그인과 Ruby 버전이 자동으로 맞춰집니다. 그 다음 `bundle install`을 실행하면 `Gemfile.lock` 파일이 생성되고, 이를 Git에 커밋하면 로컬과 GitHub 환경이 완벽하게 동기화됩니다. 이것이 제가 처음 설정할 때 놓친 부분입니다.

## _config.yml의 소소한 함정들

Jekyll 설정 파일인 `_config.yml`도 예상 외의 문제를 일으켰습니다. 특히 `url`과 `baseurl` 설정이 까다로웠습니다. 저는 처음에 로컬 테스트를 위해 이 값들을 비워두었다가 그대로 커밋했습니다. 결과적으로 GitHub Pages에 배포된 사이트의 CSS 파일, 이미지, 자바스크립트가 모두 404 에러를 띄웠습니다.

정확한 설정은 다음과 같습니다:

```yaml
url: "https://yourusername.github.io"
baseurl: "/repository-name"
```

예를 들어, 저는 `2bit-blog`라는 저장소에 블로그를 만들었으므로 `baseurl: "/2bit-blog"`로 설정했습니다. 로컬 테스트 시에는 `baseurl: ""`로 임시 변경한 후 테스트하고, 배포 전에 다시 원래 값으로 돌리는 것이 안전합니다.

또한 Jekyll 버전 4.0 이상에서는 YAML 파서가 더 엄격해졌습니다. 텍스트 값이 숫자처럼 보이면(예: `2024`) 따옴표로 감싸야 합니다. `tags: [2024, jekyll]` 대신 `tags: ["2024", "jekyll"]`로 작성해야 GitHub의 빌드 시스템에서 오류가 발생하지 않습니다.

## 플러그인과 안전 모드의 이해

제 블로그에 댓글 기능과 고급 SEO 설정을 추가하려고 여러 플러그인을 설치했습니다. 로컬에서는 멀쩡히 작동했지만, GitHub Pages에 푸시하자마자 해당 플러그인들이 완전히 무시되었습니다.

이는 GitHub Pages가 보안상의 이유로 **안전 모드(Safe Mode)**를 적용하기 때문입니다. 공식 허용 목록에 없는 플러그인은 자동으로 비활성화됩니다. GitHub Pages에서 공식 지원하는 플러그인은 약 30개 정도이며, 그 외에는 GitHub Actions를 통해 사전 빌드하고 생성된 정적 파일만 배포하는 방식을 사용해야 합니다.

`.github/workflows/jekyll.yml` 파일을 생성하여 GitHub Actions 워크플로우를 설정하면, 로컬과 정확히 같은 환경에서 빌드한 후 결과만 배포할 수 있습니다. 이를 통해 저는 모든 플러그인을 자유롭게 사용할 수 있게 되었습니다.

제 경험으로 보면, Jekyll과 GitHub Pages는 정말 강력한 조합이지만, 이 두 환경의 차이를 이해하는 것이 성공의 핵심입니다. 로컬에서 완벽한 빌드 환경을 먼저 구축하고, 그것을 GitHub에 정확히 복제하는 것이 바로 그 핵심입니다. 

혹시 당신도 Jekyll 빌드 오류로 고민 중이라면, 위의 세 가지 요소(Ruby 버전, _config.yml 설정, 플러그인 관리)를 차례로 확인하고 로컬 환경과 GitHub 환경을 동기화해보세요. 당신의 블로그도 분명 제대로 작동할 것입니다.