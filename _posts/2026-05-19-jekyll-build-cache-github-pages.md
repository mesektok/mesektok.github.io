---
title: "GitHub Pages 배포 실패의 악몽에서 벗어나다: Jekyll 빌드 캐시 전략"
slug: jekyll-build-cache-github-pages
date: 2026-05-19 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그의 빌드 실패 문제를 해결한 실제 경험담과 캐시 최적화 전략"
---

## 새벽 3시의 악몽: 갑작스런 배포 실패

대학을 정년퇴직하고 취미로 시작한 Jekyll 블로그 운영. 처음에는 모든 게 완벽했다. GitHub에 마크다운 파일을 푸시하면 자동으로 GitHub Actions가 실행되어 정적 사이트를 생성해주는 마법 같은 경험 말이다. 

그런데 어느 날 아침, 새로 작성한 포스트가 배포되지 않았다. GitHub Actions 로그를 확인해보니 빌드 프로세스가 중간에 실패했다. 당시 저는 Gemfile 의존성 충돌로 인한 Ruby 패키지 버전 문제라고 생각했다. 하지만 문제는 그것만이 아니었다. 시간이 지나면서 패턴이 보이기 시작했다. 자주 사용하는 플러그인들이 캐싱되지 않아서 매번 빌드할 때마다 다시 다운로드되고 있었던 것이다.

## 캐시 전략 없이는 빌드 시간이 배로 늘어난다

은퇴 후 기술 커뮤니티에 복귀하면서 다른 개발자들과 경험을 나누기 시작했다. 한 선배 개발자가 조언해준 것이 바로 GitHub Actions의 캐시 메커니즘이었다. 

당시 제 워크플로우는 기본적인 형태였다. 매번 `bundle install`을 실행할 때마다 모든 gem 의존성을 처음부터 설치하고 있었던 것이다. 특히 Nokogiri나 Image Magick 같은 네이티브 확장이 포함된 패키지는 컴파일 시간까지 더해져서 빌드가 5분을 훌쩍 넘겼다.

해결책은 간단했지만 효과는 뛰어났다. `.github/workflows` 디렉토리의 액션 파일에 캐싱 단계를 추가하는 것이었다. Ruby의 bundler 캐시를 활성화하면, GitHub Actions가 자동으로 `Gemfile.lock`을 기준으로 캐시 키를 생성하고 이전 빌드에서 다운로드한 gem들을 재사용하게 된다.

```yaml
- uses: ruby/setup-ruby@v1
  with:
    ruby-version: '3.2'
    bundler-cache: true
```

이 한 줄의 추가만으로 빌드 시간이 5분에서 45초로 단축되었다. 캐시가 적중할 때는 더 빨랐다. 은퇴 후 여유 시간이 많아서 기술 탐구에 더 집중할 수 있었고, 이런 최적화가 얼마나 중요한지 깨닫게 되었다.

## Jekyll 플러그인과 assets 폴더도 캐싱하자

하지만 캐싱의 여정은 여기서 끝나지 않았다. 시간이 지나면서 또 다른 병목이 드러났다. 제 블로그에는 여러 Jekyll 플러그인들이 설치되어 있었고, 이들이 이미지나 CSS를 처리할 때마다 작은 지연이 누적되었다.

GitHub Actions의 `actions/cache`를 활용해서 더 세밀한 캐싱 전략을 수립했다. 단순히 Ruby gem만 캐싱하는 것을 넘어, Jekyll이 생성하는 `_site` 디렉토리와 중간 build 파일들도 캐싱하기 시작했다.

```yaml
- uses: actions/cache@v3
  with:
    path: |
      .bundle
      vendor/bundle
      _site
    key: ${{ runner.os }}-gems-${{ hashFiles('**/Gemfile.lock') }}
    restore-keys: |
      ${{ runner.os }}-gems-
```

더 중요한 깨달음은 캐시 무효화의 타이밍이었다. 단순히 `Gemfile.lock`의 변경만으로는 부족했다. Jekyll 설정 파일(`_config.yml`)이나 플러그인 설정이 바뀔 때도 캐시를 초기화해야 했다. 이를 위해 해시 키에 여러 파일들을 포함시켜 더욱 정교한 캐시 관리가 가능해졌다.

오랜 기술 경력 동안 배운 것 중 하나는 자동화의 진정한 가치는 시간 절약이 아니라 신뢰성이라는 점이었다. 캐시 전략을 올바르게 구현하면서 배포 실패율도 현저히 낮아졌다. 의존성 해결 과정에서의 네트워크 오류나 타임아웃 문제도 줄어들었기 때문이다.

이제 제 블로그는 한 달에 몇 번의 새로운 포스트를 안정적으로 배포하고 있다. 은퇴 후에도 기술을 계속 배우고 최적화하는 즐거움이 있다는 것을 알게 되었다. 혹시 당신도 Jekyll 블로그의 빌드 시간이 길다면, 지금 바로 GitHub Actions 워크플로우에 캐싱 전략을 추가해보시길 권한다.