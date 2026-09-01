---
title: "VS Code의 에이전트 창이 2026년 8월 어떻게 정리되었을까"
slug: vscode-agent-sessions-august-2026
date: 2026-09-01 09:00:00 +0900
image: https://res.cloudinary.com/drw6zoumr/image/upload/v1788238851/damesektok/vscode-agent-sessions-august-2026.svg
render_with_liquid: false
categories: [기술]
tags: [GitHub, Copilot, VS Code, 자동화, 에이전트]
description: "GitHub Changelog를 통해 본 VS Code v1.132~v1.135의 에이전트 세션 및 워크플로우 개선 사항과 1인 자동화 운영자의 관점에서의 의미"
---

## GitHub 원문을 정리해보니 보이는 것

오늘 이 소식을 살펴보니, 8월 한 달간 VS Code에 들어온 변화들이 꽤 구체적이다. v1.132부터 v1.135까지 네 개 버전에 걸쳐서 에이전트 세션(agent sessions)과 워크플로우 정리에 관한 기능들이 연달아 나왔다는 점이 눈에 띈다. 

원문을 확인해본 결과, 이번 업데이트의 핵심은 세 가지로 요약된다. 첫째, 여러 채팅창을 수평이나 수직으로 나란히 놓을 수 있게 했고, 둘째 채팅 기록 속에서 특정 프롬프트(prompt)로 빠르게 이동할 수 있는 타임라인 컨트롤이 생겼으며, 셋째 외부 애플리케이션에서 시작한 에이전트 세션을 VS Code 안에서 계속 이어갈 수 있도록 만들었다는 것이다(Continue external agent sessions).

## 1인 운영자 입장에서 생각해볼 점

내가 n8n 자동화 인프라를 직접 다루면서 깨달은 것 중 하나는, 워크플로우가 복잡해질수록 '어디에서 어느 흐름이 시작되었는가'를 추적하는 게 얼마나 중요한지 하는 것이었다. 

이번 업데이트에서 주목할 만한 부분은 여러 창을 나란히 두고 비교할 수 있다는 기능(Arrange chats side by side)이다. 한 번 생각해보면, 1인 자동화 운영자가 서로 다른 프롬프트에서 나온 결과물들을 한눈에 비교하려면 지금까지는 탭을 왕복해야 했다. 이제는 화면을 나눠서 "이 방식으로 요청했을 때"와 "저 방식으로 요청했을 때"의 차이를 실시간으로 볼 수 있게 된 것이다. 특히 n8n 같은 노코드(no-code) 자동화 플랫폼과 함께 쓸 때, 두 개의 서로 다른 LLM 프롬프트 결과를 비교하는 일이 잦다면 꽤 실용적이다.

또 하나 눈에 띄는 건 외부 에이전트 세션을 VS Code 안에서 이어갈 수 있다는 점이다. 원문에는 "View and continue recent Copilot or Claude agent sessions created in other applications"라고 나와 있다. 이건 워크스테이션, 모바일, 웹 브라우저 등 여러 곳에서 시작한 작업을 한곳으로 모을 수 있다는 뜻이다.

## 기술 생태계에 묻는 질문들

그런데 여기서 주의 깊게 봐야 할 지점이 있다. 원문을 읽다 보면 "Open the Agents window without GitHub sign-in"이라는 문장이 나온다. 이건 Claude 모델을 API 키로 연결하면 GitHub 계정 없이도 에이전트 창을 쓸 수 있다는 뜻인데, 동시에 "Switch model providers in Claude sessions"라는 표현도 있다. 즉, 한 세션 안에서 Anthropic 구독(Claude)의 모델과 Copilot 구독(OpenAI)의 모델을 오갈 수 있다는 것이다.

이것은 단순한 기능 추가가 아니라, GitHub/Microsoft와 Anthropic 사이의 관계 변화를 암시한다. 35년을 기술 교육에서 보내면서, 나는 이런 신호들이 얼마나 중요한지 안다. 한 기업의 플랫폼 안에서 경쟁 업체의 도구를 공식적으로 지원하기 시작한다는 건, 그 시장이 다원화되고 있다는 신호다.

또한 실험적 기능(experimental setting)으로 제시된 "/rubber-duck" 명령어도 흥미롭다. "Try the experimental /rubber-duck command in a Copilot Agent Host session to get a second opinion"이라는 표현은, 현재 LLM 기술에 대한 신뢰가 아직 조건부라는 걸 보여준다. 에이전트가 놓친 부분을 잡아내기 위해 '또 다른 모델의 의견'을 구한다는 것 자체가 흥미로운 접근이다.

## 남은 물음들과 앞으로의 시선

"Arrange chats side by side"와 "prompt timeline control"이 실제로 1인 운영자들의 디버깅(debugging) 속도를 얼마나 올릴 것인가에 대한 논쟁이 있을 텐데, 이 부분은 계속 지켜봐야 할 지점입니다.

이 이슈는 다음 편에서 실제 1인 자동화 운영 환경에서 이런 기능들이 어떤 영향을 미치기 시작했는지 이어서 다뤄보겠습니다.