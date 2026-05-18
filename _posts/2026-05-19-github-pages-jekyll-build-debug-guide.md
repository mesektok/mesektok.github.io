---
title: "GitHub Pages에서 Jekyll 빌드 실패? 깃허브 액션 로그를 읽는 법"
slug: github-pages-jekyll-build-debug-guide
date: 2026-05-19 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "Jekyll 블로그를 GitHub Pages에 배포할 때 자주 겪는 빌드 실패 문제를 GitHub Actions 로그를 통해 진단하고 해결하는 실전 가이드"
---

## 처음 겪은 그 답답함을 기억합니다

15년 전 대학에서 웹프로그래밍을 가르칠 때는 FTP로 파일을 업로드하고, 직접 서버에 ssh로 접속해서 문제를 해결했습니다. 하지만 2024년에 은퇴 후 블로깅을 시작하면서 GitHub Pages를 처음 만났을 때, 나는 완전히 다른 세상에 들어갔다는 걸 느꼈습니다. 

몇 번의 git push 이후, 내 Jekyll 블로그는 갑자기 배포되지 않았습니다. 마크다운 파일은 분명 완벽했는데, 웹사이트는 여전히 이전 버전을 보여주고 있었습니다. GitHub 저장소의 Settings 탭을 헤매며 답답해했던 기억이 생생합니다. 그때 나는 **GitHub Actions의 로그**라는 것의 존재를 몰랐기 때문입니다.

## GitHub Actions 로그 찾는 법

GitHub Pages의 배포 과정은 이제 전부 GitHub Actions이 담당합니다. 당신의 저장소에 코드를 push하면, 자동으로 Jekyll 빌드가 트리거되고, 결과물이 gh-pages 브랜치에 배포됩니다. 하지만 뭔가 잘못되었을 때, 그것을 알려줄 사람이 필요합니다.

저장소의 **Actions** 탭을 클릭하세요. 여기가 모든 빌드 히스토리가 기록되는 곳입니다. 최근 push에 해당하는 워크플로우를 찾아 클릭하면, 빌드 로그를 확인할 수 있습니다. 나는 처음에 이 탭의 존재 자체를 모르다니, 정말 한심했습니다. 하지만 이제는 이곳이 내 문제 해결의 첫 번째 목표지입니다.

로그를 읽어보면 대부분의 경우 문제가 명확합니다. "Liquid syntax error", "Post date in the future", "Invalid YAML frontmatter" 같은 에러 메시지들이 정확히 어느 파일의 몇 번째 줄에서 문제가 발생했는지 알려줍니다. 예를 들어, 내 경우 `_posts/2026-05-19-example.md` 파일의 frontmatter에서 쌍따옴표 하나가 빠져 있었던 것이 로그를 통해 드러났습니다.

## 가장 흔한 세 가지 빌드 실패 원인

제 경험상, GitHub Pages Jekyll 빌드가 실패하는 원인은 거의 항상 같은 패턴입니다.

**첫째, 포스트 날짜 문제입니다.** YAML frontmatter의 date 필드가 미래 날짜로 설정되어 있으면, Jekyll은 기본적으로 그 포스트를 발행하지 않습니다. 초안으로 작성해두고 나중에 발행하려던 의도였다면, 시간을 조정하거나 `future: true` 옵션을 `_config.yml`에 추가해야 합니다.

**둘째, Liquid 템플릿 문법 오류입니다.** 마크다운 본문에 `{%` 또는 `{{` 문자를 그대로 사용하면, Jekyll은 이를 Liquid 템플릿 명령으로 해석하려다 실패합니다. 코드 블록 안에서라도 마찬가지입니다. 이 경우 역슬래시로 이스케이프하거나, 삼중 역따옴표 안의 언어 지정을 명확히 해야 합니다.

**셋째, YAML frontmatter의 구조 문제입니다.** 콜론 뒤에 공백이 없거나, 들여쓰기가 맞지 않으면 전체 파일 파싱이 실패합니다. 특히 description이나 tags 같은 필드에 특수문자가 포함되어 있으면, 반드시 쌍따옴표로 감싸야 합니다.

## 효율적인 디버깅 워크플로우

은퇴한 입장에서 시간은 많지만, 같은 실수는 반복하고 싶지 않았습니다. 그래서 저는 빌드 실패를 빠르게 진단하는 루틴을 만들었습니다.

먼저 GitHub의 Actions 탭에서 실패한 워크플로우의 "Build with Jekyll" 섹션을 펼칩니다. 에러 메시지의 파일 이름과 줄 번호를 확인한 후, 로컬 환경에서 해당 파일을 수정합니다. 그리고 다시 push 하기 전에, **로컬에서 `bundle exec jekyll serve` 명령으로 먼저 테스트합니다.** 이렇게 하면 GitHub에 올리기 전에 빌드 문제를 미리 발견할 수 있습니다.

GitHub Pages 설정에서 "Build and deployment" 섹션을 보면, "Source"가 "GitHub Actions"로 설정되어 있는지 확인하세요. 저는 처음에 "Deploy from a branch" 옵션으로 설정되어 있어서, 자동 배포가 작동하지 않는 줄 알고 애를 먹었습니다.

매번 빌드 로그를 읽다 보니, Jekyll의 동작 방식과 GitHub의 자동화 시스템에 대한 이해가 깊어졌습니다. 처음에는 답답했던 검은 화면의 로그들이, 이제는 내 가장 신뢰할 수 있는 조언자가 되었습니다.

오늘부터 당신도 빌드가 실패하면, 먼저 GitHub Actions 로그를 열어보세요. 그곳에 당신의 문제를 해결할 정확한 답이 있을 것입니다.