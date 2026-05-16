---
title: "GitHub Pages에서 Jekyll 블로그가 갑자기 빌드 실패한 날, 내가 배운 것들"
date: 2026-05-17 09:00:00 +0900
categories: [도구]
tags: [GitHub Pages, Jekyll, 블로그 운영, 빌드 오류]
description: "은퇴 후 기술 블로그를 시작한 교수가 경험한 Jekyll 빌드 실패 사건과 해결 과정"
---

## 평온했던 블로그 생활의 끝

지난해 은퇴를 맞이한 나는 늘 미루어두었던 기술 블로그를 시작하기로 결심했다. 40년간 컴퓨터 과학을 가르친 경력을 정리하고, 후배 개발자들과 경험을 공유하고 싶었기 때문이다. GitHub Pages와 Jekyll의 조합은 정말 훌륭했다. Markdown으로 글을 쓰고, Git으로 커밋하면 자동으로 블로그가 업데이트되는 마법 같은 경험이었다.

처음 3개월은 순조로웠다. Chirpy 테마를 선택하고, 로컬 환경에서 `jekyll serve`를 실행하면 완벽하게 빌드되었다. 나는 매주 토요일 오전마다 새로운 포스트를 작성하는 루틴을 만들었고, 독자들의 피드백도 조금씩 늘어나고 있었다. 그러던 어느 날 아침, 푸시한 지 5분 만에 GitHub에서 이메일이 날아왔다. "Your site failed to build."

그 순간의 당황함은 이루 말할 수 없었다. 40년간 강의실에서 우아하게 설명해온 코드들이 갑자기 작동하지 않는 경험은 처음이었다. 하지만 이 사건이 나에게 준 교훈은 매우 소중했다.

## 로컬 빌드와 GitHub Pages 빌드의 차이를 이해하다

먼저 내가 깨달은 첫 번째 진실은 이것이었다: **로컬에서 완벽하게 빌드된다고 해서 GitHub Pages에서도 빌드된다는 보장은 없다**는 것이다.

GitHub Pages는 특정 버전의 Jekyll과 플러그인만을 지원한다. 내가 로컬에서 사용 중이던 Jekyll 4.3.2와 여러 개의 커스텀 플러그인들이 문제였다. GitHub Pages가 공식적으로 지원하는 플러그인은 약 20개 정도에 불과하다는 사실을 그때야 알게 되었다.

나는 즉시 GitHub의 공식 문서를 펼쳤다. 그곳에는 `Gemfile`에서 `github-pages` gem을 사용하라는 내용이 있었다. 이 gem은 GitHub Pages가 지원하는 정확한 버전의 Jekyll과 플러그인들을 자동으로 설치해준다.

```ruby
# Before
gem "jekyll", "~> 4.3"
gem "jekyll-last-modified-at"

# After
gem "github-pages", "~> 232", group: :jekyll_plugins
```

이 한 줄의 변경이 나의 빌드 문제를 완벽하게 해결했다. 마치 미로에서 나가는 문을 찾은 것 같은 기쁨이었다. 하지만 문제는 여기서 끝나지 않았다.

## GitHub Actions로 빌드 프로세스를 투명하게 만들다

로컬 빌드는 성공했지만, 여전히 푸시 후 GitHub Pages 빌드는 실패하곤 했다. 가장 답답한 부분은 정확히 어디서 실패하는지 알 수 없다는 것이었다. GitHub에서 제공하는 에러 메시지는 너무 일반적이었다.

나는 GitHub Actions를 도입하기로 결정했다. 이것은 내 은퇴 인생의 터닝포인트였다. GitHub Actions는 매우 강력한 CI/CD 도구였지만, 설정이 복잡할 거라 생각했다. 그러나 Jekyll을 위한 스타터 워크플로우가 이미 준비되어 있었다.

`.github/workflows/jekyll.yml` 파일을 생성하고 다음과 같은 구성을 추가했다:

```yaml
name: Build and deploy Jekyll site

on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build with Jekyll
        uses: actions/jekyll-build-pages@v1
        with:
          source: ./
          destination: ./_site
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
```

