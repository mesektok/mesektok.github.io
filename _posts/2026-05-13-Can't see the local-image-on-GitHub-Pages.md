---
title: "GitHub Pages에서 로컬 이미지가 안 보인다? Jekyll 빌드 경로의 함정을 파헤쳐보자"
date: 2026-05-13 09:00:00 +0900
categories: [도구]
tags: [GitHub Pages, Jekyll, 이미지경로, 블로그운영]
description: "Jekyll Chirpy 테마에서 로컬 이미지 경로 문제로 고민하는 분들을 위한 실전 해결법"
---

## 처음 만난 문제, 그리고 삽질의 시작

제가 대학에서 정년을 앞두고 개인 블로그를 시작한 것이 2024년 초였습니다. 40년간 강의실에서만 지내온 저는 GitHub Pages와 Jekyll이라는 세상이 얼마나 복잡한지 전혀 몰랐습니다. 

처음에는 잘 진행되었습니다. Chirpy 테마를 설치하고, 마크다운으로 첫 번째 포스트를 작성했죠. 컴퓨터 공학 전공이었던 만큼 기본 개념들은 따라갈 수 있었습니다. 그런데 말입니다. 문제는 이미지를 삽입했을 때 나타났습니다.

로컬에서 `jekyll serve`로 테스트할 때는 이미지가 멀쩡하게 보이는데, GitHub Pages에 배포되면 깨진 이미지 아이콘만 떠 있었습니다. 마치 제 강의 자료에 그림이 모두 사라진 것처럼요. 제자들도 놀랄 문제였습니다.

## Jekyll의 baseurl 개념이 핵심이었다

며칠을 헤매다가 결국 발견한 것이 바로 `baseurl` 설정이었습니다. 

Jekyll의 `_config.yml` 파일을 보면 다음과 같은 설정이 있습니다:

```yaml
baseurl: ""
url: "https://yourusername.github.io"
```

문제는 여기 있었습니다. 저는 `username.github.io` 리포지토리를 사용하고 있었는데도, 마크다운에서 이미지를 다음과 같이 삽입했습니다:

```markdown
![설명](/images/my-image.png)
```

로컬에서는 작동합니다. 하지만 GitHub Pages의 웹 서버 입장에서 보면, 이 경로는 `https://yourusername.github.io/images/my-image.png`가 되어야 합니다. 만약 프로젝트 리포지토리(예: `myproject`)를 사용한다면, baseurl을 `/myproject`로 설정해야 하고, 이미지 경로는 `{{ site.baseurl }}/images/my-image.png`처럼 템플릿 변수를 사용해야 합니다.

Chirpy 테마를 사용하면서 이를 깨달은 순간, 제 강의에서 학생들에게 자주 말하던 말이 떠올랐습니다. "악마는 디테일에 숨어 있다." 바로 이것이었습니다.

## Chirpy 테마에서 올바른 이미지 삽입 방법

제가 결국 찾아낸 가장 안정적인 방법은 Chirpy 테마의 내장 이미지 처리 기능을 활용하는 것이었습니다.

먼저, 이미지 파일들은 프로젝트의 `/assets/img/` 디렉토리에 저장합니다. Chirpy 테마는 기본적으로 이 경로를 사용하도록 설계되어 있습니다.

마크다운에서 이미지를 삽입할 때는:

```markdown
![설명](/assets/img/posts/2026-05-13/my-image.png)
```

더 나은 방법은 Jekyll의 절대 경로 생성 필터를 사용하는 것입니다:

```markdown
![설명](/assets/img/posts/2026-05-13/my-image.png)
```

또는 Liquid 문법을 직접 활용할 수도 있습니다:

```html
![설명](<img src="{{ '/assets/img/posts/2026-05-13/my-image.png' | relative_url }}" alt="설명">)
```

이 세 가지 방법 중에서, 제가 가장 선호하는 것은 첫 번째입니다. 간단하면서도 명확하거든요. 다만 정확한 경로를 입력해야 한다는 단점이 있습니다.

## 로컬 테스트 환경의 중요성을 다시 깨닫다

이 문제를 겪으면서 저는 로컬 테스트의 중요성을 뼈저리게 느꼈습니다. 

제가 사용한 방법은 매우 간단합니다. 포스트를 작성한 후 `jekyll serve`를 실행하고, 브라우저에서 `http://localhost:4000`으로 접속합니다. 여기서 모든 것이 정상적으로 보이는지 확인하는 것입니다.

그런데 여기서 주의할 점이 있습니다. GitHub Pages와 정확히 같은 조건으로 테스트하려면, `jekyll serve --baseurl ""` 또는 `jekyll serve --baseurl "/myproject"` 명령어로 baseurl을 명시해서 실행해야 합니다.

저는 처음에 이를 모르고 그냥 `jekyll serve`만 했습니다. 결과적으로 baseurl이 무시되고 로컬 환경만 정상적으로 작동한 것이죠. 마치 교과서만 읽고 실험실에 안 간 학생처럼 말입니다.

## 추가 팁: GitHub 자동 배포의 함정

마지막으로 한 가지 더 공유하고 싶은 경험이 있습니다.

GitHub Actions를 사용하여 자동으로 빌드되고 배포되는 과정에서도 문제가 발생할 수 있습니다. 특히 `.github/workflows/` 디렉토리에 설정된 CI/CD 파일을 살펴봐야 합니다.

Chirpy 테마를 사용할 때는 대부분 이미 올바르게 설정되어 있지만, 커스터마이징하다 보면 실수할 수 있습니다. 저는 자동 배포 로
