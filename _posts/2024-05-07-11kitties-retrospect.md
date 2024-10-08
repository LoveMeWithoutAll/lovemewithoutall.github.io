---
layout: single
title: 11키티즈 FE 개발 회고
date: 2024-05-07 09:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
    image: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
categories:
- IT
tags: [frontend]
---

4/1 오픈 이후 대략 1달이 지났다. 서비스는 안정화 되었고 추가 개발 건도 착실히 진행 중이다. 이제 회고를 해도 좋은 때가 되었다. 프로젝트의 진행 단계별로 회고를 진행하고자 한다.

## 분석

게임 도메인 개발은 처음이었으나, 기획팀의 요구사항은 간단해보였다. 고양이는 밥을 먹고, 경험치가 쌓이며, 레벨이 올라가면 새 고양이를 뽑는다. 분석 단계에서 도출된 기술적 난제는 ‘잦은 수정 배포가 필요함’, 그리고 ‘고양이를 움직이게 해달라’ 정도였다.

돌이켜 보면 기술적 난제에 대한 고찰이 부족했다. 게임 도메인에 대한 경험 미비 때문이었다.

## 설계

설계는 기술적 난제를 해결하기 위한 대략적인 스케치다. 따라서 분석 단계에서 도출한 기술적 난제를 해결하기 위한 방법을 각각 궁리했다.

* 잦은 배포 -> 웹뷰로 개발
* 움직이는 이미지 -> 로컬 캐시 사용

이런 과제에 대한 해결 경험은 이미 충분했기에 조금도 걱정하지 않았다. 유일한 우려 사항은 촉박한 개발 기간 뿐이었다. 물론 개발 기간 부족은 심각했다. 하지만 이 또한 큰 걱정은 하지 않았다. 불가능해 보이는 개발 일정을 소화해 내는 게 내가 지금껏 해오던 일이다.

