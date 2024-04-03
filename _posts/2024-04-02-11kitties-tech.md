---
layout: single
title: 11키티즈 개발에 사용한 기술과 사용 후기
date: 2024-04-02 09:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
    image: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
categories:
- IT
tags: [frontend]
---

40일의 개발 기간을 거쳐 11 키티즈를 런칭했다.

![game](/assets/images/2024-04-02-11kitties-tech/game-image.png)

귀여운 고양이를 키우는 게임을 만들기 위해 사용한 기술과, 사용하려 했지만 폐기한 기술을 하나씩 정리해보고자 한다.

## 사용한 기술

### 개발 환경

1. Bun
    1. 채택 사유: 빠르다
    2. 후기
      * 장점
        1. 아주 빠르다.
            1. 빌드 속도가 `node` 대비 3배 이상 빠르다
            1. 로컬 서버가 즉시 실행된다
        3. 문서가 꽤 친절하다
        
      * 단점
        1. 트러블 슈팅에 필요한 레퍼런스가 적다
        4. 버그가 많다.
            1. bun 단독으로 사용하기에는 문제가 없다. 하지만 다른 도구와 함께 사용할 때 문제가 발생한다.
            2. 사례: 기본 설정만으로는 `production build`를 할 수 없었다. bun의 환경변수 설정이 `Vite`의 환경변수 설정과 충돌하여 항상 `development build`만 되는 오류가 있었다. 이 문제를 해결하기 위해 빌드를 할 때마다 환경에 따른 `.env` 파일을 프로젝트 루트 디렉토리에 복사하는 스크립트를 추가해야 했다.
        1. Test suite의 레퍼런스 부족. 그래서 `github copilot`의 테스트 코드 자동 생성 기능을 그대로 사용할 수 없고, 생성된 테스트 코드를 약간 수정해야 했다. 하지만 잘 동작한다
        5. `pwa`와 함께 작동하기 위해서는 아래와 같은 의존성 overrides가 필요하다
```json
"overrides": {
  "@rollup/plugin-node-resolve": "^15.2.3"
}
```
        
2. biome
    1. 채택 사유
        1. `eslint/prettier`가 `webstorm`에서 정상 작동하지 않는다
        2. `eslint`와 `prettier`의 설정 충돌이 꼴보기 싫다
    2. 후기
        1. 잘 동작한다
        2. 레퍼런스는 극히 드물다
        3. 웹스톰과 연동이 적당히 잘 된다(버그는 있다)
3. Vite
    1. 채택 사유: 사실상의 표준
    2. 후기: 빠르고 설정이 편리하다.

### 라이브러리

1. React 18
    1. 채택 사유
        1. 익숙함
        2. 풍부한 생태계
        3. 강력함
        4. 커리어
    2. 후기: 만족
2. Zustand
    1. 채택 사유
        1. 다른 store를 물리치고 대세가 됨
        2. 편리한 `localStorage` 지원
    2. 후기
        1. 기존에 사용하던 `mobx`에 비해 편리한 syntax
        2. Store 규모를 적절히 선택할 수 있어서 좋다
        3. 학습 곡선이 평탄함
        4. `Vue pinia`와 비슷한듯?
        5. 최적화를 위해 localStorage를 함부로 사용하다가 섯부른 최적화의 쓴 맛을 보았다.
3. xState
    1. 채택 사유
        1. 게임 개발 중 상태 분기에 따른 sequential && multple && conditional 이벤트 처리에 유한상태머신이 필요했다. xstate가 없었다면 react component는 조건문 지옥에 빠졌을 것이다.
        2. 남들이 쓰니까 나도 한 번..
    2. 후기
        * 장점
            1. 기능은 좋다
            1. 깔끔한 선언형 코드 덕분에 기능 변경에 유연하고 유지보수가 쉽다
            1. 위 장점이 압도적이기 때문에 아래 기술한 여러 단점에도 불구하고 앞으로도 사용할 예정
        * 단점
            1. 문서가 허접하다
            3. type도 허접하다. 잘 만든 라이브러리는 type만 봐도 사용법을 짐작할 수 있는데, xstate는 그렇지 못하다. generic 지옥이다.
            4. 최신 버전이자 내가 사용한 버전인 v5의 레퍼런스가 적다
            5. 기능 자체에 대한 디버깅이 힘들다
            6. 학습 곡선이 가파르다
            7. 자체 구현한 유한상태머신에 비하여 오버스펙
            9. 디버깅을 하려면 `actors.getSnapshot().value`에 크게 의존해야 한다
1. React-query(v4)
    1. 채택 사유
        1. 쿼리 캐싱이 필요
        2. 익숙하고 유용하다
        4. 최신 버전인 v5를 쓰고 싶었으나 ios12를 지원하지 않아서…
    2. 후기
        1. 늘 문서가 불만이다
        2. 그래도 레퍼런스가 드문 V5보다는 낫다
        3. types은 v4도 여전히 generic 지옥

## 사용하려 했으나 폐기한 기술

1. Pwa = workbox + vitePWA
    1. 채택 사유: 게임에 필요한 대용량 이미지의 런타임 캐싱
    2. 후기
        1. 설정이 대단히 편리하다
        2. `apple`에서 `pwa`의 `caches api`를 지원하지 않아 좌절했다
        3. Response type이 `opaque`인 경우 처리하기 곤란했다. `Access-control-allow-origin`을 `*`로 설정하지 않을 경우, `access-control-allow-header`와 `access-control-allow-method`도 설정해야 한다는 사실을 몰라서 애먹었다
        1. 채택 목적이었던 대용량 이미지 캐싱이 더이상 필요하지 않아, 눈물을 머금고 폐기했다. 다음 프로젝트에서는 필요시 사용할 계획
1. Cypress
    1. 채택 사유
        1. `Safari` 베타 지원 시작
        2. `Cypress studio`의 존재
    2. 후기: 바빠서 제대로 못 써봄

20240402
