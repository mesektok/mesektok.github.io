---
title: "Jekyll 블로그에서 한 페이지를 여러 형식으로 내보내기 - 실전 솔루션"
slug: jekyll-multiple-output-formats
date: 2026-07-04 09:00:00 +0900
render_with_liquid: false
categories: [기술]
tags: [GitHub, Jekyll, 블로그, GitHub-Pages]
description: "Jekyll에서 HTML과 PDF, iCal 등 여러 형식의 출력물을 한 번에 생성하는 방법"
---

작년 가을, 정년을 앞둔 후배 교수가 내게 웹사이트 제작을 의뢰했습니다. 35년을 대학에서 강의한 분이었는데, 은퇴 후에도 학생들을 위해 강의 일정과 자료를 공유하고 싶다고 했죠. 저도 한동안 Jekyll을 쓰고 있었기에 추천했는데, 예상 밖의 요구사항이 나왔습니다. "교수님 블로그에서 강의 일정을 HTML로도 보여주고, 동시에 학생들이 캘린더에 추가할 수 있도록 iCal 파일도 제공해야 한다"는 것이었습니다.

처음엔 단순하게 생각했습니다. 별도의 페이지를 두 개 만들면 되지 않을까 싶었는데, 같은 내용을 두 번 관리하는 것은 비효율적이었고, 나중에 수정할 때마다 두 파일을 모두 수정해야 하는 악순환에 빠졌습니다. 이 과정에서 Jekyll의 근본적인 한계를 마주하게 됐습니다.

## Jekyll의 한 페이지, 한 형식의 한계

Jekyll은 훌륭한 정적 사이트 생성기이지만, 기본적으로 한 개의 레이아웃(layout)은 하나의 출력 형식만 지원합니다. 마크다운 파일을 작성하면 HTML로 변환되고, 그것이 전부입니다. 만약 같은 글을 PDF나 iCal, JSON 등 여러 형식으로 제공하고 싶다면? 공식적으로는 방법이 없었습니다.

GitHub의 Jekyll 저장소를 뒤지다 보니 2014년 이후로 여러 개발자들이 동일한 문제를 제기해 왔더군요. 특히 이벤트 정보를 HTML과 iCal로 동시에 제공하거나, 블로그 글을 여러 형식으로 내보내고 싶은 사용자들의 요청이 많았습니다. 하지만 Core 팀은 이 기능을 정식으로 추가하지 않았습니다.

## 실전 솔루션 1: 다중 확장자 활용법

가장 실용적인 해결책은 **post-processing 방식**입니다. 저는 이를 후배 교수님 블로그에 적용했습니다.

먼저 `_posts` 폴더에 강의 일정 글을 마크다운으로 작성합니다. 예를 들어 `2026-07-15-summer-lecture.md`라는 파일입니다. 이 파일에는 YAML 프론트매터에 추가 정보를 입력해 둡니다:

```yaml
---
layout: post
title: "2026년 여름학기 특강 일정"
date: 2026-07-15
event_date: 2026-07-20T14:00:00
location: "대학로 강의실 302호"
generate_ical: true
---
```

그 다음, `_layouts` 폴더에 `ical.html`이라는 특수 레이아웃을 만듭니다. 이 파일은 iCal 형식의 데이터를 출력합니다:

```liquid
BEGIN:VCALENDAR
VERSION:2.0
PRODID:-//{{ site.title }}//EN
BEGIN:VEVENT
UID:{{ page.slug }}@example.com
DTSTAMP:{{ site.time | date: "%Y%m%dT%H%M%SZ" }}
DTSTART:{{ page.event_date | date: "%Y%m%dT%H%M%S" }}
SUMMARY:{{ page.title }}
LOCATION:{{ page.location }}
DESCRIPTION:{{ page.excerpt | xml_escape }}
END:VEVENT
END:VCALENDAR
```

이제 같은 마크다운 파일을 두 가지 방식으로 처리하는 방법이 필요합니다. 저는 Ruby 플러그인을 작성했습니다:

```ruby
Jekyll::Hooks.register :posts, :post_write do |post|
  if post.data['generate_ical']
    ical_content = post.renderer.render(post.data['ical_layout'])
    ical_path = post.destination('')
    ical_path = ical_path.sub(/\.html$/, '.ics')
    File.write(ical_path, ical_content)
  end
end
```

이 방식의 장점은 하나의 마크다운 파일만 유지하면서도 HTML과 iCal을 동시에 생성할 수 있다는 것입니다. 후배 교수님은 글을 한 번만 쓰고, 빌드하면 자동으로 두 파일이 생성되는 것을 보고 매우 만족해하셨습니다.

## 실전 솔루션 2: 외부 도구를 활용한 프로세싱

더 간단한 방법도 있습니다. 저는 또 다른 시니어 블로거를 위해 GitHub Actions를 활용했습니다.

Jekyll 빌드 이후, 별도의 Node.js 스크립트로 생성된 HTML 파일을 읽어서 다른 형식으로 변환하는 방식입니다. 예를 들어 `post-build.js`라는 파일에서:

```javascript
const html2pdf = require('html-pdf');
const fs = require('fs');
const path = require('path');

const postsDir = '_site';
fs.readdirSync(postsDir).forEach(file => {
  if (file.endsWith('.html')) {
    const htmlContent = fs.readFileSync(path.join(postsDir, file), 'utf8');
    const pdfPath = path.join(postsDir, file.replace('.html', '.pdf'));
    
    html2pdf.create(htmlContent).toFile(pdfPath, (err) => {
      if (err) console.log(err);
    });
  }
});
```

GitHub Actions의 워크플로우에 이를 추가하면:

```yaml
- name: Generate alternative formats
  run: node post-build.js
```

이 방식은 플러그인 작성의 복잡성을 피하면서도 매우 유연합니다. 향후 새로운 형식을 추가할 때도 JavaScript만 수정하면 되니까요.

## 실제 적용해본 후기

지난 6개월간 이 방식들을 실제로 적용해 봤습니다. 저와 협업한 세 분의 시니어 블로거 중 두 분은 다중 포맷 생성을 성공적으로 구현했고, 한 분은 좀 더 복잡한 요구사항(JSON 데이터 동시 생성)이 있어서 Liquid 템플릿을 직접 커스터마이징했습니다.

특히 인상적이었던 것은, 이렇게 구현한 후 블로그의 활용도가 크게 높아졌다는 점입니다. 후배 교수님은 강의 일정을 iCal로 제공함으로써 학생들의 캘린더 관리가 훨씬 수월해졌다고 하셨고, 다른 분들은 PDF 자동 생성으로 인쇄 기능 없이도 독자들이 언제든 글을 저장할 수 있게 되어 만족도가 올라갔다고 합니다.

Jekyll이 공식적으로 다중 형식 출력을 지원하지 않는다는 것은 아쉽지만, 이처럼 창의적인 방식으로 충분히 보완할 수 있습니다. 중요한 것은 자신의 블로그 목적에 맞춰 유연하게 대응하는 것입니다.

혹시 여러분도 Jekyll 블로그에서 같은 고민을 하고 계신가요? 댓글로 남겨 주시면 구체적인 구현 방법을 더 자세히 설명해 드릴 수 있습니다.