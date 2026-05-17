---
title: "GitHub Pages에서 로컬 Jekyll 빌드가 자꾸 실패하는 이유, 드디어 찾았습니다"
date: 2026-05-17 09:00:00 +0900
categories: [도구]
tags: [Jekyll, GitHub Pages, GitHub, 블로그, 빌드 오류]
description: "은퇴 교수가 겪은 Jekyll 로컬 빌드 문제와 GitHub Pages 배포 실패의 원인과 해결책"
---

## 우연히 발견한 작은 설정 차이가 모든 걸 바꿨습니다

대학에서 35년간 전자공학을 가르쳤던 저는, 은퇴 후 새로운 취미로 기술 블로그를 시작했습니다. Jekyll과 GitHub Pages를 이용해 정적 웹사이트를 만들기로 결심했는데, 정말 답답한 문제에 자주 부딪혔습니다. 로컬 환경에서 `jekyll serve`로 완벽하게 작동하던 블로그가, GitHub에 푸시하면 빌드가 실패하거나 레이아웃이 깨지는 현상이 반복됐습니다.

처음에는 단순한 실수일 거라 생각했습니다. 하지만 3주간 여러 번의 시행착오를 거친 끝에, 마침내 핵심 원인들을 찾아냈습니다. 오늘은 제가 겪었던 실제 경험과 해결책을 여러분과 나누고 싶습니다.

## Ruby 버전 불일치: 가장 흔한 함정

제 첫 번째 문제는 Ruby 버전이었습니다. 로컬에서는 Ruby 3.1을 사용하고 있었는데, GitHub Pages가 지원하는 Ruby 버전을 확인하지 않았거든요. GitHub Pages는 특정 Ruby 버전에서만 Jekyll을 실행합니다. 

저는 `.ruby-version` 파일을 프로젝트 루트에 생성했고, GitHub Actions 워크플로우를 설정할 때도 명시적으로 Ruby 버전을 지정했습니다. 특히 `actions/setup-ruby@v1` 액션을 사용할 때, `ruby-version` 파라미터를 명확히 입력하는 것이 중요합니다. 

```
- uses: actions/setup-ruby@v1
  with:
    ruby-version: 3.1
```

처음에는 이것이 단순한 설정일 뿐이라 무시했는데, 이게 모든 빌드 실패의 근본 원인이었습니다. Gemfile과 Gemfile.lock 파일도 반드시 Git에 커밋해야 합니다. 로컬 환경과 GitHub 서버 환경이 동일한 gem 버전을 사용하도록 강제하는 것입니다.

## Gemfile 설정: 보이지 않는 체크포인트

두 번째 문제는 더욱 교활했습니다. 제 Gemfile에는 개발 환경 전용 gem들이 포함되어 있었는데, GitHub Pages 배포 시에는 이들이 필요 없었거든요. 특히 `webrick`이라는 gem이 로컬 서버 실행에는 필수지만, GitHub Pages의 Ruby 환경에서는 문제를 일으킬 수 있습니다.

저는 Gemfile을 다시 정리했습니다:

```ruby
source "https://rubygems.org"

gem "jekyll", "~> 4.3"
gem "jekyll-theme-chirpy", "~> 6.1"

group :jekyll_plugins do
  gem "jekyll-paginate-v2"
  gem "jekyll-feed"
  gem "jekyll-seo-tag"
end

group :development do
  gem "webrick"
end
```

이렇게 `development` 그룹으로 분리하면, GitHub에서 프로덕션 배포 시 이 gem들을 설치하지 않습니다. 또한 `_config.yml` 파일에서 플러그인 목록을 명시적으로 지정하는 것도 중요합니다. GitHub Pages가 자동으로 지원하는 플러그인만 사용해야 한다는 원칙을 지켜야 합니다.

## _config.yml의 숨겨진 설정값들

세 번째로 제가 놓친 부분은 `_config.yml` 파일의 세부 설정이었습니다. 특히 `url`과 `baseurl` 값이 잘못되어 있으면, GitHub Pages에서 모든 자산(CSS, 이미지)이 제대로 로드되지 않습니다.

저의 경우, 저장소 이름이 `my-blog`였는데:
- 사용자 페이지라면: `url: "https://username.github.io"`와 `baseurl: ""`
- 프로젝트 페이지라면: `url: "https://username.github.io"`와 `baseurl: "/my-blog"`

이 차이를 제대로 이해하지 못했다가, 로컬에서는 `localhost:4000`으로 접근하니 baseurl이 상관없었지만, GitHub Pages에 올리니 모든 링크가 404 오류를 냈습니다.

또한 `_config.yml`에 `plugins:` 섹션도 필수입니다:

```yaml
plugins:
  - jekyll-feed
  - jekyll-seo-tag
  - jekyll-paginate-v2
```

## GitHub Actions 워크플로우: 자동화의 열쇠

제가 발견한 가장 강력한 해결책은 GitHub Actions 워크플로우 파일을 직접 작성하는 것이었습니다. `.github/workflows/jekyll.yml` 파일을 생성해서 명시적으로 빌드 과정을 제어하면, 로컬 환경과 서버 환경의 차이를 최소화할 수 있습니다.

```yaml
name: Build and Deploy

on:
  push:
    branches: [ main ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.1
          bundler-cache: true
      
      - name: Build with Jekyll
        run: bundle exec jekyll build
      
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./_site
```

이 워크플로우는 GitHub에서 자동으로 빌드하고 배포합니다. 매우 투명한 과정이라 오류가 발생하면 logs를 통해 정확히 무엇이 잘못됐는지 파악할 수 있습니다.

## 로컬에서 GitHub 환경을 시뮬레이션하기

마지막으로 제가 터득한 팁은, 로컬에서 GitHub Pages와 동일한 환경을 만드는 것입니다. `bundle install`과 `bundle exec jekyll serve`를 사용하면, Gemfile.lock에 명시된 정확한 gem 버전이 사용됩니다.

```bash
bundle install
bundle exec jekyll serve --incremental
```

`--incremental` 옵션은 파일 변경 시 증분 빌드만 하도록 해서, 개발 속도를 높입니다. 만약 로컬에서 완벽하게 작동하는데 GitHub에서 실패한다면, gem 버전 차이를 의심해야 합니다.

## 결론: 작은 주의가 큰 시간 낭비를 방지합니다

은퇴 후 기술 블로그를 시작하면서 저는 많은 것을 배웠습니다. 특히 정적 사이트 생성기인 Jekyll은 문제가 발생했을 때 원인을 찾기가 쉽지 않다는 것입니다. 하지만 Ruby 버전, Gemfile 설정, _config.yml의 url/baseurl, 그리고 GitHub Actions 워크플로우 이 네 가지만 정확히 이해하고 설정하면, 대부분의 문제를 사전에 방지할 수 있습니다.

저는 지금 매주 새로운 기술 글을 발행하고 있으며, 빌드 실패로 인한 스트레스는 완전히 사라졌습니다. 혹시 여러분도 Jekyll과 GitHub Pages로 블로그를 운영하면서 비슷한 문제를 겪고 계신다면, 이 글이 도움이 되길 바랍니다.

**지금 바로 여러분의 `_config.yml` 파일을 열어서 url과 baseurl 설정을 확인해 보시고, Gemfile의 구조가 제가 제시한 형식과 맞는지 검토해 보세요. 그리고 GitHub Actions 워크플로우를 아직 설정하지 않았다면 오늘 바로 설정해 보시길 권장합니다!**