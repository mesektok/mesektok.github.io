---
title: "GitHub Pages에서 로컬 블로그가 안 보인다? Jekyll 빌드 오류를 잡아낸 40년 개발자의 노하우"
date: 2026-05-16 09:00:00 +0900
categories: [도구]
tags: [GitHub Pages, Jekyll, Chirpy, 트러블슈팅, 블로그]
description: "은퇴 앞둔 교수가 겪은 Jekyll 빌드 실패 경험과 해결책을 공개합니다."
---

## 프롤로그: 정년퇴직을 앞두고 시작한 기술 블로그

작년 가을, 저는 대학을 떠나기 전에 40년간 쌓아온 개발 경험을 정리하고 싶었습니다. 후학들을 위해 실전 경험담을 남기려는 욕심이었죠. "GitHub Pages와 Jekyll로 무료 블로그를 만들어야겠다"는 생각에 설렜던 그 날이 지금도 생생합니다.

그런데 예상과 달랐습니다. 처음 로컬 환경에서 Jekyll을 돌렸을 때 화면에 아무것도 뜨지 않았던 것입니다. 40년 경력도 무색했습니다. 2시간 동안 헤맸던 기억, 오늘 그 경험을 여러분과 나누고 싶습니다.

## 나를 괴롭혔던 "그 오류"의 정체

당시 저는 Chirpy 테마를 선택했는데, `bundle install` 후 `bundle exec jekyll serve`를 입력했을 때 이런 메시지를 봤습니다:

```
Configuration file: /Users/professor/blog/_config.yml
            Source: /Users/professor/blog
       Destination: /Users/professor/blog/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 0.432 seconds.
 Auto-regeneration: enabled for '/Users/professor/blog'
    Server address: http://127.0.0.1:4000
```

"잘 됐네?" 싶어서 브라우저를 열었습니다. 그런데 로드되는 내용이 기대와 달랐습니다. CSS가 전혀 적용되지 않았고, 이미지도 깨졌습니다. 마치 1990년대 웹사이트를 보는 듯했습니다.

대학 시절 처음 배운 프로그래밍이 생각났습니다. "문제를 체계적으로 파악하라"던 교수님 말씀. 저는 개발자 도구(F12)를 열었고, 콘솔 탭을 확인했습니다. 그곳에는 404 에러가 떠있었습니다. 정적 자산(CSS, JS, 이미지)을 찾지 못한 것이었습니다.

## 근본 원인: `baseurl` 설정의 함정

이틀을 고민한 끝에, 저는 `_config.yml` 파일을 다시 읽어보기로 결심했습니다. 경험상 설정 파일에 답이 있었거든요.

```yaml
baseurl: ""
url: "https://yourusername.github.io"
```

이 두 줄이 문제였습니다. GitHub Pages의 저장소 이름이 `yourusername.github.io`가 아닌 경우, `baseurl`을 반드시 설정해야 한다는 것을 놓쳤던 것입니다.

예를 들어, 제 저장소 이름이 `my-blog`라면:

```yaml
baseurl: "/my-blog"
url: "https://yourusername.github.io"
```

이렇게 수정해야 했습니다. 로컬 테스트를 위해서는:

```bash
bundle exec jekyll serve --baseurl "/my-blog"
```

이 명령어를 사용해야 했던 것입니다. 간단하지만 치명적인 실수였습니다.

## 해결 이후: 재현성 테스트와 예방법

문제를 해결한 후, 저는 같은 실수를 반복하지 않기 위해 몇 가지를 정리했습니다.

**첫째, 로컬 환경과 배포 환경의 차이를 명확히 하기.** 

로컬에서 완벽하게 보여도 GitHub Pages에 올렸을 때 깨질 수 있습니다. 저는 배포 전에 항상 이 명령어로 테스트했습니다:

```bash
JEKYLL_ENV=production bundle exec jekyll build
```

**둘째, Gemfile 관리의 중요성 인식.**

버전 충돌은 개발자의 영원한 숙제입니다. 저는 팀 동료나 다른 환경에서도 동일하게 작동하도록 `Gemfile.lock`을 항상 커밋했습니다:

```bash
git add Gemfile Gemfile.lock
git commit -m "Update dependencies"
```

**셋째, `.gitignore` 설정 확인.**

Jekyll은 `_site` 폴더, `.jekyll-cache` 폴더 등을 생성합니다. 이들은 GitHub에 올리면 안 됩니다. Chirpy 테마를 사용하면 기본으로 설정되어 있지만, 저는 항상 확인했습니다.

## 지난 6개월의 성과와 깨달음

그 오류를 해결한 이후, 저는 벌써 150개의 포스팅을 작성했습니다. 프로그래밍 언어별 팁, 알고리즘 문제 풀이, 그리고 인생 경험담 등. 

무엇보다 기쁜 것은, 이 블로그를 통해 세계 각지의 학생들과 교류하게 되었다는 것입니다. 시간대가 다른 곳에서 온 질문들에 답하며, 저는 여전히 배우고 있습니다.

지금 생각해보니, 그 2시간의 좌절은 값진 투자였습니다. 기술 문제해결의 본질을 다시 한 번 깨달았기 때문입니다.

## 행동 요청: 당신의 경험을 공유하세요

혹시 Jekyll이나 GitHub Pages로 블로그를 시작하려고 하시나요? 이 글이 도움이 되었다면, 댓글로 당신의 경험을 나눠주세요. 어떤 오류를 만났고, 어떻게 해결했는지 말이죠. 우리 모두가 함께 배우는 커뮤니티가 되었으면 좋겠습니다. 그리고 이 포스트가 도움이 되었다면 GitHub에서 제 블로그 저장소에 star를 눌러주시면 큰 격려가 될 것입니다.