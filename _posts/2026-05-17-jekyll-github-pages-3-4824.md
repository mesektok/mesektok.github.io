---
title: "Jekyll 블로그를 GitHub Pages에 배포하다가 빌드 실패로 3일을 헤맨 이야기"
date: 2026-05-17 09:00:00 +0900
categories: [도구]
tags: [Jekyll, GitHub Pages, 블로그, 빌드 오류, 정적사이트]
description: "은퇴 후 기술블로그를 시작한 노교수가 겪은 Jekyll 빌드 실패 사건과 해결 과정"
---

## 프로필 사진은 멋있는데 블로그는 왜 자꾸 빨간 줄이 그어질까

작년 정년퇴임을 한 뒤 시간이 많아졌다. 40년간 대학에서 컴퓨터공학을 가르친 내가 이제 배우는 입장이 되어야 한다고 생각했다. 특히 요즘 젊은 학생들이 쓰는 GitHub와 Jekyll이라는 게 뭔지 궁금했다. "디지털 시대의 할아버지가 되고 싶지 않다"며 손주는 웃었지만, 나는 진지했다.

GitHub Pages에 Jekyll을 이용한 개인 블로그를 만들기로 결심했다. 저장소를 만들고, Chirpy 테마를 적용했다. 로컬에서는 완벽하게 작동했다. 그런데 GitHub에 푸시하는 순간 문제가 터졌다. 빨간 X 마크와 함께 "Build Failed" 메시지가 떴다. 

화면을 한참 바라봤다. 무엇이 문제인지 전혀 감이 오지 않았다. 40년간 대학에서 강의한 경험이 아무 도움이 되지 않았다. 이것이 내 3일간의 악몽의 시작이었다.

## 첫 번째 실수: GitHub Actions 로그를 읽지 않다

문제를 해결하려면 먼저 무엇이 잘못되었는지 알아야 한다. 당연한 말이지만, 나는 이를 무시했다. 대신 구글링을 시작했다. "Jekyll GitHub Pages 빌드 실패"라는 검색어로 수십 개의 포스팅을 읽었다. 하지만 내 문제와 정확히 일치하는 해답은 없었다.

그러다 손주가 옆에서 한마디 했다. "할아버지, GitHub Actions 로그를 봤어?" 

아, 맞다. GitHub 저장소 설정에서 Actions 탭을 누르면 상세한 빌드 로그가 있었다. 나는 비로소 그 로그를 읽기 시작했다. 거기에는 정확한 오류 메시지가 있었다:

```
Gem::Resolver::ActivationError: can't activate bundler (~> 2.4.0)
```

Bundler 버전 문제였다. 로컬에서는 최신 버전이 설치되어 있었지만, GitHub의 빌드 환경에서는 다른 버전을 사용하고 있었다.

## 두 번째 실수: Gemfile과 Gemfile.lock을 무시하다

처음 Jekyll을 배울 때 "Gemfile"이라는 파일의 중요성을 간과했다. 로컬에서만 작동하면 된다고 생각했던 것이다. 하지만 GitHub Pages에서 블로그를 배포하려면 Gemfile과 Gemfile.lock이 정확하게 설정되어야 한다.

Gemfile은 프로젝트가 필요한 모든 의존성(gem)을 명시하는 파일이다. Gemfile.lock은 설치된 gem의 정확한 버전을 기록한다. GitHub의 빌드 환경은 이 파일들을 읽고 정확히 같은 버전의 gem들을 설치하려고 시도한다.

나는 다음과 같이 Gemfile을 수정했다:

```ruby
source "https://rubygems.org"

gem "jekyll", "~> 4.3.0"
gem "jekyll-theme-chirpy", "~> 6.0"

group :jekyll_plugins do
  gem "jekyll-paginate"
  gem "jekyll-redirect-from"
  gem "jekyll-seo-tag"
  gem "jekyll-archives"
  gem "jekyll-sitemap"
end
```

그리고 로컬에서 `bundle update`를 실행하여 Gemfile.lock을 갱신했다. 이것만으로도 많은 문제가 해결되었다.

