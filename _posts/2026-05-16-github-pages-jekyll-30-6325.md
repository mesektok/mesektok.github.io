---
title: "GitHub Pages에서 로컬 Jekyll 빌드가 자꾸 실패하는 이유, 30년 개발자가 찾은 답"
date: 2026-05-16 09:00:00 +0900
categories: [도구]
tags: [Jekyll, GitHub Pages, 블로그, 빌드 오류, 로컬 개발]
description: "GitHub Pages와 로컬 환경의 Jekyll 버전 불일치로 인한 빌드 오류 해결법을 경험담을 통해 설명합니다."
---

## 은퇴 후 블로깅을 시작한 노교수의 예상 밖의 고민

대학에서 정년퇴직한 지 2년이 지났습니다. 50년 가까운 프로그래밍 경력을 정리하고 싶다는 생각에 GitHub Pages로 블로그를 시작했죠. 요즘 유행한다는 Jekyll Chirpy 테마를 사용하기로 결정했고, 처음엔 잘 진행되는 것처럼 보였습니다. 

그런데 문제가 생겼습니다. 로컬 환경에서 `bundle exec jekyll serve`를 입력하면 빌드가 되는데, GitHub에 푸시하면 빌드 오류 메일이 날아오는 것입니다. 처음엔 이것이 단순한 실수라고 생각했지만, 10번 이상 시도해도 같은 오류가 반복되었습니다. 결국 며칠을 헤매다 근본적인 원인을 찾게 되었고, 그 경험을 여러분과 나누고 싶습니다.

## 진짜 문제는 버전 불일치였다

내가 겪던 주요 증상은 이랬습니다. 로컬에선 모든 게 완벽했습니다. 마크다운 파일을 작성하고, `bundle exec jekyll serve`를 실행하면 `http://localhost:4000`에서 블로그가 멋지게 렌더링되었거든요. 그런데 `git push`를 하면 GitHub Actions에서 자동 빌드가 실패하는 메시지가 떴습니다.

오류 메시지를 자세히 살펴보니 문제가 명확했습니다. 로컬 환경의 Jekyll 버전과 GitHub Pages가 사용하는 버전이 달랐던 것입니다. 나는 로컬에서 최신 버전의 Jekyll(4.3.x)을 사용하고 있었는데, GitHub Pages의 기본 환경은 여전히 이전 버전을 기반으로 하고 있었던 거죠.

특히 Chirpy 테마는 최신 기능들을 적극 활용하기 때문에, 버전 호환성이 매우 중요합니다. 예를 들어 새로운 플러그인이나 액체 필터(Liquid Filter) 같은 기능들은 구 버전에서 지원되지 않습니다. 나는 로컬에서 테스트했을 때 이런 기능들이 모두 정상 작동했기 때문에, 원격 저장소에서도 당연히 작동할 거라고 착각했던 것입니다.

## 해결책 1: Gemfile 제대로 관리하기

문제를 해결하기 위해 처음 한 일은 `Gemfile`을 정확히 설정하는 것이었습니다. Chirpy 테마의 공식 저장소에서 제공하는 `Gemfile`을 다시 확인했습니다.

```ruby
source "https://rubygems.org"

gem "jekyll", "~> 4.3"
gem "jekyll-paginate", "~> 2.0"
gem "jekyll-redirect-from"
gem "jekyll-seo-tag"
gem "jekyll-archives"
gem "jekyll-sitemap"
gem "jekyll-relative-links"
```

중요한 것은 버전을 명시하는 방식입니다. `~>`는 "호환성이 있는 버전을 사용하라"는 뜻인데, 이를 통해 로컬과 GitHub Pages 환경 모두에서 비슷한 버전의 gem을 사용하도록 강제할 수 있습니다. 

또한 `bundle update`를 정기적으로 실행하되, 그 결과인 `Gemfile.lock`을 반드시 커밋해야 합니다. 이 파일이 있으면 GitHub에서도 정확히 같은 버전을 설치하게 됩니다. 내가 처음에는 `.gitignore`에서 실수로 이 파일을 제외했었는데, 이것이 원인 중 하나였습니다.

## 해결책 2: GitHub Actions 워크플로우 점검

GitHub Pages는 이제 GitHub Actions를 통해 자동으로 빌드됩니다. Chirpy 테마를 사용한다면 `.github/workflows/` 디렉토리에 빌드 설정 파일이 있을 것입니다.

```yaml
name: Build and Deploy

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.2
          bundler-cache: true

      - name: Build
        run: bundle exec jekyll build
```

나는 이 설정에서 Ruby 버전도 명시적으로 설정했습니다. 로컬에서 `ruby -v`로 확인한 버전과 GitHub Actions에서 사용하는 버전을 맞추는 것이 중요합니다. 만약 로컬에서 Ruby 3.1을 사용하는데 GitHub에서 3.0을 사용한다면, 호환성 문제가 발생할 수 있습니다.

또한 `bundler-cache: true`는 매우 중요한 설정입니다. 이것이 `Gemfile.lock`을 읽고 정확한 버전들을 캐시하기 때문입니다.

## 해결책 3: 로컬 테스트 환경을 GitHub와 동일하게

가장 근본적인 해결책은 로컬 환경을 GitHub Pages와 동일하게 만드는 것입니다. GitHub Pages 공식 문서에 따르면, 로컬에서 다음 명령어를 실행해야 합니다:

```bash
bundle add github-pages
```

이 gem을 추가하면 로컬 환경이 자동으로 GitHub Pages의