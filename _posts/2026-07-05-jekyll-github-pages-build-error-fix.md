---
title: "GitHub Pages 배포 실패? Jekyll 빌드 에러 10분 안에 해결하는 법"
slug: jekyll-github-pages-build-error-fix
date: 2026-07-05 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그를 GitHub Pages에 배포할 때 마주치는 흔한 빌드 에러들을 진단하고 빠르게 해결하는 실전 가이드"
---

## 나는 어떻게 Jekyll 에러의 늪에서 빠져나왔는가

제 이름은 박재호입니다. 대학에서 30년간 컴퓨터공학을 가르쳤고, 은퇴 후 기술 블로그를 시작했습니다. 처음 GitHub Pages에 Jekyll 블로그를 올렸을 때, 저는 예상 밖의 에러 메시지 폭주에 직면했습니다. "Build failed"라는 메시지만 반복되고, GitHub에서 제공하는 상세 로그는 마치 암호문처럼 느껴졌습니다.

그 날 밤, 저는 커피를 마시며 문제를 체계적으로 풀어나가기로 결심했습니다. 30년의 교육 경험이 여기서 빛을 발했습니다. 복잡한 문제를 작은 단위로 나누어 접근하면, 대부분의 기술적 장애물은 극복 가능하다는 것을 알고 있었거든요. 지금부터 제가 체험한 해결책들을 여러분과 나누겠습니다.

## GitHub Actions 로그를 읽는 올바른 방법

Jekyll 빌드 실패의 첫 번째 단계는 정확한 진단입니다. 많은 초보자들이 놓치는 부분이 바로 GitHub Actions 로그의 상세 정보입니다.

저장소의 'Actions' 탭으로 이동하면, 최근 배포 시도 기록이 나타납니다. 실패한 워크플로우를 클릭하고 'build' 작업을 펼치면, 정확히 어느 줄에서 무엇이 잘못되었는지 볼 수 있습니다. 예를 들어 "Liquid Exception: Unknown tag 'figure'"라는 메시지가 나타났다면, 이는 Jekyll이 인식하지 못하는 Liquid 태그를 사용했다는 의미입니다.

제가 경험한 가장 흔한 에러는 gem 의존성 문제였습니다. Gemfile에 명시된 플러그인과 _config.yml의 plugins 섹션이 일치하지 않으면 빌드가 실패합니다. Chirpy 테마를 사용할 때는 특히 jekyll-paginate-v2, jekyll-archives 같은 플러그인이 필수입니다. 로컬에서는 완벽히 작동하지만 GitHub 서버에서는 다른 환경이므로, Gemfile.lock을 정확히 커밋하는 것이 중요합니다.

## _config.yml의 숨겨진 함정들

많은 블로거들이 건너뛰는 부분이 설정 파일의 정확성입니다. YAML 형식은 들여쓰기에 매우 민감합니다. 탭 문자와 스페이스를 혼합하면 파서가 읽을 수 없게 됩니다.

제 경험상 가장 자주 발생하는 실수는 다음과 같습니다. 첫째, baseurl 설정입니다. 저장소 이름이 'myblog'라면 baseurl을 '/myblog'로 설정해야 하는데, 앞의 슬래시를 빼먹으면 모든 자산(CSS, JavaScript, 이미지) 로드가 실패합니다. 둘째, 한글 문자 인코딩입니다. _config.yml 파일의 인코딩이 UTF-8이 아니면 한글 문자열이 깨집니다. 마지막으로 permalink 형식입니다. 잘못된 permalink 패턴은 특정 포스트만 빌드되지 않게 만듭니다.

저는 이 문제들을 해결하기 위해 온라인 YAML 검증 도구를 사용하기 시작했습니다. 손쉽게 구문 오류를 찾을 수 있으며, 로컬에서 'bundle exec jekyll serve' 명령어로 미리 테스트하는 것도 좋은 방법입니다.

## 로컬 환경과 GitHub 환경의 차이 이해하기

은퇴 후 처음 몇 주는 저도 "로컬에서는 되는데 GitHub에서 왜 안 될까?"라는 의문으로 밤을 지새웠습니다. 문제는 두 환경이 다르다는 것입니다.

로컬 환경에서 Jekyll은 JEKYLL_ENV=development로 실행되지만, GitHub Pages는 production 환경에서 실행됩니다. 이것이 의미하는 바는 if jekyll.environment == 'production' 같은 조건부 코드가 다르게 작동한다는 뜻입니다. 또한 Ruby 버전도 다를 수 있습니다. .ruby-version 파일을 만들어 명시적으로 Ruby 버전을 지정하면 도움이 됩니다.

더 중요한 것은 플러그인 호환성입니다. 로컬에서는 모든 플러그인이 작동하지만, GitHub Pages는 특정 플러그인만 공식 지원합니다. Chirpy 같은 인기 있는 테마는 이를 고려해 설계되어 있지만, 맞춤 플러그인을 추가했다면 GitHub Actions 워크플로우를 수정해야 할 수도 있습니다.

저는 GitHub에서 제공하는 공식 문서를 읽고, 'jekyll-theme-chirpy' 저장소의 .github/workflows/pages-deploy.yml 파일을 참고했습니다. 이 파일은 정확히 어떻게 빌드 프로세스가 실행되는지 보여줍니다. 이를 이해하면 대부분의 문제를 예방할 수 있습니다.

## 실전 체크리스트: 5분 안에 문제 해결하기

제가 30년의 교육 경험으로 개발한 순차적 접근 방법을 공유합니다.

1단계: GitHub Actions 로그를 자세히 읽고 정확한 에러 메시지를 확인합니다. "bundle install" 단계에서 실패하는지, 아니면 "jekyll build" 단계에서 실패하는지 파악합니다.

2단계: Gemfile과 _config.yml의 gem 이름과 플러그인 설정이 일치하는지 확인합니다. 특히 jekyll-paginate와 jekyll-paginate-v2는 동시에 사용할 수 없습니다.

3단계: 온라인 YAML 검증 도구로 _config.yml을 확인하고, 로컬에서 'bundle exec jekyll build'를 실행해 같은 에러가 발생하는지 확인합니다.

4단계: Ruby 버전, Bundler 버전, 그리고 각 gem의 버전을 명시적으로 지정합니다.

5단계: 위 모든 단계를 거쳤는데도 실패하면, 저장소 설정에서 GitHub Pages 소스를 "GitHub Actions"로 설정했는지 확인합니다.

이 체크리스트를 따르면, 제가 겪었던 대부분의 문제를 빠르게 진단하고 해결할 수 있습니다. 저도 이 방법으로 30분 이상의 빌드 실패를 단 5분 만에 해결한 경험이 있습니다.

지금 당신의 Jekyll 블로그도 이 가이드를 따라 한번 체크해보시고, 만약 여전히 문제가 있다면 GitHub 저장소의 Issues 탭에 정확한 에러 로그와 함께 질문해 보세요. 많은 경험 많은 개발자들이 기꺼이 도와줄 것입니다.