---
title: "Jekyll _config.yml 설정 중 헷갈렸던 부분 정리"
date: 2026-04-03 09:00:00 +0900
categories: [development]
tags: [jekyll, config, github-pages]
---
Jekyll 블로그를 운영할 때 가장 자주 열어보게 되는 파일 중 하나가 `_config.yml`입니다.

특히 `url`, `baseurl`, `timezone`, `lang` 같은 항목은 사이트가 실제로 어떻게 보일지를 크게 좌우합니다.

겉보기에는 단순한 설정 파일이지만, 작은 오타 하나로도 링크나 홈 화면 출력이 달라질 수 있습니다.

이번 글에서는 초보자 입장에서 특히 헷갈렸던 설정 항목들을 중심으로 정리합니다.
---

## 1. `url` + `baseurl` 관계

### 1-1. GitHub Pages 사용자/프로젝트 페이지 구분

- **사용자 페이지**: `username.github.io` 형태  
  - 예: `https://mesektok.github.io`
  - 이 경우 보통 `baseurl: ""` 로 둡니다. [web:565]
- **프로젝트 페이지**: `username.github.io/project-name` 형태  
  - 예: `https://username.github.io/blog`
  - 이 경우 `baseurl: "/blog"` 처럼 **앞에 슬래시를 붙여** 설정합니다. [web:566]

예시:

```yaml
# 사용자 페이지
url: "https://mesektok.github.io"
baseurl: ""

# 프로젝트 페이지
url: "https://username.github.io"
baseurl: "/blog"
```

- `url` 은 사이트의 **루트 도메인**  
- `baseurl` 은 그 안에서 블로그가 시작되는 **하위 경로**입니다.

둘 중 하나라도 잘못 적으면 RSS, OG 이미지, 내부 링크 주소가 어색해집니다.

---

## 2. `timezone` 설정

Jekyll은 기본적으로 UTC를 기준으로 날짜를 처리합니다.  
한국 기준으로 날짜와 시간이 맞게 표시되도록 하려면 `timezone` 을 꼭 지정하는 것이 좋습니다.

```yaml
timezone: Asia/Seoul
```

- 포스트의 `date` 와 `timezone` 이 함께 맞춰져야  
  글 목록 정렬과 RSS 발행 시간도 기대한 대로 동작합니다.
- 타임존이 빠져 있으면  
  GitHub 빌드 환경(UTC 기준)으로 계산되어 날짜가 하루 어긋나 보이는 경우가 있습니다.

---

## 3. `lang` 설정

```yaml
lang: ko
```

- HTML `<html lang="ko">` 속성에 반영되어  
  검색엔진과 스크린리더가 페이지 언어를 판단할 때 사용됩니다. 
- 한국어 블로그라면 `ko`, 영어 블로그라면 `en` 으로 설정합니다.
- 다국어 지원을 할 계획이 있다면  
  나중에 `jekyll-polyglot` 같은 플러그인과 함께 더 세부적으로 다루게 됩니다. 

---

## 4. `title`, `tagline`, `description`

이 항목들은 눈에 보이는 텍스트와 검색 결과 미리보기에 직접 영향을 줍니다. 

```yaml
title: "mesektok의 블로그"
tagline: "하루 한 번 기록하기"
description: "GitHub Pages와 Jekyll, Chirpy로 운영하는 기술/기록 블로그"
```

- `title` : 브라우저 탭과 사이트 헤더에 주로 사용.
- `tagline` : 테마에 따라 사이드바나 헤더 아래 부제목으로 표시.
- `description` : 메타 태그를 통해 검색엔진과 SNS 미리보기 설명에 들어갑니다.

처음에는 대충 적어두기 쉬운데,  
블로그 방향이 잡히면 **짧고 명확하게** 다시 다듬어 주는 게 좋습니다.

---

## 5. `permalink` 규칙

URL 모양을 어떻게 가져갈지 정하는 설정입니다. 

보통 Jekyll 기본값이나 Chirpy 기본값을 그대로 써도 되지만,  
구조를 이해해 두면 나중에 URL 전략을 바꿀 때 도움이 됩니다.

예시:

```yaml
permalink: /:categories/:year/:month/:day/:title/
```

- `:categories` : `categories` front matter 기준 폴더 경로
- `:title` : 파일명에서 날짜 뒤 슬러그 부분
- 날짜를 포함하지 않는 짧은 URL을 원하면:

```yaml
permalink: /posts/:title/
```

주의할 점:

- 이미 발행된 글의 URL 구조를 변경하면  
  오래된 링크가 404가 되기 때문에 가급적 초기에만 바꾸는 것이 좋습니다.

---

## 6. `collections` 와 `defaults`

Chirpy처럼 구조가 있는 블로그에서는  
`_config.yml` 의 `collections` 와 `defaults` 도 자주 보게 됩니다. 

### 6-1. `collections`

```yaml
collections:
  posts:
    output: true
```

- 기본 `posts` 외에 `notes`, `projects` 같은 별도 컬렉션을 만들 수 있습니다.
- 아직 복잡한 구조가 필요 없다면  
  초보자는 `posts` 기본값만 유지해도 충분합니다.

### 6-2. `defaults`

```yaml
defaults:
  - scope:
      path: ""
      type: posts
    values:
      layout: post
      author: mesektok
```

- 특정 타입(예: posts)에 공통으로 들어갈 값을 미리 정의합니다.
- 파일마다 `layout: post` 를 쓰지 않아도 자동으로 적용되기 때문에  
  실수로 빠뜨리는 일을 줄여 줍니다.

---

## 7. GitHub Pages 관련 옵션

GitHub Pages에서 빌드할 때는  
허용된 플러그인만 사용해야 한다는 제약이 있습니다. 

대표적으로:

```yaml
plugins:
  - jekyll-feed
  - jekyll-seo-tag
```

- GitHub 공식 문서에 나온 허용 리스트 안의 플러그인만 사용해야  
  Actions나 Pages 빌드가 실패하지 않습니다.
- Chirpy starter는 이미 GitHub Pages 호환 구성을 맞춰 둔 상태라  
  초보자는 **플러그인 목록을 함부로 추가하지 않는 것**이 중요합니다. 

---

## 8. 변경 후에는 항상 빌드 확인

`_config.yml` 은 작은 오타도 빌드 전체를 깨뜨릴 수 있는 파일입니다.

설정을 바꾼 뒤에는 항상 아래 순서로 확인합니다.

1. GitHub에 커밋
2. **Actions 탭**에서 최신 워크플로 실행 상태 확인
3. 실패했다면 로그에서 `Configuration file` 아래 오류 메시지 확인 
4. 성공했다면 로컬/실제 사이트에서  
   - URL, 타이틀, 언어, 시간대가 의도대로 적용됐는지 확인

이 패턴만 몸에 익어도  
“_config.yml 조금 고쳤는데 사이트가 안 뜬다” 같은 상황을 빠르게 복구할 수 있습니다.

---

## 마무리

정리해 보면, 초보자가 `_config.yml` 에서 특히 신경 써야 할 부분은 다음 네 가지입니다.

- 도메인 구조에 맞는 `url` + `baseurl`  
- 한국 기준 시간대를 위한 `timezone: Asia/Seoul`  
- 언어 표기를 위한 `lang: ko`  
- GitHub Pages 호환 플러그인만 사용하는 것

이 네 가지를 먼저 정확히 맞춘 뒤,  
그 밖의 옵션(`title`, `description`, `permalink`, `defaults` 등)을  
조금씩 손보면 훨씬 덜 스트레스 받으면서 블로그를 운영할 수 있습니다.
