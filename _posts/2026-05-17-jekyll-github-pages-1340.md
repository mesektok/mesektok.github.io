---
title: "Jekyll 블로그가 GitHub Pages에서 자꾸 빌드 실패하는 이유, 드디어 찾았습니다"
date: 2026-05-17 09:00:00 +0900
categories: [도구]
tags: [Jekyll, GitHubPages, 블로그, 빌드에러, Chirpy]
description: "30년 컴퓨터과학 교수가 은퇴 후 Jekyll 블로그 운영하며 겪은 빌드 실패 사건들과 해결책"
---

## 프로로그: 은퇴 교수의 새로운 도전

저는 30년간 대학에서 컴퓨터과학을 가르친 후 작년에 은퇴했습니다. 교수 생활 동안 학생들 앞에서 코딩을 설명하고, 논문을 썼지만, 정작 제 생각을 제대로 정리하는 공간은 없었습니다. 그래서 은퇴 후 블로그를 시작하기로 결심했습니다. 최신 기술을 배운다는 명목으로요.

Jekyll과 GitHub Pages를 선택한 것은 제가 Git을 너무 좋아했기 때문입니다. 그런데 첫 3개월은 정말 지옥이었습니다. 블로그 포스트를 작성하고 GitHub에 올리면 빌드가 실패하고, 메일로 받는 에러 메시지는 이해하기 어렵고, 심지어 로컬에서는 잘 돌아가는데 GitHub Pages에서만 실패하는 일들이 계속되었습니다. 하지만 문제를 하나하나 해결하면서 배운 교훈들이 정말 많았습니다. 오늘은 제가 겪었던 가장 흔하고도 해결하기 어려웠던 빌드 실패 사건들을 공유하려고 합니다.

## Jekyll 로컬 환경과 GitHub Pages 환경의 차이를 무시했던 실수

처음에 제가 저지른 가장 큰 실수는 "로컬에서 잘 돌아가면 GitHub에서도 잘 돌아갈 것"이라는 착각이었습니다. 너무 순진한 생각이었습니다.

로컬 환경에서 Jekyll을 실행할 때, 저는 최신 버전의 Ruby와 Jekyll Gem들을 사용하고 있었습니다. 하지만 GitHub Pages는 특정한 버전의 Jekyll만 지원합니다. 2026년 현재, GitHub Pages는 특정 플러그인과 의존성 버전에 제한이 있습니다. 제가 로컬에서 사용한 플러그인이 GitHub Pages에서 지원되지 않으면? 빌드 실패입니다.

가장 흔한 사례가 Liquid 문법 오류였습니다. 로컬에서는 Chirpy 테마의 설정 파일이 잘 파싱되는데, GitHub Pages에서는 같은 파일이 컴파일 에러를 일으켰습니다. 결국 `_config.yml` 파일의 주석 처리 부분과 따옴표 처리를 다시 확인해야 했습니다. YAML은 생각보다 엄격한 포맷입니다.

이 문제를 해결하기 위해 저는 Gemfile에 `github-pages` Gem을 명시적으로 추가했습니다. 이렇게 하면 로컬 환경을 GitHub Pages와 동일하게 구성할 수 있습니다.

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
gem "jekyll-feed", "~> 0.12"
gem "jekyll-seo-tag"
```

## Chirpy 테마의 빌드 설정 이해하기

제가 선택한 Chirpy 테마는 정말 아름답습니다. 하지만 기본 구성이 생각보다 복잡했습니다. Chirpy는 여러 플러그인에 의존하고 있으며, 특히 `jekyll-paginate-v2`, `jekyll-archives`, `jekyll-seo-tag` 등이 필수입니다.

문제는 이 플러그인들이 GitHub Pages의 안전 모드에서 모두 작동하지 않는다는 것입니다. GitHub Pages의 안전 모드(safe mode)는 신뢰할 수 없는 플러그인의 실행을 차단합니다. 처음에는 이 사실을 몰랐습니다.

결국 해결책은 GitHub Actions를 사용하는 것이었습니다. 로컬에서 완전히 빌드된 `_site` 폴더를 GitHub에 올리거나, GitHub Actions 워크플로우를 설정하여 GitHub 서버에서 전체 빌드 프로세스를 실행하도록 변경했습니다. 

제가 설정한 GitHub Actions 워크플로우는 다음과 같습니다:

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
          ruby-version: 3.1
          bundler-cache: true
      - name: Build site
        run: bundle exec jekyll build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./_site
```

이 설정 덕분에 이제 로컬에서 완벽한 빌드를 테스트한 후, 단순히 소스 코드만 푸시하면 GitHub가 자동으로 빌드하고 배포합니다.

## 한글 파일명과 인코딩 문제

교수 시절 습관인지, 저는 포스트 파일명을 한글로 지으려고 했습니다. 예를 들어 `"2026-05-17-과학철학강의요약.md"` 식으로요. 이것이 또 다른 악몽의 시작이었습니다.

GitHub는 기본적으로 UTF-8 인코딩을 사용하지만, Jekyll의 파일 처리와 URL 생성 과정에서 한글 파일명은 예측 불가능한 결과를 만들어냅니다. 로컬 Mac에서는 NFD 정규화 형식을, Windows에서는 NFC 형식을 사용하기도 합니다. 같은 파일명인데도 다르게 인식되는 것입니다.

더 심각한 문제는 GitHub Pages가 생성하는 URL이 깨진다는 것이었습니다. 한글 파일명 포스트의 URL은 퍼센트 인코딩되어 `%EA%B3%BC%ED%95%99%EC%B2%A0%ED%95%99` 같은 형태가 되는데, 이것이 사람들이 기억하거나 공유하기 어렵습니다.

결국 저는 정책을 바꿨습니다. 파일명은 영문으로, 제목은 한글로 작성하기로요. 이제 파일은 `2026-05-17-science-philosophy-lecture-summary.md`로 짓고, YAML 프론트매터의 `title` 필드에 한글 제목을 넣습니다. 이렇게 하니 모든 것이 순조로워졌습니다.

## 결론: 기술 블로그는 인내심의 게임

은퇴 후 Jekyll 블로그를 운영하면서 배운 가장 큰 교훈은, 기술은 항상 예상 밖의 방식으로 작동한다는 것입니다. 30년을 학생들을 가르치며 "문제 해결이 중요하다"고 강조했지만, 정작 저 자신이 문제 해결의 중요성을 다시 한 번 몸으로 느껴야 했습니다.

GitHub Pages의 빌드 실패는 언짜증스럽지만, 그 과정에서 Git 워크플로우, GitHub Actions, 웹 서버의 작동 방식 등을 더 깊이 이해하게 되었습니다. 무엇보다 이런 경험이 없었다면 제 블로그에 방문하는 독자들에게 정말로 유용한 조언을 해줄 수 없었을 것입니다.

혹시 당신도 Jekyll 블로그를 운영하다가 빌드 실패로 좌절하고 계신가요? 저는 당신과 같은 경험을 했으며, 분명히 해결책이 있다는 것을 알고 있습니다. 위의 팁들이 도움이 되었다면, 댓글로 당신의 경험담을 공유해주세요. 그리고 혹시 다른 해결 방법을 알고 계신다면, 저도 배우고 싶습니다. 블로그는 혼자하는 것이 아니라 함께하는 것이니까요.