사용한 기술은 [11키티즈 개발에 사용한 기술과 사용 후기](https://lovemewithoutall.github.io/it/11kitties-tech/) 를 참고할 것.

## 개발

설계만 잘 해놓았으면 개발은 시간 문제에 불과한 것. 그러나 여태 그런 프로젝트는 겪어본 적이 없다. 이번에도 개발 도중에 발견한 난제가 여럿 있었다. 각 항목 별로 살펴본다.

### 섣부른(잘못된) 이미지 캐싱 최적화

고양이를 움직이게 하기 위해서는 고양이마다 최소 60Mb의 이미지 파일 다운로드가 필요했다. 디자인팀에 이미지 파일 용량 축소를 요청했으나 거절당했다. 이미지 퀄리티의 하락은 게임의 퀄리티 하락으로 이어진다는 것이 이유였다.

나는 정석적으로 이 문제를 해결하려 했다. 브라우저 local storage를 활용하여 고양이의 상태를 캐싱하고, 고양이의 상태에 따라 이미지를 browser cache에 오래도록 저장한 후, 이미지를 Preloading 하는 전략이었다. 하지만 60Mb는 거대한 용량이고, CDN 비용의 문제도 있어서 더욱 개선할 필요가 있었다. 이에 뒤늦게 PWA 레이어를 추가하여 browser cache를 직접 제어했다. iOS의 인앱 브라우저가 PWA의 cache API를 지원하지 않아 다소 곤란했으나 곧 우회책을 발견했다. 다운로드 속도는 만족스러울만큼 빨랐다. 하지만…

구형 모바일 기기에서 문제가 발생했다. 고양이 이미지는 1장당 10Mb가 넘어갔고, 구형 기기에서는 이미지 파일에 local cache를 적용한다 하더라도 여전히 렌더링에 시간이 오래 걸렸다. 결국 디자인팀의 허락을 얻어 이미지 파일의 용량을 2~3Mb로 크게 줄여서 문제를 해결했다. 돌이켜보면 캐싱보다 이게 더욱 정석적인 해결책이었다. 캐싱과 PWA 관련 코드는 여전히 동작했지만, 어플리케이션의 복잡성을 줄이기 위해 모두 삭제했다. 안 그래도 부족한 일정인데 복잡한 캐싱 로직과 설정을 더하느라 괜한 시간을 낭비한 셈이다.

### 복잡한 상태 분기 및 연계

이미지 캐싱 최적화가 해프닝으로 끝난 일이었다면, 복잡한 상태 분기 처리는 재앙을 낳을 뻔 했다. 동일한 user input일지라도 state에 따라 게임에서의 동작은 크게 달라져야 했다. 여기서 state에는 고양이의 상태, 레벨, 특별 이벤트 등 다양한 케이스를 포함해야 했고, 운영 중 언제든지 배포를 통해 로직 수정이 가능해야 했다. 게임 개발은 일반적인 웹 어플리케이션 개발과는 달랐다.

![calculator infinite state machine](https://miro.medium.com/v2/resize:fit:4800/format:webp/1*QhktO3TSniCjsuMC_vaL3w.jpeg)
<i>당연하지만 게임은 계산기보다 복잡하다(그림 출처: https://rvunabandi.medium.com/making-a-calculator-in-javascript-64193ea6a492)</i>

React의 useState와 zustand만으로도 어찌저찌 동작을 구현하는데는 성공했으나, 복잡성이 상당하여 유지보수가 쉽지 않아 보였다. 그래서 고심 끝에, 그리고 긴급하게 xstate를 도입하기로 결정했다. 예전에 Live11 프로젝트에서 그랬던 것처럼 유한상태머신을 직접 구현하는 방법도 있겠으나, 개발 기간이 촉박하여 이미 검증된 라이브러리를 사용했다. xstate의 기능이 본 프로젝트에서 필요로 하는 것 이상으로 오버 스펙이고, 학습 곡선이 상당하며, v5로 업그레이드 된지 얼마 지나지 않아 레퍼런스가 부족하다는 어려움이 있었다. 그러나 결과적으로 xstate의 도입은 성공적이었다. 선언적으로 기술된 상태머신은 본 프로젝트의 가장 복잡한 프로세스인 레벨업 프로세스를 견고하게 표현해냈다. 코드가 어찌나 깔끔해졌는지 QA 기간 동안 버그도 발견되지 않았다. 프로젝트 오픈 이후에도 xstate 덕분에 로직의 추가, 수정, 삭제 모두 편리하고 간단하게 해결했다. 적절한 솔루션 선택이 프로젝트의 성패를 좌우한다.

아쉬운 점은 복잡한 프로세스 구현이 필요한 모든 곳에 xstate를 사용하지는 않았다는 점이다. xstate를 처음 도입할 때만 해도 그 유용성에 대한 확신이 없었기 때문이다. 덕분에 xstate를 도입하지 않은 프로세스는 오랜 디버깅과 버그에 시달려야 했다.

## BE 연동

다른 프로젝트에서는 이틀이면 끝났을 BE연동이나, 이번 프로젝트에서는 4일이나 걸릴 정도로 고생했다. API가 60개에 달하는 복잡성이 1차적인 원인이겠으나, 분석 설계 단계에서부터 BE측과 FE측의 충분한 협의가 부족했기 때문이 아닌가 싶다. 기획의 요구사항이 무엇인지, 각 API 필드가 뜻하는 바가 무엇인지에 대하여 BE 연동 단계에서야 논의한 항목이 많았다. 추후 프로젝트 진행시 보완해야할 점이다.

## 테스트

BE 연동 단계에서 고생을 해서 그런지 QA 기간에는 버그가 많이 발견되지 않았다. 상부의 우려와는 달리 개발 기간 연장도 필요 없었다. 이 점에 대해서는 만족스럽게 생각한다.

## 총평

2명의 개발자가 1달 간 2000개가 넘는 commit을 했던 강도 높은 프로젝트였다. 잘 따라와준 팀원 김병호님께 감사한다.

비록 게임 개발 경험 부재로 인하여 분석 설계 단계에서 결함이 있었으나, 적절한 솔루션을 과감하게 도입하여 난제를 해결할 수 있었다. 이는 결과적으로 다행스러운 결과지만 한편으로는 아쉬움을 남긴다. 만약 게임 개발의 어려움과 xstate와 같은 라이브러리에 대하여 충분한 사전 조사가 있었다면 프로젝트 진행 중의 불안요소를 크게 줄일 수 있지 않았을까 싶다.

20240507
