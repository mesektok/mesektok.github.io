---
title: "GitHub Pages 배포 실패? Jekyll 빌드 에러 한 번에 해결하는 법"
slug: jekyll-github-pages-build-errors
date: 2026-07-03 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그를 GitHub Pages에 배포할 때 자주 마주치는 빌드 에러들을 진단하고 해결하는 실전 노하우"
---

## 서론: 내가 겪은 GitHub Pages의 첫 번째 배포 좌절

은퇴한 지 3년 차인 나는 지난여름 갑자기 블로그를 시작하고 싶은 충동이 들었다. 평생을 개발자로 살아온 터라 WordPress 같은 플랫폼은 너무 뻔했고, 자연스럽게 Jekyll과 GitHub Pages에 눈길이 갔다. 그런데 처음 로컬에서는 완벽하게 작동하던 블로그가 GitHub에 푸시하는 순간 build failure 알림이 떴다. 그 순간의 답답함이란! 이 글은 그때의 경험과 이후 1년간 축적된 노하우를 정리한 것이다.

## 가장 흔한 세 가지 빌드 에러와 해결법

### 1. Gemfile 버전 불일치 문제

가장 많이 마주쳤던 에러는 로컬 Ruby 환경과 GitHub Pages 서버의 gem 버전이 다를 때 발생했다. 나는 처음에 Gemfile을 제대로 관리하지 않아 로컬에서 `bundle install`로 설치한 gem 버전이 GitHub의 기본 gem 버전과 충돌했다.

해결책은 간단했다. Jekyll의 공식 문서를 참고하여 Gemfile에 `github-pages` gem을 명시적으로 추가하는 것이다. 이 gem은 GitHub Pages에서 지원하는 모든 플러그인과 의존성을 자동으로 관리해준다. 나는 Gemfile에 다음 줄을 추가했다.

```ruby
gem "github-pages", "~> 232", group: :jekyll_plugins
```

이후 `bundle update`를 실행하고 `Gemfile.lock`을 커밋했더니 더 이상 버전 충돌이 발생하지 않았다. 지금 생각해보니 이 과정이 얼마나 간단한지 당시에 왜 못 알아챘는지 아쉽기만 하다.

### 2. 플러그인 호환성 에러

GitHub Pages는 모든 Jekyll 플러그인을 지원하지 않는다. 나는 처음 `jekyll-sitemap` 플러그인을 마음대로 추가했다가 배포 후 에러를 맞닥뜨렸다. GitHub Pages는 보안 상의 이유로 특정 공식 플러그인만 화이트리스트에 등록해두었기 때문이다.

GitHub에서 지원하는 플러그인 목록은 공식 문서에 명시되어 있다. 나는 필요한 기능들을 모두 이 목록에서 찾아 사용하도록 변경했다. 예를 들어 `jekyll-feed`, `jekyll-seo-tag`, `jekyll-sitemap` 같은 공식 지원 플러그인들로 교체했으며, 모두 정상 작동했다.

### 3. YAML 프론트매터와 인코딩 문제

이건 정말 황당한 경험이었다. 로컬에서는 완벽히 빌드되는데 GitHub에서만 실패했던 것이다. 나중에 알고 보니 마크다운 파일의 문자 인코딩이 UTF-8 BOM(Byte Order Mark)으로 설정되어 있었다. GitHub의 Jekyll은 순수 UTF-8만 인식하기 때문에 BOM이 포함되면 YAML 파싱에서 실패했다.

해결책은 모든 마크다운 파일을 BOM 없는 UTF-8로 변환하는 것이었다. VSCode를 사용한다면 파일 우측 하단의 인코딩 버튼을 클릭해 "UTF-8"을 선택하면 된다. 이 한 번의 수정으로 그동안의 스트레스가 모두 사라졌다.

## 빌드 에러를 사전에 예방하는 체크리스트

이제 나는 언제나 새 포스트를 작성한 후 GitHub에 푸시하기 전에 로컬에서 완전히 동일한 환경을 시뮬레이션한다. `bundle exec jekyll build` 명령어로 정확히 GitHub와 같은 방식으로 빌드를 테스트하는 것이다. 이 과정을 거치면 99%의 에러를 사전에 잡을 수 있다.

또한 GitHub Actions를 활용한 자동 배포 설정을 권장한다. `.github/workflows/` 디렉토리에 배포 스크립트를 추가하면 푸시 후 빌드 실패 여부를 즉시 알 수 있고, 에러 로그도 상세하게 확인할 수 있다.

## 결론: 블로그 운영의 안정성은 기초부터

은퇴 후 블로그를 통해 그동안의 경험을 나누는 것이 얼마나 즐거운지 몸소 느끼고 있다. 하지만 그 즐거움이 기술적 장벽으로 인해 방해받아서는 안 된다. 이 글에서 소개한 세 가지 에러 해결법과 예방 체크리스트가 누군가의 좌절을 조금이라도 덜어줄 수 있다면 좋겠다.

당신도 지금 Jekyll 블로그 배포에서 막혔다면, 이 글의 해결법들을 차례로 시도해보길 권한다. 그리고 해결되었다면 댓글로 당신의 경험과 추가 팁을 공유해주면 더욱 감사하겠다.