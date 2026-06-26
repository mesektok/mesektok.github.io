---
title: "GitHub Pages에서 Jekyll 빌드 실패? 이 한 가지만 확인하세요"
slug: jekyll-github-pages-build-fix
date: 2026-06-26 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그가 GitHub Pages에서 자꾸만 빌드 실패하는 이유와 해결 방법을 실제 경험담과 함께 공유합니다"
---

저는 지난 15년간 소프트웨어 엔지니어로 일해오다가 작년에 은퇴했습니다. 은퇴 후 개인 기술 블로그를 운영하면서 Jekyll과 GitHub Pages를 처음 접했는데, 정말 많은 시행착오를 겪었습니다. 그 과정에서 배운 가장 중요한 교훈을 오늘 여러분과 나누고 싶습니다.

## 처음 만났던 악몽, "Build failed"

블로그를 시작하겠다고 결심했을 때, 저는 Jekyll Chirpy 테마를 선택했습니다. 로컬 환경에서는 완벽하게 작동했습니다. 하지만 GitHub에 커밋한 지 5분 후 메일함에 도착한 것은 "Your site failed to build"라는 메시지였습니다. 

처음엔 당황했습니다. Jekyll 문서를 읽고, 스택오버플로우를 뒤지며 하루종일 삽질했습니다. 그렇게 일주일을 헤매다가 마침내 발견한 범인은 정말 황당했습니다. 바로 `_config.yml` 파일의 단 한 줄의 설정 오류였습니다.

## 대부분의 빌드 실패는 "baseurl" 때문이다

제가 저지른 실수는 이것입니다. `_config.yml`에서:

```yaml
baseurl: "/my-blog"  # 올바른 형식
```

이것을 다음과 같이 설정했었습니다:

```yaml
baseurl: "my-blog"   # 슬래시를 빼먹음
```

GitHub Pages가 리포지토리 이름을 기반으로 사이트를 호스팅할 때, baseurl은 반드시 슬래시로 시작해야 합니다. 이것이 없으면 모든 에셋 경로(CSS, JavaScript, 이미지)가 깨져서 빌드가 실패합니다. GitHub의 자동 빌드 시스템은 이 오류를 명확하게 표시하지 않았기에, 저는 한참을 헤맸던 것입니다.

더 나아가서 중요한 팁을 하나 더 알려드리겠습니다. GitHub Pages의 빌드 프로세스는 로컬 환경과 다릅니다. 로컬에서 `bundle exec jekyll serve`를 실행할 때는 `baseurl`을 무시하고 빌드하기 때문에 문제를 발견하기 어렵습니다. 제대로 테스트하려면:

```bash
bundle exec jekyll serve --baseurl="/my-blog"
```

이렇게 명시적으로 baseurl을 지정해야 합니다.

## GitHub Actions 로그는 당신의 최고의 친구

은퇴 후 기술을 놓친 사람이 저였지만, 이 문제를 해결하면서 배운 것이 있습니다. GitHub Pages 빌드 실패는 항상 **GitHub Actions 로그**에 정확한 오류 메시지가 남아있다는 점입니다.

리포지토리의 "Actions" 탭으로 이동해서 가장 최근의 워크플로우를 클릭하면, 빌드 프로세스의 각 단계별 로그를 볼 수 있습니다. 저는 이 로그를 제대로 읽지 않고 추측으로만 문제를 해결하려니까 며칠을 낭비했습니다.

로그에서 흔히 보이는 오류들은:

- YAML 문법 오류 (`_config.yml` 들여쓰기 잘못)
- 플러그인 호환성 문제 (GitHub Pages가 지원하지 않는 플러그인)
- 인코딩 문제 (특히 한글 포함 파일)
- 의존성 버전 불일치

이런 모든 것들이 Actions 로그에 명시되어 있습니다. 저는 이 사실을 깨닫는 데 너무 오래 걸렸습니다.

## 실전 체크리스트

지난 1년간의 경험으로 만들어본 체크리스트를 공유합니다:

첫째, `_config.yml`를 YAML 검증 도구에서 확인하세요. 온라인 YAML 검증기는 무료로 제공됩니다.

둘째, `Gemfile`에서 Jekyll 버전을 명시적으로 지정하세요. GitHub Pages는 특정 버전의 Jekyll과 플러그인만 지원합니다.

셋째, 로컬에서 baseurl을 명시해서 테스트하세요.

넷째, 모든 이미지 경로가 정확한지 확인하세요. 상대 경로 오류도 빌드 실패를 유발합니다.

은퇴 후 새로운 취미로 기술 블로그를 시작했을 때, 이런 작은 오류들로 몇 시간을 낭비했던 저의 경험이 여러분에게 도움이 되길 바랍니다. GitHub Pages에서 Jekyll을 운영하는 것은 정말 훌륭한 선택이지만, 처음 설정 단계에서의 작은 실수가 큰 좌절감을 가져올 수 있습니다.

지금 바로 여러분의 `_config.yml`을 열어서 baseurl 설정을 확인해보세요. 그리고 GitHub Actions 로그를 살펴보면서 숨어있는 오류가 없는지 점검해보시길 권장합니다.