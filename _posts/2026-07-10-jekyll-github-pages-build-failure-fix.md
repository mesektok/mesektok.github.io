---
title: "GitHub Pages에서 Jekyll 블로그가 빌드 실패하는 이유, 20년 경력 개발자의 해결책"
slug: jekyll-github-pages-build-failure-fix
date: 2026-07-10 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages, 블로그]
description: "Jekyll 블로그의 빌드 실패 문제를 진단하고 해결하는 실전 방법을 소개합니다."
---

## 은퇴 전 마지막 프로젝트, Jekyll 블로그와의 씨름

저는 소프트웨어 개발자로 거의 40년을 일했습니다. 은퇴를 앞두고 지식을 정리하고 싶어 GitHub Pages에 Jekyll 블로그를 만들기로 결심했습니다. "이 정도야 쉽겠지"라고 생각했던 저의 순진함이 문제였습니다. 첫 포스팅을 올리려던 날, GitHub Actions 로그에는 빨간 X 표시만 가득했습니다. "Build failed" – 이 단 두 글자가 저를 밤새도록 깨어있게 만들었습니다.

지난 3개월간 이 문제들을 하나하나 해결하면서 깨달은 점들이 많습니다. 젊은 개발자들이 비슷한 시행착오를 겪지 않기를 바라며, 제 경험을 나누고자 합니다.

## 가장 흔한 실수: Ruby 버전 불일치

Jekyll 블로그가 로컬에서는 잘 돌아가는데 GitHub에서만 빌드가 실패하는 경우, 대부분 Ruby 버전 때문입니다. 저도 처음엔 이걸 모르고 한 시간 이상을 헤맸습니다.

GitHub Pages는 현재 Ruby 3.3.x 환경을 기본으로 사용합니다. 그런데 저는 로컬에 Ruby 2.7이 설치되어 있었고, 그 환경에서만 테스트했습니다. Gemfile에 `ruby "3.3.0"` 라인을 명시적으로 추가하고, `.github/workflows/jekyll.yml` 파일에서도 정확히 동일한 버전을 지정해야 합니다. 저는 이것을 `ruby-version: '3.3'`로 설정했습니다.

또 한 가지 배운 점은 `Gemfile.lock` 파일의 중요성입니다. 이 파일은 절대로 `.gitignore`에 넣으면 안 됩니다. 이 파일이 없으면 GitHub가 gem 의존성을 정확히 파악할 수 없어 빌드 중에 버전 충돌이 발생합니다. 저는 이 파일을 실수로 깃에서 제거했다가 다시 복구해야 했습니다.

## 플러그인 호환성 문제, 그리고 safe 모드

은퇴 전 마지막 기술 공부 중 가장 답답했던 부분은 플러그인 호환성 문제였습니다. 로컬에서는 멋진 플러그인들이 잘 작동하는데, GitHub Pages에 올리면 갑자기 작동하지 않는 것입니다.

그 이유는 GitHub Pages가 safe mode로 Jekyll을 실행하기 때문입니다. 이는 보안상의 이유로, 공개 저장소의 임의 코드 실행을 방지하기 위한 것입니다. 따라서 GitHub Pages가 공식적으로 지원하는 플러그인들만 사용 가능합니다. 지원 목록은 [GitHub Pages 공식 문서](https://pages.github.com/versions/)에서 확인할 수 있습니다.

저는 처음에 `jekyll-seo-tag`, `jekyll-feed` 같은 표준 플러그인들은 작동하는데, 제가 설치한 커스텀 플러그인들이 작동하지 않아 헷갈렸습니다. 이 문제를 해결하려면 Gemfile에서 `group :jekyll_plugins`에만 플러그인을 넣고, 허용된 플러그인 목록 안에서만 선택해야 합니다. 저는 결국 Chirpy 테마에서 권장하는 플러그인들로 정렬했습니다.

## 로컬 빌드와 GitHub 빌드 환경을 동일하게 만들기

제 은퇴 준비 프로젝트에서 배운 가장 값진 교훈은, 로컬과 GitHub 빌드 환경을 정확히 동일하게 유지해야 한다는 점입니다. 

`bundle update` 명령어를 정기적으로 실행하되, Gemfile.lock 파일은 깃에 커밋합니다. 제 경우 GitHub Actions가 `bundle install --frozen` 옵션으로 실행되므로, Gemfile.lock의 정확성이 매우 중요합니다.

또한 `_config.yml` 파일에서 `theme: jekyll-theme-chirpy`를 명시적으로 지정했습니다. 이전에는 테마를 수동으로 설치했는데, 공식 방식은 Gemfile에 `gem "jekyll-theme-chirpy"` 형태로 추가하는 것입니다. 

로컬에서 `bundle exec jekyll serve`로 항상 테스트하고, GitHub에 푸시하기 전에 빌드 로그를 확인하는 습관을 들였습니다. GitHub 저장소 Settings > Pages 섹션에서 최근 배포 상태를 확인할 수 있습니다.

지난 3개월간의 이 여정이 제 은퇴를 맞이하면서 새로운 취미를 시작하게 해주었고, 여전히 배우고 성장할 수 있다는 깨달음을 주었습니다. 혹시 당신도 Jekyll 블로그 빌드 에러로 고민이라면, 위의 해결책들을 차례대로 시도해보시기 바랍니다.

지금 바로 당신의 `Gemfile.lock`을 확인하고, GitHub Actions 빌드 로그를 자세히 읽어보세요. 그 답이 거기 있습니다.