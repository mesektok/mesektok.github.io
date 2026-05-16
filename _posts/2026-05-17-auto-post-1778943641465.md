---
title: "GitHub Pages에서 Jekyll 빌드 실패? 나는 이렇게 해결했다"
date: 2026-05-17 09:00:00 +0900
categories: [도구]
tags: [GitHub Pages, Jekyll, 빌드 오류, Chirpy, 트러블슈팅]
description: "30년 개발 경력의 은퇴 교수가 공개하는 Jekyll 블로그 빌드 문제 해결법"
---

## 은퇴 후 시작한 기술 블로그, 첫 번째 난관

작년 3월, 저는 35년간의 대학 교수 생활을 마감했습니다. 한 달간 쉬면서 문득 평생 경험한 프로그래밍 기술과 교육 철학을 정리하고 싶다는 생각이 들었습니다. 그렇게 시작한 것이 GitHub Pages 기반의 Jekyll 블로그였습니다.

처음엔 간단할 거라 생각했습니다. Chirpy 테마를 받아서 로컬에서 `jekyll serve`를 실행하면 될 줄 알았거든요. 하지만 현실은 달랐습니다. 저장소를 푸시한 후 GitHub Pages에서 빌드되기를 기다렸지만, 며칠이 지나도 블로그는 나타나지 않았습니다. 대신 깨진 테마와 에러 이메일만 받았습니다.

그때의 답답함과 좌절감은 이루 말할 수 없었습니다. 하지만 지난 30년간 무수히 많은 버그를 추적하고 해결해온 개발자의 근성이 다시 살아났습니다. 그리고 지금, 완벽하게 작동하는 블로그를 운영하면서 같은 문제로 고민하는 초보자들을 돕고 싶은 마음에 이 글을 작성합니다.

## Gemfile과 Ruby 버전 호환성 문제 - 내 첫 번째 실패

GitHub Pages에서 Jekyll이 제대로 빌드되지 않는 가장 흔한 원인은 바로 로컬 환경과 GitHub Pages 서버 환경의 불일치입니다. 제가 겪었던 것도 바로 이 문제였습니다.

저는 Mac에서 Ruby 3.2.0을 사용하고 있었는데, GitHub Pages는 당시 Ruby 3.1.4만 지원했습니다. 이 작은 차이가 Chirpy 테마의 특정 플러그인들과 호환성 문제를 일으켰습니다.

**해결책은 간단했습니다:**

1. GitHub Pages가 지원하는 Ruby 버전 확인하기 (당시 3.1.4)
2. Gemfile을 열어서 첫 줄에 `ruby '3.1.4'` 명시하기
3. `bundle update` 실행
4. 로컬에서 `bundle exec jekyll serve`로 재검증

이 과정을 거친 후, 신기하게도 GitHub Actions 워크플로우가 자동으로 문제를 감지했고, 빌드가 성공했습니다. 마치 의료 진단처럼, 정확한 환경 정의가 문제 해결의 첫 단계였던 것입니다.

## 깃허브 액션(GitHub Actions) 설정 실수 - 교수 경험담

Chirpy 테마를 사용할 때, GitHub Actions 워크플로우 파일이 매우 중요하다는 것을 릈늦게 깨달았습니다. 제가 초기에 했던 실수는 이 파일을 완전히 무시한 것입니다.

`.github/workflows` 디렉토리에 빌드 파일이 없거나 잘못되어 있으면, GitHub Pages는 기본 설정으로 빌드를 시도하게 되고, Chirpy 테마의 복잡한 구조를 제대로 처리하지 못합니다. 특히 마크다운 파일들이 제대로 변환되지 않고, CSS와 JavaScript 파일들도 로드되지 않는 현상이 발생합니다.

**올바른 워크플로우 파일 설정:**

```yaml
name: Build and Deploy

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    - name: Setup Ruby
      uses: ruby/setup-ruby@v1
      with:
        ruby-version: 3.1.4
        bundler-cache: true
    - name: Build
      run: bundle exec jekyll build
    - name: Deploy
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./_site
```

이 파일을 제대로 설정한 후, 저는 푸시할 때마다 자동으로 빌드되고 배포되는 기적 같은 경험을 할 수 있었습니다. 마치 대학원생 시절, 자동화된 테스트 슈트를 처음 만들었을 때의 그 희열이 다시 살아났습니다.

## 캐시 문제와 강제 새로고침 - 세 번째 관문

모든 설정이 완벽하게 되었다고 생각했을 때, 또 다른 문제가 발생했습니다. 글을 수정해서 푸시했는데, 웹사이트에는 여전히 이전 내용이 표시되는 것이었습니다.

이것은 브라우저 캐시 문제였습니다. 제가 블로그를 방문할 때 사용하던 크롬이 CSS와 JavaScript 파일을 캐시에 저장해두었던 것입니다. 또한 GitHub Pages 자체의 CDN 캐시도 영향을 미쳤습니다.

**제가 배운 해결 방법들:**

- Ctrl+Shift+Delete (Windows) 또는 Command+Shift+Delete (Mac)로 캐시 비우기
- 시크릿 탭에서 테스트 페이지 방문
- Jekyll의 `_config.yml`에서 에셋 파일명을 타임스탬프로 자동 변경 설정
- GitHub Pages의 설정에서 "Enforce HTTPS" 재확인

특히 `_config.yml`에 다음을 추가하는 것이 도움이 되었습니다:
```yaml
sass:
  style: compressed
```

이렇게 하면 CSS가 압축되면서 자동으로 새로운 버전으로 인식됩니다.

## 로컬 테스트의 중요성 - 교수의 조언

지난 30년간 수천 명의 학생들을 가르치면서 배운 가장 중요한 교훈은 "작은 것부터 확인하라"는 것이었습니다. 이것은 블로깅에도 동일하게 적용됩니다.

GitHub에 푸시하기 전에 반드시 로컬에서 완벽하게 테스트되어야 합니다. `bundle exec jekyll serve`로 `http://localhost:4000`에서 정확히 어떻게 보이는지 확인한 후, 그 다음에 푸시하는 것입니다. 이 단계를 건너뛰면, 빌드 실패 알림을 받고 다시 수정하고 푸시하고... 이런 반복을 하게 됩니다.

저는 이제 매번 다음의 체크리스트를 따릅니다:
1. 글 작성
2. `bundle exec jekyll serve` 실행
3. 로컬에서 모든 링크와 이미지 확인
4. 마크다운 문법 검증
5. Git 커밋 및 푸시

## 정리하며

은퇴 후 새로운 취미로 시작한 기술 블로그가 처음엔 얼마나 답답했는지, 그리고 그 문제들을 어떻게 해결했는지 공유했습니다. GitHub Pages + Jekyll + Chirpy는 정말 훌륭한 조합입니다. 하지만 이들이 완벽하게 작동하려면 로컬 환경 설정, GitHub Actions 워크플로우, 그리고 체계적인 테스트가 필수입니다.

혹시 여러분도 비슷한 문제로 고민하고 계신가요? 아니면 다른 해결 방법을 찾으셨나요? 댓글로 여러분의 경험과 팁을 공유해 주시면, 이 커뮤니티가 더욱 풍요로워질 거라고 생각합니다. 함께 배우고 성장하는 개발자 커뮤니티를 만들어 나갈 수 있도록 여러분의 피드백을 기대합니다!