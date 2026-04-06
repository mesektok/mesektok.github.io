---
title: "GitHub Pages에 Chirpy 테마 적용한 과정"
date: 2026-04-02 09:00:00 +0900
categories: [development]
tags: [github-pages, chirpy, jekyll]
---

GitHub Pages로 블로그를 만들 때 Chirpy 테마는 꽤 깔끔한 선택지입니다.

사이드바 구조와 포스트 화면이 잘 정리되어 있어서 기술 블로그나 기록형 블로그에 잘 어울립니다.

다만 처음 적용할 때는 설정 파일과 폴더 구조를 정확히 이해해야 시행착오를 줄일 수 있습니다.

이 글에서는 Chirpy를 적용하면서 실제로 체크했던 순서를 정리해 보겠습니다.
---

## 1. Chirpy 스타터 템플릿 가져오기

### 1-1. 저장소 템플릿 사용

1. GitHub에서 `cotes2020/chirpy-starter` 저장소로 이동합니다.   
2. 우측 상단의 **Use this template → Create a new repository** 를 클릭합니다.
3. 새 저장소 이름을 `username.github.io` 형태로 지정합니다.  
4. Public 으로 생성합니다.

이렇게 하면 이미 Chirpy 설정이 포함된 템플릿을 내 계정으로 그대로 복사할 수 있습니다.

### 1-2. 로컬 클론(선택)

로컬에서 수정하고 싶다면:

```bash
git clone https://github.com/username/username.github.io.git
cd username.github.io
```

로컬 클론 없이 GitHub 웹 UI만으로도 초기 설정은 충분히 가능합니다.

---

## 2. 기본 설정 파일 점검하기 (`_config.yml`)

테마 적용 후 가장 먼저 확인한 파일이 `_config.yml` 입니다. 

특히 아래 항목들을 실제 내 환경에 맞게 바꿉니다.

```yaml
title: username의 기술 블로그
tagline: 기록하는 개발자
lang: ko
timezone: Asia/Seoul

url: https://username.github.io   # 커스텀 도메인 없을 때
# url: https://mydomain.com      # 커스텀 도메인 사용 시

github:
  username: username
```

- `title`, `tagline` : 사이트 제목과 부제목.
- `lang`, `timezone` : 검색·시간 표시에 영향을 줍니다.
- `url` : 나중에 OG 이미지, RSS 링크 등에서 기준이 되므로 꼭 맞춰 둡니다.

이 단계에서 **build 관련 설정은 건드리지 않고**  
사이트 정보만 먼저 정리했습니다.

---

## 3. GitHub Pages 배포 설정하기

Chirpy는 기본적으로 **GitHub Actions로 빌드 후 GitHub Pages에 배포**하는 방식을 사용합니다. 

1. 저장소에서 **Settings → Pages** 로 이동합니다.
2. **Source** 를 `GitHub Actions` 로 선택합니다.
3. 별도 브랜치 설정은 필요 없고, Actions 워크플로가 자동으로 Pages에 올립니다.

템플릿에는 이미 `.github/workflows/jekyll.yml` 이 포함되어 있어서  
별도 설정 없이도 커밋할 때마다 자동으로 빌드가 돌도록 구성되어 있습니다. 

---

## 4. 로고·프로필 등 기본 자산 교체

Chirpy starter는 기본 아바타·파비콘 등이 샘플로 들어 있습니다. 

### 4-1. 프로필 이미지

1. `assets/img/` 또는 `assets/img/avatar/` 위치를 확인합니다.  
2. 기존 샘플 이미지를 내 프로필 이미지로 교체합니다.  
3. `_config.yml` 또는 `_data/contact.yml` 에서 아바타 경로를 확인하고 필요하면 수정합니다.

예:

```yaml
avatar: /assets/img/site/profile.png
```

### 4-2. 파비콘(favicon)

1. `assets/img/favicons/` 폴더에 있는 기본 아이콘들을  
   파비콘 생성기로 만든 아이콘으로 교체합니다. 
2. `head.html` 관련 include는 이미 템플릿에 연결되어 있으므로  
   파일만 교체해도 브라우저 탭 아이콘이 바뀝니다.

---

## 5. 메뉴/사이드바 탭 구성 확인 (`_tabs`)

