---
title: "GitHub Actions 403 오류가 다섯 가지 다른 문제를 숨기고 있다면?"
slug: github-actions-403-permission-mystery
date: 2026-09-21 09:00:00 +0900
image: https://res.cloudinary.com/drw6zoumr/image/upload/v1789966845/damesektok/github-actions-403-permission-mystery.svg
render_with_liquid: false
categories: [기술]
tags: [GitHub, 자동화, GitHub Actions]
description: "같은 403 Resource not accessible 오류가 실은 다섯 가지 원인을 가지고 있다. 시니어 자동화 운영자라면 어디서부터 진단해야 할까."
---

## 모두가 겪지만 아무도 말하지 않는 문제

원문을 확인해본 결과, Reddit의 r/github 커뮤니티에서 GitHub Actions의 권한 문제(permission)에 관해 흥미로운 진단이 나왔습니다. 바로 `403 Resource not accessible by integration` 오류가 정말 같은 원인 하나로 발생하지 않는다는 것입니다.

이것은 단순한 기술 팁을 넘어서, 자동화 인프라를 직접 구축하고 관리하는 사람이라면 반드시 마주칠 수밖에 없는 설계 문제를 드러냅니다. 지난 수십 년간 대학에서 학생들을 가르치며 여러 기술 플랫폼의 성장과 한계를 지켜보면서, 이런 식의 오류 메시지는 "편의성"과 "정확성" 사이의 간극을 보여주는 전형적인 사례입니다. GitHub도 분명 좋은 의도로 통일된 오류를 만들었겠지만, 현장에서는 그것이 오히려 문제 해결을 더 어렵게 만들고 있습니다.

## 다섯 가지 다른 얼굴을 한 같은 오류

원문에서 제시한 다섯 가지 경우를 찬찬히 살펴보겠습니다.

**첫 번째는 가장 단순합니다**: workflow가 필요한 권한을 선언하지 않은 경우입니다. 예를 들어 릴리스를 만들려면 `contents: write`가 필요한데, workflow 파일에 `contents: read`만 있으면 403 오류가 납니다. 이 경우는 비교적 쉽습니다.

**두 번째는 훨씬 미묘합니다**: workflow 파일에 `contents: write`라고 명시했어도, 실행 환경이 fork된 저장소의 pull request라면 자동으로 read-only가 됩니다. 이것은 GitHub의 보안 정책이므로, 사용자가 권한을 더 주려고 해도 소용이 없습니다. 이 부분이 특히 까다로운 이유는 workflow 파일과 실제 동작이 일치하지 않기 때문입니다.

**세 번째는 구조적 실수입니다**: workflow 수준에서 `permissions: contents: write`를 선언했지만, 개별 job 수준에서 다시 `permissions: contents: read`라고 override(덮어씀) 했을 때입니다. 규모가 큰 workflow에서는 이런 부분을 놓치기 쉽습니다.

**네 번째는 근본적인 설계 문제입니다**: GITHUB_TOKEN은 workflow가 속한 저장소에만 접근할 수 있습니다(repository boundary). 다른 저장소에 접근하려면 GITHUB_TOKEN이 아닌 다른 자격증명(credential)이 필요합니다. 이 경우 workflow의 로컬 권한을 아무리 추가해도 해결되지 않습니다.

**다섯 번째는 위험한 "어둠의 마법"입니다**: `permissions: write-all`을 추가하면 403 오류가 일단 사라집니다. 하지만 이것은 문제를 해결한 것이 아니라, 전역 권한을 과도하게 높여서 오류를 덮은 것입니다. fork 제약(fork restriction), 저장소 경계(repo boundary), 조직 정책(organization policy), 또는 토큰 유형(token type) 때문이었다면 write-all도 근본 해결이 아닙니다.

## 시니어 자동화 운영자가 이것을 봐야 하는 이유

n8n 같은 자동화 인프라를 직접 구축하거나 GitHub Actions를 조직 규모로 운영하는 사람이라면, 이 문제는 단순한 "권한 설정 오류"가 아닙니다. 이것은 **오류 메시지가 근본 원인을 명확히 알려주지 않을 때, 팀 전체가 시간을 낭비할 수 있다**는 의미입니다.

예를 들어, 당신이 자동화 인프라의 보안을 책임지고 있다면:
- 누군가 권한 문제로 막혔을 때 어디서부터 진단할지 알아야 합니다.
- write-all 같은 "손쉬운 해결책"이 실은 조직의 보안 정책을 우회한다는 것을 알아야 합니다.
- fork된 저장소나 외부 저장소와의 연동 설계 단계부터 다르게 접근해야 합니다.

이것은 기술 습관의 문제이기도 합니다. 지난 35년간 교육 현장에서 본 패턴 중 하나는, 기술 플랫폼이 성장하면서 편의성을 강조하다가, 결국 그 편의성이 정확성과 보안을 흐릿하게 만든다는 점입니다. GitHub Actions의 오류 메시지 통일도 비슷한 맥락입니다. 개발자 경험을 개선하려는 의도가 있었지만, 현장에서는 오히려 진단을 더 어렵게 만들었습니다.

## 이것이 계속 지켜봐야 할 지점입니다

원문에서 강조한 부분이 있습니다: "The tricky part is that GitHub often surfaces the same vague error for all of these cases(까다로운 점은 GitHub이 모든 경우에 똑같은 모호한 오류를 보여준다는 것)." 이것이 GitHub 측에서 개선될 것인가, 아니면 커뮤니티가 체크리스트와 진단 가이드를 따로 만들어 갈 것인가 하는 논쟁이 있는데, 이 부분은 계속 지켜봐야 할 지점입니다.

이 이슈는 다음 편에서 실제 자동화 워크플로우 설계 사례에서 어떻게 나타나고, 어떻게 예방할 수 있는지 구체적으로 이어서 다뤄보겠습니다.