## 세 번째 실수: _config.yml의 설정을 방심하다

빌드는 여전히 실패했다. 이번에는 다른 오류였다:

```
Liquid Exception: Liquid syntax error (line x): Expected id but found end_of_string
```

Liquid 템플릿 엔진에서 구문 오류가 났다는 뜻이다. 나는 `_config.yml` 파일을 다시 살펴봤다. GitHub Pages에 배포할 때 설정해야 할 것들이 있었다:

```yaml
baseurl: "/repository-name"
url: "https://username.github.io"

# GitHub Pages 호스팅
plugins:
  - jekyll-paginate
  - jekyll-redirect-from
  - jekyll-seo-tag
  - jekyll-archives
  - jekyll-sitemap
```

특히 `baseurl`이 중요했다. 만약 저장소 이름이 "my-blog"라면 baseurl을 "/my-blog"로 설정해야 한다. 하지만 username.github.io 저장소라면 baseurl을 비워둬야 한다.

내 경우 이 부분을 제대로 설정하지 않았고, 그 결과 CSS와 JavaScript 파일이 제대로 로드되지 않았다. 뿐만 아니라 내부 링크들도 깨져 있었다.

## 네 번째 깨달음: GitHub Pages 제한사항을 이해하다

모든 것을 해결한 후 깨달은 가장 중요한 사실이 있다. GitHub Pages는 보안상의 이유로 모든 Jekyll 플러그인을 지원하지 않는다는 것이다. 

공식적으로 지원하는 플러그인은 다음과 같다:
- jekyll-paginate
- jekyll-redirect-from
- jekyll-relative-links
- jekyll-optional-front-matter
- jekyll-readme-index
- jekyll-default-layouts
- jekyll-sitemap
- jekyll-seo-tag
- jekyll-feed
- jekyll-gist
- jekyll-github-metadata

이 목록에 없는 플러그인을 사용하려면 로컬에서 사이트를 빌드한 후 `_site` 폴더를 GitHub에 업로드하거나, GitHub Actions를 커스터마이징해야 한다.

## 결국 배운 것

3일간의 헤맴 끝에 나는 다음과 같은 교훈을 얻었다:

첫째, **오류 메시지를 읽어야 한다**는 것이다. 나는 40년간 강의를 하면서 학생들에게 항상 "문제를 정확히 분석하라"고 말해왔다. 그런데 나 자신은 GitHub Actions의 상세한 로그를 무시했다.

둘째, **문서를 읽어야 한다**는 것이다. Jekyll과 GitHub Pages의 공식 문서는 매우 상세하다. 구글링을 하기 전에 공식 문서를 먼저 읽었다면 3일을 아낄 수 있었을 것이다.

셋째, **의존성 관리가 얼마나 중요한지** 깨달았다. Gemfile과 Gemfile.lock은 단순한 설정 파일이 아니다. 이것은 로컬 환경과 배포 환경의 일관성을 보장하는 계약서와 같다.

마지막으로, **기술은 나이를 가리지 않는다**는 것을 확인했다. 40년의 경력도, 대학 교수의 경험도 새로운 도구 앞에서는 모두 같은 출발선이다. 하지만 문제를 체계적으로 분석하고, 차근차근 해결하려는 태도는 여전히 유용했다.

지금 내 블로그는 완벽하게 작동한다. GitHub Actions 대시보드에는 초록색 체크 마크가 떴고, 배포된 사이트는 멋진 Chirpy 테마로 반짝인다. 

혹시 당신도 Jekyll과 GitHub Pages를 사용하다가 빌드 실패로 고민하고 있다면, 저 위의 네 가지 실수를 하지 않기를 바란다. 그리고 가장 중요하게는, **GitHub Actions 로그를 먼저 확인하라**. 그 안에 모든 해답이 있다.

당신의 블로그 배포 여정이 나보다 훨씬 순탄하기를 바라며, 혹시 같은 문제로 고민 중이라면 지금 바로 저장소의 Actions 탭을 열어보는 것은 어떨까요?