Chirpy는 상단/사이드 메뉴를 `_tabs` 폴더의 마크다운 파일로 관리합니다. 

1. `_tabs` 폴더를 열어 `about.md`, `categories.md`, `tags.md` 등을 확인합니다.
2. 필요 없는 탭은 파일 이름 앞에 `_` 를 붙여 비활성화하거나 삭제합니다.
3. `about.md` 내용은 내 소개에 맞게 수정합니다.

탭 하나당 하나의 페이지이기 때문에,  
메뉴 구성을 바꾸고 싶을 때는 `_tabs` 폴더만 손보면 됩니다.

---

## 6. 첫 테스트 포스트 작성 및 빌드 확인

테마가 정상 동작하는지 확인하기 위해 간단한 테스트 글을 먼저 작성했습니다.

1. `_posts` 폴더에 예시 글을 하나 복사하거나 새로 만듭니다.
2. 파일 이름은 `YYYY-MM-DD-slug.md` 형식으로 맞춥니다. 
3. 최소한 아래 정도의 front matter를 넣습니다.

```markdown
***
title: "Chirpy 첫 포스트 테스트"
date: 2026-04-02 10:00:00 +0900
categories: [test]
tags: [chirpy, test]
***
테마 적용이 잘 되었는지 확인하는 테스트 포스트입니다.
```

4. 커밋 후 **Actions → jekyll build** 워크플로가 성공(✅)했는지 확인합니다. 
5. 블로그 주소에 접속해 홈 화면에서 글이 보이는지 체크합니다.

이 단계에서 빌드가 실패한다면  
대부분 `_config.yml` 문법 오류나 front matter 오타이므로  
에러 로그의 파일/줄 번호를 보고 바로 수정합니다.

---

## 7. 댓글·검색·분석 도구 연동 여부 체크

Chirpy는 여러 외부 서비스를 옵션으로 지원하기 때문에  
처음에 한 번에 다 켜지 않고, **필요한 것만 순서대로** 확인했습니다. 

### 7-1. Google Analytics (예시)

```yaml
google_analytics:
  id: "G-XXXXXXXXXX"
```

- 환경변수나 별도 스크립트로도 설정할 수 있지만,  
  처음에는 `_config.yml` 의 ID만 설정해 동작 여부를 확인했습니다.

### 7-2. Disqus / giscus 댓글

댓글 시스템을 쓸 계획이 있다면  
Chirpy 문서에서 지원하는 방식대로 `_config.yml` 에 값을 넣고  
포스트 하단에 댓글 영역이 나오는지 확인합니다. 

---

## 8. 로컬 미리보기 환경(jekyll serve) 준비(선택)

계속 GitHub에 커밋하면서만 확인하면 느리기 때문에  
나중을 위해 로컬 실행 환경도 한 번 세팅해 두었습니다. 

1. Ruby와 Bundler 설치.
2. 프로젝트 폴더에서:

```bash
bundle install
bundle exec jekyll serve
```

3. 브라우저에서 `http://localhost:4000` 을 열어  
   실제 GitHub Pages와 거의 동일한 화면을 확인합니다.

템플릿에 이미 `Gemfile` 과 필요한 플러그인이 정의되어 있어  
추가 설정 없이 바로 실행되는 것이 장점입니다. 

---

## 9. 마지막 검증 체크리스트

Chirpy 테마 적용 후, 실제로 아래 항목들을 하나씩 확인했습니다.

1. 홈 화면에 최근 포스트 목록이 정상적으로 보이는가  
2. 포스트 상세 페이지 상단에 제목, 날짜, 카테고리, 태그가 깨지지 않는가  
3. 사이드바에 프로필, 카테고리, 태그, 최근 글 등이 의도대로 노출되는가  
4. 모바일 화면(브라우저 개발자 도구)에서 메뉴가 정상 동작하는가  
5. GitHub Actions가 새 커밋마다 자동으로 성공하는가  
6. 커스텀 도메인을 썼다면 `https://도메인` 으로 접속해서 SSL/리다이렉트가 정상인지

이 체크가 끝나면  
“GitHub Pages + Chirpy 기본 구조”는 안정적으로 갖춰진 상태라 볼 수 있고,  
이후에는 본문 작성, 이미지 구조, 광고(AdSense) 등 **콘텐츠와 수익화 단계**로 넘어가면 됩니다.
