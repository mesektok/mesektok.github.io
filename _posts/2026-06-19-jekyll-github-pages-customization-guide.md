---
title: "GitHub Pages에서 Jekyll 테마 커스터마이징할 때 겪는 '그 문제'를 해결하는 법"
slug: jekyll-github-pages-customization-guide
date: 2026-06-19 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages, 블로깅]
description: "Jekyll Chirpy 테마 커스터마이징 과정에서 발생하는 빌드 오류와 배포 문제를 실제 경험을 바탕으로 해결하는 방법"
---

## 은퇴 후 블로깅을 시작하며 마주친 예상 밖의 난관

컴퓨터공학과에서 30년간 강의한 후 은퇴한 지 2년. 마침내 시간이 남아 그동안 못한 기술 블로깅을 시작하기로 마음먹었습니다. GitHub Pages와 Jekyll은 이미 강의 자료로 다뤄봤던 도구였기에, 아무 걱정 없이 시작했습니다. 하지만 현실은 가혹했습니다. Chirpy 테마를 적용하고 로컬에서는 완벽하게 보이던 블로그가 GitHub에 푸시하는 순간 엉망이 되어버린 것입니다.

당시 제 마음가짐은 "이런 게 뭐하는 짓인가" 였습니다. 그러나 3개월간의 시행착오 끝에 터득한 해결책들을 이제 여러분과 나누려 합니다.

## Jekyll 빌드 오류: 로컬과 GitHub의 '미스매치' 문제

첫 번째 난관은 gem 버전 호환성 문제였습니다. 저는 로컬 개발 환경에서 최신 버전의 Ruby와 Jekyll을 사용했는데, GitHub Pages는 특정 버전의 gems만 지원합니다. 

제 경험상 가장 흔한 오류는 `jekyll-seo-tag`와 `jekyll-feed` 플러그인의 버전 충돌이었습니다. Gemfile을 수정할 때 주의할 점은 GitHub Pages가 공식 지원하는 의존성 목록을 정확히 파악해야 한다는 것입니다. GitHub의 공식 문서에 나온 `github-pages` gem을 사용하면 모든 호환 가능한 의존성을 자동으로 설치할 수 있습니다.

제가 적용한 해결책은 Gemfile에 다음처럼 명시하는 것이었습니다.

```ruby
gem "github-pages", "~> 231", group: :jekyll_plugins
```

이렇게 하면 로컬과 GitHub 환경이 동일한 버전의 gems를 사용하게 되어, "로컬에서는 되는데 GitHub에선 안 된다"는 악몽에서 벗어날 수 있습니다. 또한 번들 업데이트 전에 항상 `bundle lock --update github-pages`를 실행하여 의존성 변경 사항을 추적하는 습관도 중요합니다.

## CSS와 이미지 경로: 상대 경로의 함정

두 번째 문제는 경로 설정이었습니다. Chirpy 테마에서 커스텀 CSS를 추가하거나 이미지를 삽입할 때, 저는 상대 경로를 사용했고 로컬에서는 완벽했습니다. 하지만 GitHub Pages에 배포되자 이미지가 깨지고 CSS가 적용되지 않았습니다.

원인은 `baseurl` 설정이었습니다. Repository 이름이 `username.github.io`가 아니라면, `_config.yml`에 반드시 baseurl을 명시해야 합니다. 예를 들어 Repository가 `my-blog`라면:

```yaml
baseurl: "/my-blog"
url: "https://username.github.io"
```

더 나아가 모든 이미지와 CSS 참조를 다음처럼 수정해야 합니다:

```markdown
![이미지설명]({{ site.baseurl }}/assets/images/photo.jpg)
```

처음에는 이 문제 때문에 밤샜던 기억이 납니다. 70대의 제가 console에서 개발자 도구를 켜고 네트워크 탭을 분석하며 404 오류를 추적하는 모습은 꽤 우습지 않았을까 싶습니다.

## GitHub Actions 워크플로우: 자동 배포의 영묘한 설정

세 번째로 배웠던 부분은 GitHub Actions 워크플로우입니다. Chirpy 테마의 최신 버전은 자동 배포를 위해 특정한 GitHub Actions 설정을 요구합니다. 초기에는 `gh-pages` 브랜치에 수동으로 파일을 푸시했는데, 이는 매우 비효율적이었습니다.

올바른 워크플로우 설정은 `.github/workflows/pages-deploy.yml` 파일을 생성하고, GitHub의 공식 문서에 따라 빌드 및 배포 자동화를 구성하는 것입니다. 저는 초기에 토큰 권한 설정을 간과했고, 이로 인해 배포 과정이 반복적으로 실패했습니다. Settings > Actions > General에서 Workflow permissions를 "Read and write permissions"로 설정한 후에야 문제가 해결되었습니다.

또한 Jekyll 빌드 과정에서 liquid 문법이 포함된 마크다운 파일을 올바르게 처리하는 것도 중요합니다. 코드 블록에 liquid 문법이 있다면 `raw` 태그로 감싸야 빌드 오류를 방지할 수 있습니다.

## 현재의 마음가짐과 전하고 싶은 말

지금 저는 매월 안정적으로 기술 포스트를 게시하고 있습니다. 이 과정에서 배운 것은 "자동화가 완벽하다고 생각하지 말 것"이었습니다. 로컬 빌드 후 항상 최종 확인을 하고, GitHub 배포 후에도 실제 웹사이트를 방문하여 검증하는 습관이 중요합니다.

제 실수들이 여러분의 시간과 스트레스를 절약하기를 바랍니다. 지금 바로 여러분의 `_config.yml` 파일을 열어 baseurl과 플러그인 설정을 확인해보세요. 그리고 로컬 빌드와 GitHub 배포 사이의 차이를 경험하신다면, 이 글의 해결책들을 하나씩 시도해보시기를 권장합니다.