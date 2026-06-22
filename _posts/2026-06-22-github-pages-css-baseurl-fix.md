---
title: "GitHub Pages 배포 후 CSS가 안 먹히는 문제, 20년 경력도 헷갈린다"
slug: github-pages-css-baseurl-fix
date: 2026-06-22 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "GitHub Pages에서 CSS가 로드되지 않는 baseUrl 문제의 원인과 해결책을 실제 경험담으로 풀어낸 가이드"
---

## 나도 한 번 당했다, Jekyll의 baseUrl 함정

20년을 개발 일선에 있다가 은퇴한 지금, 여유 시간에 블로그를 시작했다. 로컬 환경에서는 완벽했다. HTML도 깔끔하고 스타일도 살아있었다. 그런데 GitHub Pages에 올리는 순간 순식간에 망가져버렸다. 웹폰트는 안 보이고, 레이아웃은 부서지고, 마치 1990년대 웹사이트를 보는 기분이었다. CSS 파일이 분명히 서버에 올라갔는데 왜 적용이 안 될까?

당황해서 개발자 도구를 열었다. 네트워크 탭을 보니 CSS 파일의 경로가 이상했다. 로컬에서는 `/assets/css/style.css`로 로드되었는데, GitHub Pages에서는 `/repository-name/assets/css/style.css` 경로를 찾고 있었다. 아, 이게 baseUrl 문제구나 하는 생각이 들었다.

## GitHub Pages의 baseUrl 설정 이해하기

GitHub Pages의 배포 방식은 두 가지다. 첫째는 사용자 페이지(`username.github.io` 저장소)로 배포하는 방식이고, 둘째는 프로젝트 저장소에서 배포하는 방식이다. 이 두 방식의 결정적 차이가 바로 baseUrl이다.

사용자 페이지로 배포하면 `https://username.github.io`가 루트가 되지만, 프로젝트 저장소로 배포하면 `https://username.github.io/repository-name`이 루트가 된다. 즉, 모든 리소스 경로 앞에 저장소 이름이 붙어야 한다는 뜻이다.

Jekyll은 이를 자동으로 처리하기 위해 `_config.yml`에 baseUrl 설정을 제공한다. 내가 처음 실수한 부분이 바로 여기였다. `_config.yml` 파일을 열었더니 baseUrl이 비어있었다. 프로젝트 저장소에 배포했는데도 baseUrl을 설정하지 않았으니 CSS가 로드될 리가 없었다.

해결책은 간단했다. `_config.yml`에 다음과 같이 추가하면 된다:

```yaml
baseurl: "/repository-name"
```

그리고 테마 파일의 모든 정적 리소스 경로 앞에는 `{{ site.baseurl }}`을 붙여야 한다. 예를 들어 CSS 링크는 이렇게:

```html
<link rel="stylesheet" href="{{ site.baseurl }}/assets/css/style.css">
```

## 로컬 테스트도 baseUrl을 반영해야 한다

여기서 또 다른 함정이 있다. 로컬 환경에서 테스트할 때다. `jekyll serve` 명령어로 실행하면 baseUrl을 무시하고 로컬 서버가 구동된다. 그래서 로컬에서는 완벽하게 작동하지만 GitHub Pages에 올리면 깨지는 현상이 발생한다.

나는 다음과 같은 해결책을 찾았다. 로컬 테스트용 `_config_local.yml` 파일을 따로 만들어서 baseUrl을 비워두고, 실제 배포용 `_config.yml`에만 baseUrl을 설정하는 것이다. 로컬 테스트 시에는 이렇게 실행한다:

```bash
jekyll serve --config _config_local.yml
```

또는 더 정확하게 하려면:

```bash
jekyll serve --baseurl=""
```

이렇게 하면 로컬과 배포 환경 간 리소스 로드 경로의 차이를 정확히 시뮬레이션할 수 있다. 아마도 이 방법을 처음부터 알았다면 내가 겪은 혼란은 없었을 것이다.

## 그 외 체크해야 할 사항들

CSS 문제의 근본 원인이 baseUrl이 아닐 수도 있다. 우선 GitHub Pages의 배포 상태를 확인해야 한다. 저장소의 Settings 탭에서 Pages 섹션을 보면 배포 상태와 URL이 명시되어 있다. 만약 배포가 실패했다면 빨간 X 표시가 나타난다.

또한 브라우저의 개발자 도구에서 네트워크 탭을 꼭 확인하자. 404 에러가 뜨면 경로 문제이고, 200이지만 적용 안 되면 다른 CSS가 덮어씌우고 있을 가능성이 높다. Chirpy 테마처럼 복잡한 테마를 사용한다면 CSS 로드 순서도 확인해야 한다.

마지막으로 혼합 콘텐츠(Mixed Content) 문제도 체크할 가치가 있다. HTTPS 페이지에서 HTTP 리소스를 로드하려고 하면 브라우저가 차단할 수 있다.

이제 당신도 이런 함정에 빠지지 말고, baseUrl 설정부터 정확히 한 후 GitHub Pages에 배포해보자. 20년 개발자도 헷갈렸던 부분이니 처음 경험하는 분들이라면 더욱 신경 써야 한다.