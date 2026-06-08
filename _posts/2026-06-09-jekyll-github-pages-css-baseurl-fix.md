---
title: "GitHub Pages에서 Jekyll 블로그 배포 후 CSS가 안 먹히는 이유, 드디어 찾았습니다"
slug: jekyll-github-pages-css-baseurl-fix
date: 2026-06-09 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages, CSS, Troubleshooting]
description: "Jekyll 블로그를 GitHub Pages에 배포했는데 CSS가 적용되지 않는 문제의 근본 원인과 해결 방법"
---

## 은퇴 후 블로깅을 시작하며 겪은 난제

컴퓨터공학을 40년간 가르친 후 은퇴한 지 이제 2년. 한가로운 시간을 채우기 위해 기술 블로그를 시작했습니다. Jekyll과 GitHub Pages는 비용도 없고, 마크다운으로 글을 쓸 수 있어 매력적이었습니다. Chirpy 테마를 설치하고 로컬에서는 완벽하게 작동했는데, GitHub Pages에 배포한 순간 CSS가 모두 사라진 것처럼 보였습니다. 흰 배경에 텍스트만 떠 있는 참담한 화면을 마주했을 때의 당혹감을 잊을 수 없습니다.

처음엔 테마 파일이 제대로 업로드되지 않았거나 Jekyll 빌드 과정에서 에러가 발생했다고 생각했습니다. GitHub Actions 로그도 확인했고, 저장소 설정도 여러 번 뒤졌습니다. 그런데 이틀을 헤맨 후, 답은 의외로 간단한 곳에 있었습니다. 바로 `_config.yml` 파일의 `baseurl` 설정이었습니다.

## baseurl 설정이 CSS 경로를 좌우한다

Jekyll은 사이트를 생성할 때 모든 리소스의 경로를 생성합니다. 만약 GitHub Pages 저장소 이름이 `username.github.io`가 아니라 `my-blog`라면, 실제 배포 URL은 `https://username.github.io/my-blog/`가 됩니다. 이 경우 CSS, JavaScript, 이미지 같은 모든 정적 자산의 경로도 `/my-blog/`를 기준으로 설정되어야 합니다.

저는 이 기본 개념을 놓쳤습니다. `_config.yml`에서 `baseurl: ""` 또는 baseurl을 아예 설정하지 않았거든요. 그러면 로컬에서 `jekyll serve`를 실행했을 때는 `http://localhost:4000`을 기준으로 모든 경로가 설정되어 정상 작동합니다. 하지만 GitHub Pages에 배포되면 브라우저는 CSS를 `/assets/css/style.css`에서 찾으려다가 실제로는 `/my-blog/assets/css/style.css`에 있으니까 404 에러가 발생하는 것입니다.

Chirpy 테마는 기본적으로 `baseurl` 변수를 활용하도록 설계되어 있습니다. 레이아웃 파일들을 살펴보면 이미지 경로나 스크립트 경로 앞에 `{{ site.baseurl }}`이 붙어 있습니다. 따라서 `_config.yml`에서 올바른 baseurl을 설정하기만 하면 모든 경로 문제가 자동으로 해결됩니다.

## 올바른 설정 방법과 검증 과정

저는 결국 이렇게 해결했습니다. 먼저 자신의 GitHub Pages 저장소 이름을 확인합니다. 만약 저장소 이름이 `my-learning-blog`라면, `_config.yml`에 다음과 같이 입력합니다:

```yaml
baseurl: /my-learning-blog
```

`/`로 시작하고 끝나지 않음에 주의합니다. Jekyll은 이를 자동으로 처리합니다. 설정 후 로컬에서 `jekyll serve`를 다시 실행하면, `http://localhost:4000/my-learning-blog/`로 접속해야 합니다. 이제 로컬 환경도 실제 배포 환경과 동일한 경로 구조를 가지게 되므로, 배포 후 예상치 못한 CSS 누락 문제를 방지할 수 있습니다.

검증 방법도 중요합니다. GitHub에 푸시한 후 Actions 탭에서 빌드 로그를 확인하고, 배포가 완료되면 웹 개발자 도구(F12)를 열어 Network 탭에서 CSS 파일이 제대로 로드되는지 확인합니다. 만약 여전히 빨간색 404 에러가 표시되면, baseurl 설정을 다시 점검해야 합니다.

추가로, `username.github.io` 형식의 저장소를 사용한다면 baseurl을 빈 문자열로 설정해도 됩니다. 이 경우 루트 URL이 사이트의 홈이 되므로 경로 설정이 간단합니다. 하지만 일반 저장소 이름을 사용한다면 반드시 baseurl을 설정해야 합니다.

40년간 학생들을 가르치며 배운 교훈 중 하나는 '기초가 가장 중요하다'는 것입니다. Jekyll도 마찬가지입니다. baseurl이라는 기초 설정 하나를 제대로 이해하면, GitHub Pages에서의 배포 문제 대부분은 사라집니다. 이제 저는 매주 화요일과 토요일에 블로그를 업데이트하며 기술 경험을 나누고 있습니다.

혹시 당신도 Jekyll 블로그의 CSS 문제로 고민 중이라면, 지금 바로 `_config.yml`을 열어 baseurl 설정을 확인해보세요.