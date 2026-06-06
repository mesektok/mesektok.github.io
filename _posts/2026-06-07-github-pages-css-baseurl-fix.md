---
title: "GitHub Pages 배포 후 CSS가 안 먹힐 때, 이 한 줄이 해결책이었다"
slug: github-pages-css-baseurl-fix
date: 2026-06-07 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그를 GitHub Pages에 배포했는데 CSS가 적용되지 않는 문제를 baseurl 설정으로 해결하는 방법"
---

## 내가 겪었던 악몽 같은 경험

대학을 은퇴한 지 3년 차, 나는 개발 공부를 다시 시작하기로 마음먹었다. Jekyll과 GitHub Pages로 기술 블로그를 만드는 것이 첫 프로젝트였다. 로컬 환경에서는 완벽했다. 글자 색도 예쁘고, 사이드바도 잘 떴고, 코드 하이라이팅도 멋있었다. 하지만 GitHub Pages에 푸시한 순간, 모든 것이 무너졌다.

검은 배경에 흰 텍스트만 남고, 모든 스타일이 사라져 버렸다. 마치 1990년대 초반 웹사이트를 보는 듯했다. 나는 며칠간 헤맸다. HTML 파일을 여러 번 수정했고, Jekyll을 재설치했으며, GitHub 저장소 설정도 초기화했다. 그런데 답은 매우 간단했다. `_config.yml`의 단 한 줄 설정이 모든 문제를 해결했다.

## baseurl 설정이 왜 중요한가

GitHub Pages의 사용자 페이지(username.github.io)가 아닌 프로젝트 저장소에 블로그를 배포하는 경우, 당신의 사이트는 `https://username.github.io/repository-name/` 경로에서 실행된다. 이것이 핵심이다.

로컬 환경에서 `jekyll serve`를 실행하면, 사이트는 `http://localhost:4000/`에서 실행된다. 이 경우 CSS, JavaScript, 이미지 파일들은 모두 루트(`/`)에서 로드된다. 예를 들어, CSS 파일은 `/assets/css/style.css`에서 찾아진다.

그런데 GitHub Pages에서는 상황이 다르다. 당신의 사이트가 `/repository-name/` 디렉토리 안에 있기 때문에, CSS는 실제로 `/repository-name/assets/css/style.css`에 위치한다. Jekyll이 baseurl을 알지 못하면, CSS를 찾을 수 없게 되는 것이다. 이를 해결하려면 `_config.yml`에 명시적으로 baseurl을 지정해야 한다.

## 해결책: _config.yml 수정

내가 한 것은 매우 간단했다. `_config.yml` 파일을 열어서 다음 줄을 추가했다.

```yaml
baseurl: "/your-repository-name"
```

여기서 `your-repository-name`은 당신의 실제 저장소 이름이다. 예를 들어, 저장소 이름이 `tech-blog`라면:

```yaml
baseurl: "/tech-blog"
```

이 설정을 추가한 후, Jekyll 테마의 레이아웃 파일(보통 `_layouts/default.html`이나 `_includes/head.html`)에서 모든 CSS와 JavaScript 링크가 `{{ site.baseurl }}`을 사용하고 있는지 확인했다. Chirpy 테마는 이미 이렇게 설정되어 있었지만, 커스텀 테마를 사용하는 경우 직접 수정해야 할 수 있다.

```html
<link rel="stylesheet" href="{{ site.baseurl }}/assets/css/style.css">
```

이렇게 하면 로컬과 GitHub Pages 양쪽에서 올바른 경로로 파일을 찾을 수 있다.

## 로컬 테스트 환경에서 baseurl 적용하기

한 가지 주의할 점이 있다. `baseurl`을 설정한 후 로컬에서 테스트할 때는 주의해야 한다. `jekyll serve` 실행 시 자동으로 baseurl이 적용되지 않을 수 있기 때문이다.

로컬에서도 baseurl을 적용한 상태로 테스트하려면:

```bash
jekyll serve --baseurl "/your-repository-name"
```

이렇게 실행하면, 로컬에서도 `http://localhost:4000/your-repository-name/`에서 사이트를 확인할 수 있다. 이 방법으로 테스트하면 실제 배포 환경과 동일한 조건에서 확인할 수 있어 매우 유용하다.

## 마지막 팁

나는 이 문제 해결 후에 한 가지 더 배웠다. 만약 username.github.io 저장소를 사용한다면, baseurl을 비워두거나 설정할 필요가 없다는 점이다. 이 경우 사이트가 루트에서 실행되기 때문이다.

또한 GitHub의 저장소 설정에서 Pages가 올바르게 활성화되어 있는지 확인하는 것도 중요하다. Settings > Pages에서 Source가 'Deploy from a branch'로 설정되어 있고, 올바른 브랜치(보통 main)가 선택되어 있는지 확인하자.

이 경험을 통해 나는 개발에서 작은 설정의 중요성을 깨달았다. 큰 버그라고 생각했던 것이 실은 한 줄의 설정 누락이었다. 혹시 당신도 비슷한 문제로 고민 중이라면, 지금 바로 `_config.yml`의 baseurl 설정을 확인하고 수정해보길 권한다. 당신의 블로그도 곧 멋진 모습으로 돌아올 것이다.