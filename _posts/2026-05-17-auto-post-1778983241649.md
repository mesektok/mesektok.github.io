---
title: "GitHub Pages에서 Jekyll 블로그가 자꾸 빌드 실패하는 이유, 30년 개발자도 헤맸습니다"
date: 2026-05-17 09:00:00 +0900
categories: [도구]
tags: [GitHub Pages, Jekyll, 블로그 운영, 빌드 오류, 정적 사이트 생성기]
description: "Jekyll 블로그의 빌드 실패 원인을 파악하고 해결하는 실전 팁"
---

# GitHub Pages에서 Jekyll 블로그가 자꾸 빌드 실패하는 이유, 30년 개발자도 헤맸습니다

대학에서 30년을 컴퓨터 과학을 가르치다가 작년에 은퇴한 나는, 여유 있는 시간을 활용해 기술 블로그를 시작하려고 결심했습니다. GitHub Pages와 Jekyll이라는 조합이 완벽해 보였거든요. 무료이고, 버전 관리도 되고, 마크다운으로 글을 쓰면 된다니까요. 하지만 현실은 다르더군요.

처음 일주일은 정말 고통스러웠습니다. 로컬에서 완벽하게 빌드되던 블로그가 GitHub에 푸시하면 자꾸만 빌드 실패 이메일이 날아오는 것입니다. "Build failed"라는 메시지 앞에서 나는 한 명의 초보자가 되어있었습니다. 그 과정에서 배운 교훈들을 여러분과 나누고 싶습니다.

## 루비 버전 호환성, 가장 흔한 함정

처음 맞닥뜨린 문제는 루비(Ruby) 버전 문제였습니다. 제 노트북에는 루비 3.2가 설치되어 있었고, 로컬에서 `jekyll serve` 명령어로 완벽하게 사이트가 구동되었습니다. 그런데 GitHub Pages의 서버 환경은 다랐습니다.

GitHub Pages는 기본적으로 루비 3.3과 특정 버전의 Jekyll 및 플러그인들을 지원합니다. 로컬 환경과 원격 환경이 다르면 문제가 생기는 것이지요. 저는 이를 해결하기 위해 `.ruby-version` 파일을 생성했습니다.

```
3.3.0
```

이 파일을 저장소의 루트에 놓으면, rbenv를 사용하는 개발자들이 자동으로 같은 버전을 사용하게 됩니다. 더 중요한 것은 GitHub Actions 워크플로우에서도 이 버전을 명시해야 한다는 점입니다. `.github/workflows/jekyll.yml` 파일에서 Ruby 버전을 정확히 지정하면, 서버 환경과 로컬 환경의 불일치를 해소할 수 있습니다.

```yaml
- uses: ruby/setup-ruby@v1
  with:
    ruby-version: 3.3.0
    bundler-cache: true
```

## Gemfile과 Gemfile.lock, 버전 관리의 생명줄

제 경험상 가장 흔한 실패 원인은 Gemfile 관리였습니다. 처음에는 Gemfile에 `gem "jekyll"` 이렇게만 썼습니다. 그러면 어느 버전의 Jekyll을 설치할지 명확하지 않습니다. 로컬에서는 최신 버전이 설치되고, GitHub에서는 또 다른 버전이 설치되는 불운한 상황이 벌어집니다.

저는 이 문제를 해결하기 위해 모든 gem의 버전을 명시하기로 했습니다.

```
gem "jekyll", "~> 4.3.2"
gem "jekyll-feed", "~> 0.17.0"
gem "jekyll-seo-tag", "~> 2.8.0"
gem "github-pages", "~> 227"
```

더 중요한 것은 `Gemfile.lock` 파일을 반드시 저장소에 커밋해야 한다는 점입니다. 이 파일은 npm의 `package-lock.json`처럼, 설치된 gem들의 정확한 버전을 기록합니다. 로컬에서 `bundle install`로 생성된 이 파일을 함께 푸시하면, GitHub에서도 정확히 같은 버전의 라이브러리들을 설치하게 됩니다.

```bash
bundle lock --add-platform ruby
```

이 명령어로 플랫폼 의존성까지 해결할 수 있다는 점도 배웠습니다.

## _config.yml의 숨은 설정들, 디버깅이 쉬워집니다

GitHub Pages 빌드 실패의 30%는 사실 `_config.yml` 파일의 설정 오류입니다. 저도 처음에는 이걸 몰랐습니다. YAML 형식의 들여쓰기 실수, 따옴표 누락, 그리고 github-pages gem과 호환되지 않는 플러그인들이 원인이 되곤 합니다.

제가 발견한 중요한 설정은 다음과 같습니다.

```yaml
# GitHub Pages 호환성
github: [metadata]
remote_theme: jekyll/minimal

# 안전한 플러그인만 사용
plugins:
  - jekyll-feed
  - jekyll-seo-tag
  - jekyll-archives
  - jekyll-sitemap

# 빌드 환경 명시
markdown: kramdown
highlighter: rouge

# 로컬 빌드와 동일한 환경
exclude:
  - .git
  - .gitignore
  - vendor
  - .bundle
  - Gemfile
  - Gemfile.lock
```

특히 `safe: true` 설정이 중요합니다. 이 설정을 하면 GitHub에서 지원하지 않는 플러그인들을 무시하고 진행합니다. 처음에는 제 커스텀 플러그인 때문에 계속 실패했었습니다.

## 로컬 빌드 환경 재현, 예방이 최고의 약

이제 저는 로컬에서 GitHub와 동일한 환경을 만들어 사용합니다.

```bash
bundle exec jekyll serve --incremental --draft
```

`bundle exec`를 사용하면 Gemfile에 명시된 버전의 gem들을 사용합니다. `--incremental` 옵션은 변경된 파일만 다시 빌드해 속도를 높이고, `--draft` 옵션은 작성 중인 글도 미리보기로 볼 수 있습니다.

더 나아가, 저는 GitHub Actions의 로그를 자주 확인합니다. 빌드 실패 시 "Actions" 탭에서 정확한 오류 메시지를 볼 수 있거든요. 이 로그야말로 가장 솔직한 피드백입니다.

```
Error: redcarpet markdown processor is not installed
Did you mean 'kramdown'?
```

이런 식의 명확한 오류 메시지를 통해 문제를 빠르게 진단할 수 있습니다.

## 경험담: 결국 무엇이 도움이 되었나

30년을 가르쳤던 제 경험에서 배운 것은 이것입니다. 기술은 버전이 있고, 환경이 있으며, 문서가 있습니다. GitHub Pages와 Jekyll의 관계도 마찬가지입니다. 로컬 환경과 서버 환경을 동일하게 맞추는 것이 가장 중요합니다.

처음에는 정말 답답했지만, 한 번 설정이 제대로 되면 이후로는 순수하게 글 쓰는 즐거움만 누릴 수 있습니다. 지난 3개월간 저는 블로그에 45개 글을 올렸고, 단 한 번의 빌드 실패도 없습니다.

---

혹시 당신도 Jekyll 블로그 빌드 실패로 고민하고 있다면, 이 글의 해결책들을 차근차근 따라해보세요. 그리고 자신의 `_config.yml` 파일을 한 번 더 검토해보시기 바랍니다. 당신의 블로그가 성공적으로 빌드되는 그 순간의 기쁨이 정말 값지거든요. 댓글로 당신의 경험을 나눠주신다면 더욱 감사하겠습니다!