---
title: "Jekyll 블로그에서 한 글을 여러 형식으로 발행하고 싶다면?"
slug: jekyll-multiple-formats-single-page
date: 2026-07-04 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, GitHub-Pages]
description: "HTML뿐 아니라 PDF, iCal 등 다양한 형식으로 콘텐츠를 제공하고 싶을 때 Jekyll에서 실제로 구현하는 방법"
---

정년을 맞이한 후 30년간의 강의 노트를 디지털화하려던 나는 한 가지 문제에 봉착했습니다. 학생들에게 공개하는 강의 일정을 웹사이트에 HTML로 올리면서 동시에 학생들의 달력 앱(Google Calendar, Outlook 등)에 자동으로 추가될 수 있도록 iCal 형식으로도 제공하고 싶었던 것입니다. Jekyll로 블로그를 운영하는 나로서는 난감한 상황이었습니다.

## 왜 한 글을 여러 형식으로 제공해야 할까요?

제 경우는 강의 일정이었지만, 현대의 콘텐츠 크리에이터라면 다양한 상황에서 이 문제를 만날 수 있습니다. 블로그 포스트를 HTML로 공개하면서 동시에 독자들이 다운로드할 수 있는 PDF 버전을 제공하고 싶을 수도 있고, 뉴스레터를 구독하는 사용자들을 위해 plaintext 형식으로도 배포하고 싶을 수도 있습니다. 더 나아가 연구 논문을 BibTeX 형식으로, 이벤트를 iCal 형식으로 제공하는 것도 사용자 경험 향상에 도움이 됩니다.

하지만 Jekyll의 기본 구조에서는 하나의 마크다운 파일이 하나의 HTML 페이지로만 변환됩니다. GitHub의 공식 이슈에서도 많은 사용자들이 이 제한을 지적했습니다. 이것이 바로 제가 직면한 문제였고, 제 경험과 함께 그 해결책을 공유하고 싶은 이유입니다.

## Jekyll의 현재 제한사항과 원인

Jekyll의 기본 동작 방식을 이해해야 합니다. 기본적으로 Jekyll은 한 개의 콘텐츠 파일(예: `_posts/2026-07-04-my-lecture.md`)에 대해 하나의 출력 형식(보통 HTML)을 생성합니다. 이는 Liquid 템플릿 엔진이 하나의 레이아웃 파일을 사용하도록 설계되었기 때문입니다.

더 정확히 말하면, YAML Front Matter에서 `layout: post`라고 선언하면, Jekyll은 `_layouts/post.html` 파일을 찾아 그것만 렌더링합니다. 만약 같은 콘텐츠로 PDF나 iCal 형식도 함께 생성하려면? 기본 방식으로는 불가능합니다. 여러 개의 마크다운 파일을 만들거나 매우 복잡한 커스텀 플러그인을 작성해야 했습니다.

저도 처음에는 같은 내용을 여러 마크다운 파일에 복사해서 관리하려고 했습니다. 강의 일정 정보를 `lecture.md`, `lecture.ics`, `lecture.txt`로 각각 만드는 방식이었지요. 그러나 이 방법은 콘텐츠를 수정할 때마다 여러 파일을 동시에 편집해야 하므로 실수하기 쉽고, 유지보수가 매우 번거로웠습니다.

## 실제 해결책: 플러그인과 post_write 후크 활용

제가 찾아낸 가장 실용적인 해결책은 Jekyll의 플러그인 시스템을 활용하는 것입니다. 특히 `post_write` 후크를 사용하여 HTML 생성 직후에 추가 형식의 파일들을 생성하는 방식입니다.

먼저 `_plugins/` 디렉토리에 `multi_format_generator.rb` 같은 파일을 만듭니다. 이 플러그인은 각 포스트가 HTML로 렌더링된 후, YAML Front Matter에 정의된 추가 형식들을 처리합니다. 예를 들어 포스트의 Front Matter에 `formats: [html, ics, pdf]`라고 명시하면, 플러그인이 해당 레이아웃 파일들(`post.html`, `post.ics`, `post.pdf`)을 찾아 모두 렌더링하는 방식입니다.

저의 강의 일정 포스트에서는 다음과 같이 작성했습니다:

```yaml
---
title: "2026년 2학기 강의 일정"
formats: [html, ics]
lecture_date: 2026-09-01
---
```

그리고 `_layouts/post.ics`라는 레이아웃 파일을 만들어서:

```
BEGIN:VCALENDAR
VERSION:2.0
PRODID:-//My University//Lecture Schedule//EN
BEGIN:VEVENT
UID:{{ page.slug }}@myuniversity.edu
DTSTAMP:{{ site.time | date: "%Y%m%dT%H%M%SZ" }}
DTSTART:{{ page.lecture_date | date: "%Y%m%d" }}
SUMMARY:{{ page.title }}
END:VEVENT
END:VCALENDAR
```

이런 식으로 iCal 형식의 레이아웃을 정의했습니다. 그 결과 `lecture.html`과 `lecture.ics` 두 파일이 동시에 생성됩니다.

## GitHub Pages 환경에서의 실무 적용 팁

한 가지 중요한 주의사항이 있습니다. GitHub Pages는 보안상의 이유로 특정 플러그인만 허용합니다. 따라서 위의 커스텀 플러그인 방식은 로컬에서는 작동하지만, GitHub Pages에 직접 푸시하면 실행되지 않습니다.

이를 해결하려면 GitHub Actions를 활용해야 합니다. GitHub Actions를 사용하면 로컬에서와 동일한 환경에서 Jekyll 빌드를 수행한 후, 생성된 파일들을 GitHub Pages로 배포할 수 있습니다. 저는 `jekyll-build-pages` 액션과 커스텀 플러그인을 함께 사용하여 모든 형식의 파일이 올바르게 생성되도록 설정했습니다.

더 간단한 방법도 있습니다. GitHub Pages의 제약을 피하기 위해, 빌드 프로세스를 분리하는 것입니다. `_site/` 디렉토리를 로컬에서 생성한 후 커밋하거나, GitHub Actions의 자동화 스크립트에 빌드 단계를 포함시키는 방식입니다. 저도 처음에는 이 방법을 사용했고, 요즘도 간단한 프로젝트에는 이 방식을 선호합니다.

## 마치며

제가 강의 일정을 iCal 형식으로 학생들에게 제공하기 시작한 후, 수강신청 시스템과 개인 캘린더의 자동 동기화 기능이 학생들에게 많은 편의를 제공했습니다. 정보 기술의 올바른 활용은 나이와 무관하게 사용자 경험을 크게 향상시킬 수 있다는 것을 깨닫게 되었습니다.

혹시 당신의 블로그에서도 콘텐츠를 여러 형식으로 제공하고 싶다면, 위의 방법들을 시도해 보세요. 처음에는 복잡해 보일 수 있지만, 한 번 설정해 놓으면 유지보수가 훨씬 수월해질 것입니다.