이 설정을 추가한 후, 각 푸시마다 상세한 빌드 로그를 볼 수 있게 되었다. 이제 문제가 발생하면 GitHub의 Actions 탭에서 정확히 어느 파일의 어느 줄이 문제인지 확인할 수 있었다.

지난 6개월간 나는 이 로그들을 통해 수십 개의 잠재적 문제를 발견하고 해결했다. YAML 형식의 오류, 이미지 경로의 오타, 카테고리 명명의 일관성 부족 등등. 각각의 오류는 내가 더 나은 글쓰기 습관을 갖게 도와주었다.

## 실전 경험: 특수 문자와 인코딩의 함정

문제 해결 과정 중 가장 흥미로웠던 것은 특수 문자와 관련된 버그였다. 한국어로 블로그를 운영하면서 제목에 특수 문자를 포함시켰을 때, 로컬에서는 완벽하게 작동했지만 GitHub Pages에서는 URL이 제대로 생성되지 않았다.

이것은 단순한 인코딩 문제가 아니었다. Jekyll의 `permalink` 설정과 Chirpy 테마의 기본 설정이 충돌하고 있었던 것이다. 나는 `_config.yml`에서 다음과 같이 수정했다:

```yaml
permalink: /posts/:title/
```

그리고 포스트의 Front Matter에서 특수 문자를 명시적으로 처리하기 시작했다:

```yaml
---
title: "GitHub Pages에서 Jekyll 블로그가 실패한 날"
slug: "github-pages-jekyll-failure"
---
```

이 경험은 나에게 중요한 교훈을 주었다: **시스템이 설계되고 문서화되는 과정에서 발생하는 마찰은 매우 소중한 학습 기회**라는 것이다. 40년의 강의 경험 중에도 이렇게 구체적인 문제 해결 경험은 드물었다.

## 버전 관리와 문서화의 중요성

마지막으로 깨달은 것은 버전 관리의 중요성이었다. 나는 `Gemfile.lock`을 무시했다. 이 파일이 무엇인지 이해하지 못했기 때문이다. `Gemfile`만으로 충분하다고 생각했다.

하지만 `Gemfile.lock`은 현재 작동하는 정확한 버전들을 기록해둔 소중한 문서다. 이것을 버전 관리에 포함시키면, 팀원들이나 다른 머신에서도 정확히 같은 환경을 재구성할 수 있다.

```bash
gem install bundler
bundle install  # Gemfile.lock에 기반하여 정확한 버전 설치
bundle exec jekyll serve
```

은퇴 후 혼자 블로그를 운영하는 입장이지만, 이 습관은 내 코드의 품질을 크게 향상시켰다. 6개월 후 로컬 환경을 재설정해야 할 때도 `Gemfile.lock`만 있으면 정확히 같은 상태로 복구할 수 있었기 때문이다.

## 마지막 조언: 로컬 테스트를 철저히 하자

40년간의 경험으로부터 나온 마지막 조언은 이것이다. GitHub Pages에 푸시하기 전에, 반드시 로컬에서 최종 빌드를 검증하라는 것이다.

```bash
bundle exec jekyll build
# 또는
bundle exec jekyll serve
```

이 명령어를 통해 `_site` 디렉토리가 생성되고, 모든 HTML 파일이 정상적으로 생성되는지 확인하라. 특히 포스트의 제목, 카테고리, 태그에 오타가 없는지 꼼꼼히 살펴보라.

나는 이제 매주 토요일 오전 9시에 새 포스트를 작성하고, 월요일 아침에 최종 검증을 거친 후에야 푸시한다. 이 루틴을 통해 지난 6개월간 빌드 실패는 한 번도 없었다.

당신의 기술 블로그도 GitHub Pages와 Jekyll로 시작하고 싶다면, 또는 현재 빌드 문제로 고민 중이라면, 오늘 바로 `Gemfile`을 점검하고 GitHub Actions를 설정해보기를 강력히 권장한다. 그리고 댓글 섹션에서 당신의 경험담과 추가 팁을 공유해주길 바란다. 은퇴한 교수도 배우고 싶다.