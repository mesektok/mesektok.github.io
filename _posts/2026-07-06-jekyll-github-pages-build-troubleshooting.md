---
title: "GitHub Pages에서 Jekyll 빌드 실패? 내가 15년 개발자 생활에서 배운 해결책"
slug: jekyll-github-pages-build-troubleshooting
date: 2026-07-06 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages, 트러블슈팅]
description: "Jekyll 블로그를 GitHub Pages에 배포할 때 겪는 빌드 오류를 체계적으로 진단하고 해결하는 실전 가이드"
---

대학교를 정년퇴직한 지 3년이 지났습니다. 컴퓨터공학과에서 30년을 가르쳤던 경험이 아깝다 싶어, 퇴직 후 기술 블로그를 시작하기로 결심했습니다. 그렇게 처음 도전한 게 바로 Jekyll과 GitHub Pages였습니다. 젊은 시절엔 자체 서버에 블로그를 구축했지만, 요즘엔 무료 호스팅을 활용하는 게 똑똑한 방법이라는 걸 깨달았거든요.

그런데 예상과 달리 처음 배포는 순탄하지 않았습니다. GitHub Actions에서 빌드가 계속 실패했고, 마크다운 파일은 제대로 렌더링되지 않았습니다. 며칠간의 삽질 끝에 규칙을 발견했고, 지금은 자신 있게 블로그를 유지보수하고 있습니다. 오늘 그 경험담을 여러분과 나누고 싶습니다.

## 로컬 환경과 GitHub Pages 환경의 차이를 이해하라

가장 큰 실수는 "로컬에서 잘 되면 GitHub에서도 잘 될 거야"라는 착각이었습니다. `bundle exec jekyll serve`로 로컬 테스트를 하면 블로그가 완벽하게 보입니다. 하지만 GitHub Pages는 다른 규칙으로 작동합니다.

GitHub Pages는 기본적으로 **안전 모드(safe mode)**에서 Jekyll을 실행합니다. 이는 특정 플러그인과 설정이 작동하지 않음을 의미합니다. 예를 들어, `_config.yml`에서 임의의 플러그인을 마음대로 사용할 수 없습니다. 공식적으로 지원되는 플러그인만 작동합니다.

제 경험에서는 로컬에서 설치한 Ruby 버전과 GitHub Actions에서 사용하는 Ruby 버전이 달라서 문제가 발생했습니다. 로컬에선 Ruby 3.2를 사용했지만, GitHub는 그 당시 3.1을 기본값으로 했던 것이죠. 이를 해결하기 위해 `.github/workflows/jekyll.yml` 파일에 명시적으로 Ruby 버전을 지정했습니다.

```yaml
- name: Set up Ruby
  uses: ruby/setup-ruby@v1
  with:
    ruby-version: '3.2'
    bundler-cache: true
```

이 한 줄이 제 빌드 실패를 90% 해결했습니다.

## Gemfile과 _config.yml의 일관성을 맞춰야 한다

은퇴 후 처음 몇 주는 문서를 제대로 읽지 않고 무작정 따라 했던 습관이 문제였습니다. 인터넷의 여러 튜토리얼에서 다양한 플러그인을 권장했고, 저는 그걸 모두 설치하려 했습니다.

그런데 문제는 이겁니다. `Gemfile`에는 플러그인이 있는데 `_config.yml`에 등록하지 않았거나, 반대의 경우가 발생했던 것입니다. GitHub Pages가 빌드할 때는 두 파일이 정확하게 동기화되어야 합니다.

제 경험에서 가장 흔했던 실수는 SEO 관련 플러그인이었습니다. `jekyll-seo-tag`를 설치했지만 `_config.yml`의 `plugins:` 섹션에 추가하지 않았던 것입니다. 결과적으로 메타 태그가 생성되지 않아 검색 엔진 최적화가 전혀 되지 않았습니다.

해결책은 간단합니다. 매번 플러그인을 추가할 때마다 체크리스트를 만드세요:

1. `Gemfile`에 gem을 추가했는가?
2. `bundle install`을 실행했는가?
3. `_config.yml`의 `plugins:` 섹션에 추가했는가?
4. `_config.yml` 파일의 문법이 올바른가? (들여쓰기는 탭이 아닌 스페이스여야 합니다)

저는 이 네 가지를 체크하는 습관을 들인 후로 빌드 오류가 거의 사라졌습니다.

## GitHub Actions 로그를 꼼꼼히 읽는 습관을 키우라

가장 마지막이지만 가장 중요한 팁입니다. GitHub 저장소의 'Actions' 탭에 들어가면 빌드 로그를 볼 수 있습니다. 처음엔 이 로그가 무슨 말인지 몰랐습니다. 50줄이 넘는 텍스트 속에서 오류 메시지를 찾는 것도 어려웠습니다.

하지만 퇴직 후 느긋한 시간이 많아져서 차근차근 읽어보니, 오류 메시지는 매우 명확했습니다. "Liquid error: undefined variable", "Invalid YAML", "Plugin not found" 같은 메시지들이 정확하게 무엇이 문제인지 알려주고 있었던 것입니다.

제가 찾은 팁은 항상 로그의 **마지막 부분**을 먼저 읽는 것입니다. "Build failed"라는 메시지 바로 위의 몇 줄이 문제의 핵심입니다. 거기서 파일명과 줄 번호까지 정확하게 지시합니다.

예를 들어, 제가 과거에 본 오류 메시지는 이렇습니다: "jekyll 4.2.0 | Error: undefined variable ... in _layouts/post.html line 23". 이 한 줄이 제게 `_layouts/post.html` 파일의 23번 줄에 문제가 있다는 걸 명확하게 알려줬습니다.

30년을 교육 현장에서 보낸 입장에서 보면, GitHub Actions의 오류 메시지 시스템은 정말 잘 설계되었습니다. 학생들에게 피드백을 줄 때도 이렇게 명확하고 구체적이어야 한다고 생각합니다.

이제 저의 Jekyll 블로그는 안정적으로 작동합니다. 새 글을 작성해서 GitHub에 push하면 몇 초 후 자동으로 배포됩니다. 이것이 바로 GitHub Pages와 Jekyll의 매력입니다. 혹시 여러분도 같은 문제로 고민하고 있다면, 이 글의 방법들을 하나씩 시도해보세요. 여러분의 블로그도 분명히 성공할 것입니다.