---
title: "GitHub Pages에서 Jekyll 빌드 실패? 숨겨진 _config.yml 설정이 답이다"
slug: jekyll-github-pages-build-failure-fix
date: 2026-06-05 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그가 GitHub Pages에서 갑자기 빌드 실패하는 문제를 해결하는 _config.yml 설정 팁"
---

## 내가 겪은 문제: 로컬에서는 멀쩡한데 GitHub에서만 안 된다

30년 개발자 생활을 마치고 은퇴 후 블로깅을 시작한 지 3년째다. Jekyll Chirpy 테마로 블로그를 운영하면서 정말 많은 문제를 마주쳤는데, 그중 가장 답답했던 것이 바로 **로컬에서는 완벽하게 빌드되지만 GitHub Pages에 푸시하면 갑자기 빌드가 실패하는 현상**이었다. 

처음엔 정말 미쳤다. 로컬에서 `bundle exec jekyll serve`로 테스트했을 때는 모든 게 잘 작동했다. 마크다운도 렌더링되고, 테마도 적용되고, 포스트도 보인다. 그런데 GitHub에 푸시하는 순간 "Build failed" 이메일이 날아온다. 어디가 문제인지 알 수가 없었다.

처음에는 GitHub Actions 로그를 자세히 봤지만, 에러 메시지가 너무 추상적이었다. "Jekyll failed with exit code 1" 정도가 전부였다. 정말 초보자처럼 느껴졌다. 젊은 개발자들은 이렇게 막막한 걸 어떻게 견디나 싶었다.

## GitHub와 로컬의 빌드 환경 차이 이해하기

문제의 핵심은 **빌드 환경의 차이**였다. 내 로컬 머신과 GitHub의 빌드 서버는 완전히 다른 환경에서 작동한다.

로컬에서는 내 컴퓨터에 설치된 Ruby, Bundler, Jekyll이 모두 최신 버전일 가능성이 높다. 하지만 GitHub Pages는 자신만의 Ruby 버전, Gem 의존성 정책을 가지고 있다. 특히 GitHub Pages는 **특정 버전의 Jekyll과 플러그인만 공식 지원**한다는 점이 중요하다.

예를 들어, 내가 로컬에서 `jekyll-sitemap` 플러그인의 최신 버전(4.0.0)을 사용하고 있어도, GitHub Pages가 지원하는 버전은 1.4.0일 수 있다는 뜻이다. 또한 특정 플러그인은 아예 GitHub Pages에서 지원하지 않는 경우도 있다.

이것을 깨닫기까지 정말 오래 걸렸다. 내가 놓친 부분은 바로 `_config.yml` 파일에 있었다.

## _config.yml의 critical settings: safe mode와 plugins

우리가 주목해야 할 설정은 크게 두 가지다.

**첫째, `safe: true` 설정의 의미다.** GitHub Pages에서는 모든 사이트를 safe mode로 빌드한다. 이것은 보안 때문이다. Unsafe 플러그인(즉, GitHub가 공식 지원하지 않는 플러그인)은 실행되지 않는다.

```yaml
safe: true
```

내가 로컬에서 `safe: false`로 실험하던 플러그인들이 GitHub에서는 작동하지 않은 이유가 바로 이것이었다. 

**둘째, `plugins` 항목을 명확히 정의해야 한다.** 많은 초보자는 `_config.yml`에 플러그인 목록을 쓰지 않는데, 이게 문제의 시발점이 된다. GitHub Pages는 명시된 플러그인만 로드하기 때문이다.

```yaml
plugins:
  - jekyll-feed
  - jekyll-seo-tag
  - jekyll-sitemap
  - jekyll-paginate-v2
```

여기서 **반드시 포함해야 할 플러그인들**이 있다. Jekyll Chirpy 테마를 사용한다면:
- `jekyll-feed`: RSS 피드 생성
- `jekyll-seo-tag`: SEO 메타 태그
- `jekyll-sitemap`: sitemap.xml 생성

이 세 개는 기본이다. 그 외의 플러그인은 **반드시 GitHub Pages 공식 지원 목록**에서 확인해야 한다. 내가 자의로 추가한 플러그인들이 빌드 실패의 원인이었다.

## 나는 어떻게 해결했는가

해결 방법은 단순했다.

1. **GitHub Pages 공식 지원 플러그인 목록 확인** (pages.github.com/versions/)
2. **내 _config.yml의 plugins 섹션을 지원되는 플러그인으로만 정리**
3. **로컬 테스트 환경을 GitHub와 동일하게 설정**

마지막 단계가 중요한데, 내 로컬 Gemfile에 `github-pages` gem을 명시적으로 추가했다.

```ruby
gem "github-pages", group: :jekyll_plugins
```

이렇게 하면 내 로컬 환경이 GitHub Pages의 정확한 Ruby 버전과 의존성을 사용하게 된다. 이후로는 로컬에서 실패하는 빌드는 GitHub에서도 실패하고, 로컬에서 성공하는 빌드는 GitHub에서도 성공했다.

또 하나 추가한 것이 GitHub Actions의 workflow 파일 확인이다. `.github/workflows/pages.yml` 파일을 직접 살펴보니, 특정 Ruby 버전으로 빌드되고 있었다. 이를 통해 내 로컬 Ruby 버전도 맞춰야 한다는 걸 알았다.

은퇴 후 배우는 입장에서 이런 세부 사항들을 하나씩 이해하는 과정이 정말 재미있었다. 요즘 개발 환경의 복잡성을 제대로 체험했다고 할 수 있다. 현역일 때는 팀이 알아서 해줬던 것들이다.

혹시 당신도 같은 문제로 고민 중이라면, 지금 바로 당신의 `_config.yml` 파일을 열어서 plugins 섹션을 GitHub 공식 지원 목록과 비교해보길 강력히 권한다. 아마도 그것이 당신의 빌드 실패 문제를 해결하는 열쇠가 될 것이다.