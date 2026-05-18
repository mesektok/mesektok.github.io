---
title: "GitHub Pages에서 Jekyll 빌드 실패? 나는 이렇게 해결했다"
slug: jekyll-github-pages-build-fix
date: 2026-05-19 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "GitHub Pages의 Jekyll 빌드 오류를 디버깅하고 해결하는 실전 노하우"
---

## 내 첫 GitHub Pages 블로그가 검은 화면이 되던 날

은퇴 후 취미로 기술 블로그를 시작하려고 GitHub Pages에 Jekyll을 올렸을 때의 일이다. 리포지토리는 깔끔하게 설정했고, 로컬에서는 `jekyll serve` 명령어로 완벽하게 작동했다. 그런데 GitHub에 push한 지 5분 후, 이메일 한 통이 날아왔다. "Your site failed to build."

당시 나는 프로그래밍을 전공하긴 했지만, 20년 전 기술이었다. 요즘의 CI/CD나 자동 빌드 시스템은 낯설었다. 하지만 문제를 해결해야 했다. 그렇게 시작된 디버깅 여정에서 배운 것들을 공유하려고 한다.

## GitHub Actions 로그를 읽는 것이 모든 것의 시작이다

가장 기본적이지만 많은 사람들이 놓치는 부분이 바로 에러 메시지를 확인하는 것이다. GitHub 리포지토리의 **Settings → Pages** 섹션을 들어가면, 빌드 상태를 확인할 수 있다. 하지만 더 자세한 정보는 **Actions** 탭에서 찾을 수 있다.

나는 처음에 무작정 `_config.yml` 파일을 수정했고, Gemfile을 바꿔보고, 플러그인을 제거해봤다. 하지만 에러 메시지를 제대로 읽지 않았기에 같은 실수를 반복했다. 그 후로 나는 항상 **Actions 탭의 workflow 로그**를 먼저 확인하는 습관을 들였다.

예를 들어, 내 경우에는 `Liquid Exception: Undefined variable` 에러가 떴었다. 이는 특정 포스트의 front matter에서 참조하는 변수가 정의되지 않았다는 뜻이었다. 로그가 없었다면 이 문제를 찾는 데 몇 시간을 더 낭비했을 것이다.

## 로컬 환경과 GitHub Pages의 미묘한 차이를 아는 것이 중요하다

GitHub Pages는 기본적으로 **safe 모드**로 Jekyll을 실행한다. 이는 특정 플러그인들이 작동하지 않는다는 의미다. 내가 직면했던 문제 중 하나는 로컬에서는 완벽하게 작동하는 커스텀 플러그인이 GitHub에서 작동하지 않는 것이었다.

로컬에서 테스트할 때는 `jekyll serve`를 사용했지만, 실제 GitHub Pages 환경을 정확히 시뮬레이션하려면 `bundle exec jekyll build --safe`를 실행해야 한다. 이 한 줄의 명령어가 나를 얼마나 많은 시간에서 구했는지 모른다.

또한, Ruby 버전 문제도 고려해야 한다. Gemfile에 명시된 Ruby 버전과 GitHub Pages에서 사용하는 버전이 다를 수 있다. GitHub Pages는 특정 버전의 Ruby를 고정으로 사용하므로, 호환성을 확인해야 한다. `.ruby-version` 파일을 추가하고, Gemfile에 `ruby '3.2.0'` 같은 명시적 버전을 추가하는 것이 좋다.

## 의존성 관리와 theme 설정이 생각보다 복잡하다

나는 Chirpy 테마를 사용하고 있다. 훌륭한 테마지만, 초기 설정에서 여러 번 실패했다. 특히 `gem "jekyll-theme-chirpy"` 대신 GitHub Pages 공식 지원 테마들을 사용하려고 했을 때 문제가 생겼다.

올바른 방식은 remote_theme을 사용하는 것이다. Gemfile에서 theme gem을 제거하고, `_config.yml`에 `remote_theme: cotes2020/jekyll-theme-chirpy@master` 같은 형태로 명시하면 된다. 이렇게 하면 GitHub Pages가 자동으로 테마를 다운로드하고 빌드한다.

또 다른 교훈은 플러그인의 순서다. `_config.yml`의 plugins 섹션에 나열하는 순서가 중요할 수 있다. 나는 `jekyll-seo-tag`를 다른 플러그인보다 뒤에 배치했다가 메타 태그가 제대로 생성되지 않는 문제를 겪었다.

## 실제로 해본 체크리스트

지금 나는 새 프로젝트마다 이 체크리스트를 실행한다. 첫 번째는 로컬 빌드 테스트다. `bundle install`로 의존성을 설치하고 `bundle exec jekyll serve --safe`로 로컬 서버를 실행한다. 모든 페이지가 정상 렌더링되는지 확인한다.

두 번째는 GitHub 설정이다. Pages 섹션에서 브랜치를 올바르게 설정했는지, 빌드 원본이 GitHub Actions인지 확인한다. 세 번째는 `_config.yml`의 url과 baseurl을 정확히 설정하는 것이다. 도메인이 `username.github.io`라면 baseurl은 비워두되, 프로젝트 페이지라면 프로젝트명을 기입한다.

네 번째는 front matter 검증이다. 모든 포스트 파일이 올바른 형식의 front matter를 갖고 있는지 확인한다. 다섯 번째는 한글 인코딩이다. 파일들이 UTF-8로 저장되어 있는지 꼭 확인해야 한다.

## 마지막 팁: 커뮤니티의 힘

내가 배운 가장 중요한 교훈은 혼자 고민하지 말라는 것이다. GitHub 이슈, Stack Overflow, Jekyll 공식 문서를 적극적으로 활용했다. 내가 겪은 문제는 대부분 이미 누군가 겪었던 문제였다. 에러 메시지를 그대로 검색하면 해답을 찾을 확률이 매우 높다.

지금은 내 GitHub Pages 블로그가 완벽하게 작동하고 있다. 은퇴 후 취미로 시작한 것이지만, 이 과정에서 얻은 경험은 매우 소중하다. 혹시 당신도 Jekyll 빌드 오류로 고민하고 있다면, 먼저 GitHub Actions 로그를 확인하고, 로컬에서 `--safe` 옵션으로 테스트해보기를 강력히 추천한다. 그리고 마지막으로, 이 글의 내용을 바탕으로 당신의 GitHub Pages 블로그도 한 번 점검해보세요!