---
title: "GitHub Pages에서 Jekyll 블로그가 자꾸 빌드 실패한다면? 은퇴 교수의 3년 삽질 경험담"
date: 2026-05-17 09:00:00 +0900
categories: [도구]
tags: [GitHub Pages, Jekyll, 블로그 운영, Chirpy, 트러블슈팅]
description: "GitHub Pages Jekyll 블로그 빌드 실패 원인과 해결책을 은퇴 교수의 실제 경험으로 풀어냅니다"
---

## 은퇴 후 블로깅을 시작하게 된 우연한 계기

저는 30년을 대학에서 전자공학을 가르쳤습니다. 정년퇴직 후 시간이 남아돌았고, 젊은 시절부터 정리하지 못했던 연구 노트와 강의 자료들을 정리하고 싶은 마음이 들었습니다. 손녀딸이 "할아버지, 지금은 모두 GitHub에 올려요"라고 말해줬고, 그렇게 저는 62세의 나이에 GitHub Pages와 Jekyll이라는 생소한 기술을 만나게 되었습니다.

처음 며칠은 설레었습니다. 로컬 환경에서 `jekyll serve` 명령을 입력하면 http://localhost:4000에서 블로그가 떠올랐을 때의 그 희열! 하지만 문제는 GitHub Pages에 push한 후부터 시작되었습니다. 로컬에서는 멀쩡하게 작동하던 블로그가 GitHub Pages에서는 빌드 실패 이메일만 받아대는 것입니다. 첫 번째 문제 해결까지만 해도 2주가 걸렸습니다.

## GitHub Pages의 숨겨진 진실: Jekyll 버전 불일치

처음 삽질의 원인은 매우 단순했지만, 찾기는 정말 어려웠습니다. 제 로컬 환경의 Jekyll 버전과 GitHub Pages에서 지원하는 Jekyll 버전이 달랐던 것입니다. 

제 컴퓨터에는 Jekyll 4.3.2가 설치되어 있었지만, GitHub Pages는 당시 Jekyll 3.9.0만 지원했습니다. 로컬에서만 작동하는 플러그인을 사용했다는 뜻이었습니다. 당시 저는 `Gemfile`이 무엇인지도 몰랐으니까요. 

Gemfile의 중요성을 깨닫게 된 것은 손자가 물어봤을 때입니다. "할아버지, `bundle exec jekyll serve`는 해봤어?" 그 한 마디가 제 블로그 인생을 바꿨습니다. 번들을 사용하면 Gemfile에 명시된 버전으로 정확하게 환경을 맞출 수 있다는 걸 알게 된 것입니다.

```
# Gemfile 예시
source "https://rubygems.org"
gem "jekyll", "~> 3.9.0"
gem "jekyll-feed", "~> 0.12"
```

이제 저는 항상 `bundle exec jekyll serve`로 로컬에서 테스트합니다. 이렇게 하면 실제 GitHub Pages 환경과 동일한 조건에서 블로그를 미리 확인할 수 있습니다.

## Chirpy 테마의 함정: _config.yml의 비밀

2년차에 들어서면서 저는 기본 Jekyll 테마에서 Chirpy 테마로 옮겨가고 싶었습니다. 아름답고 깔끔한 디자인에 반했거든요. 하지만 Chirpy 테마의 설정은 기본 Jekyll과는 차원이 달랐습니다.

`_config.yml` 파일 하나가 블로그의 운명을 결정합니다. 이 파일에서 기본 설정이 틀리면 CSS가 제대로 로드되지 않고, 카테고리 페이지가 안 보이고, 심지어 글자가 보이지 않을 수도 있습니다.

특히 주의해야 할 설정들:

**1. baseurl 설정의 중요성**
저는 처음에 baseurl을 비워뒀다가 모든 게 깨졌습니다. 리포지토리 이름이 `username.github.io`가 아니라면 baseurl에 리포지토리 이름을 명시해야 합니다.

```yaml
baseurl: "/my-blog"  # 예: https://github.com/username/my-blog
url: "https://username.github.io"
```

**2. timezone 설정**
한국에 사는데 UTC 기본값으로 놓고 있어서 포스트 시간이 자꾸 이상했습니다.

```yaml
timezone: Asia/Seoul
```

**3. 플러그인 활성화**
Chirpy는 특정 플러그인들이 필수인데, `_config.yml`에서 명시되지 않으면 작동하지 않습니다.

```yaml
plugins:
  - jekyll-paginate
  - jekyll-redirect-from
  - jekyll-seo-tag
  - jekyll-archives
```

## GitHub Actions: 나의 구원자

3년차가 되면서 저는 GitHub Actions를 알게 되었고, 이것이 제 블로깅 인생을 정말로 바꿔놓았습니다.

GitHub Pages의 문제 중 하나는 빌드가 실패해도 에러 메시지가 충분하지 않다는 것입니다. 이메일로는 "빌드 실패"라고만 나오니까요. 하지만 GitHub Actions를 사용하면 정확히 어디서 무엇이 잘못됐는지 볼 수 있습니다.

`.github/workflows/pages.yml` 파일을 만들면 push할 때마다 자동으로 빌드를 시도하고, 실패 원인을 상세히 표시해줍니다. 이것 덕분에 저는 "어? 이 파일 이름에 한글이 있어서 안 되는 건가?" 같은 구체적인 원인들을 찾을 수 있었습니다.

실제로 GitHub Actions 로그를 통해 발견한 문제들:
- 포스트 파일명의 날짜 형식이 YYYY-MM-DD 형식이 아닐 때
- front matter의 YAML 문법이 잘못되었을 때
- 이미지 경로가 절대경로로 되어 있을 때

## 마치며: 은퇴자도 배울 수 있다

제 경험을 정리하다 보니 깨닫는 게 있습니다. 기술은 나이와 무관하게 배울 수 있다는 것입니다. 다만 인내심이 필요할 뿐입니다. 제가 겪은 3년의 삽질이 누군가에게는 3시간이 될 수 있다면, 이 글이 얼마나 소중한 것인지 알 것 같습니다.

혹시 당신도 GitHub Pages에서 Jekyll 블로그를 운영하다가 막혔던 경험이 있다면, 댓글로 당신의 경험담을 공유해보시겠어요? 당신의 이야기가 다른 블로거의 3년을 3일로 줄여줄 수 있으니까요.