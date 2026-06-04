---
title: "GitHub Pages 배포 실패의 90%는 _config.yml 설정 때문이었다"
slug: github-pages-config-yml-troubleshooting
date: 2026-06-05 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "20년 개발 경력의 노교수가 알려주는 Jekyll 블로그 배포 실패 원인과 해결법"
---

## 머리를 싸매던 그 밤을 기억한다

정년을 맞이하고 본격적으로 기술 블로그를 운영하기로 마음먹은 것이 2024년 초였습니다. 젊은 시절 C언어와 Java로 밥을 먹었던 터라, 요즘은 뭐가 유행하는지 따라가려는 심정이었죠. GitHub Pages에 Jekyll을 올려서 개인 블로그를 만들어보자는 목표였는데, 첫 배포 시도는 참담했습니다. 

로컬에서는 `jekyll serve`로 완벽하게 작동했던 블로그가 GitHub에 푸시하는 순간 빨간 X 표시만 반복되었습니다. Actions 탭을 열어봐도 "Build failed"라는 막연한 메시지만 있었고, 당시 저는 "요즘 것들은 대체 뭐가 문제인가" 하면서 하루종일 디버깅에 시간을 썼습니다. 그런데 우연히 발견한 것이 _config.yml의 작은 설정 오류였습니다. 그 이후로 제 주변 개발자 선후배들에게 "GitHub Pages 배포 안 되지? _config.yml 먼저 확인해봐"라는 말을 수십 번은 했을 겁니다.

## _config.yml의 주범 항목들

정확한 진단이 없었다면 저도 계속 헛짚었을 겁니다. Jekyll 공식 문서를 꼼꼼히 읽고 실제 오류 로그를 추적한 결과, **_config.yml의 특정 항목들이 배포 실패의 주요 원인**임을 알게 되었습니다.

가장 흔한 문제는 `url` 설정입니다. GitHub Pages는 기본적으로 `https://username.github.io` 형식의 URL을 사용하는데, 이곳에 http를 입력하거나 뒤에 슬래시를 붙이지 않으면 경로 인식에 혼란이 생깁니다. 저는 처음에 `url: http://github.io`라고 입력했다가 나중에 `url: https://myusername.github.io`로 정정했습니다.

다음은 `baseurl` 설정입니다. 만약 프로젝트 기반 저장소(개인 저장소가 아닌 경우)를 사용한다면 반드시 `baseurl: /repository-name`을 입력해야 합니다. 개인 저장소라면 빈 문자열이나 슬래시만 입력하면 되죠. 저는 두 가지를 헷갈려서 꽤 오래 고민했습니다.

세 번째는 **theme 설정**입니다. Jekyll Chirpy 테마를 사용할 계획이었다면 `theme: jekyll-theme-chirpy`를 명시해야 합니다. 아니면 `remote_theme: cotes2020/jekyll-theme-chirpy`로 원격 테마를 지정하는 방법도 있습니다. 저는 처음엔 테마 폴더만 있으면 자동으로 인식될 줄 알았는데, GitHub Actions에서 빌드될 때는 명시적 선언이 필수였습니다.

## 로컬과 서버 환경의 차이를 이해하기

대학에서 20년간 학생들을 가르친 경험으로 미루어보면, 사람들은 자신의 로컬 환경에만 의존하는 경향이 있습니다. "내 컴퓨터에서는 되는데?"라는 말을 수백 번은 들었죠. Jekyll도 마찬가지입니다.

로컬에서 `jekyll serve`를 실행할 때는 Ruby 환경, Gem 의존성, 플러그인들이 모두 설치되어 있습니다. 하지만 GitHub Actions에서는 기본 환경에서만 빌드되기 때문에 일부 플러그인은 작동하지 않습니다. 예를 들어 `jekyll-admin`이나 커스텀 플러그인들은 GitHub Pages 환경에서 차단될 수 있습니다.

또한 YAML 형식의 엄격함도 문제가 됩니다. 로컬에서 들여쓰기가 약간 잘못되어도 Ruby가 관대하게 넘어갈 수 있지만, GitHub Actions 빌드 환경은 훨씬 엄격합니다. 특히 `exclude`, `include`, `plugins` 섹션의 배열 형식을 제대로 맞춰야 합니다. 저는 이 차이를 알기 위해 정말 오랜 시간이 필요했습니다.

## 효율적인 디버깅 방법

이제는 제 경험을 토대로 체계적인 접근 방식을 권장합니다. 첫째, **GitHub Actions 로그를 정확히 읽기**입니다. 저장소의 Actions 탭에서 실패한 워크플로우를 클릭하면 상세한 빌드 로그를 볼 수 있습니다. 단순한 "Build failed"가 아니라, 어느 줄에서 어떤 에러가 났는지 명시되어 있거든요.

둘째, **_config.yml을 온라인 YAML 검증 도구로 확인**하기입니다. `yamllint.com` 같은 사이트에서 파일 내용을 붙여넣으면 구문 오류를 즉시 찾을 수 있습니다. 저는 이 방법으로 들여쓰기 오류를 무수히 찾아냈습니다.

셋째, **로컬 환경을 GitHub 환경과 동일하게 설정**하기입니다. `Gemfile`을 사용하면 로컬에서도 GitHub과 같은 의존성으로 테스트할 수 있습니다. `bundle install`과 `bundle exec jekyll serve` 명령어로 실행하면 훨씬 더 정확한 사전 검증이 가능합니다.

지금 저는 블로그가 안정적으로 운영되고 있으며, 새로운 포스트를 작성할 때마다 로컬에서 완벽히 검증한 후 배포합니다. 20년 전에는 이런 깔끔한 배포 환경이 있었다면 얼마나 좋았을까 싶기도 합니다.

혹시 당신도 GitHub Pages 배포로 고민 중이라면, 지금 바로 본인의 _config.yml을 열어서 위의 세 가지 주범 항목을 확인해보시기 바랍니다. 아마 그 안에 당신의 답이 있을 것입니다.