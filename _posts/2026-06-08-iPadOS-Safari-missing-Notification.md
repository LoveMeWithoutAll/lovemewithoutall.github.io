---
layout: single
title: "iPadOS Safari ReferenceError: Can't find variable: Notification"
date: 2026-06-08 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTE5avojQC4sVS83b4AZXWCg6GEz_zXbqExaA&s"
    image: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTE5avojQC4sVS83b4AZXWCg6GEz_zXbqExaA&s"
categories:
- IT
tags: [safari]
---

## 현상

- iPad Safari 탭으로 접속 시 useEffect 안에서 ReferenceError: Can't find variable: Notification 발생
- 데스크톱(Chrome·Safari·Firefox)에선 정상, 실제 iOS/iPadOS Safari 탭에서만 발생.
- [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Notification)에서는 Notification 지원 한다고 했는데 왜??


## 원인

- iOS/iPadOS의 일반 Safari & WebView 에서는 browser의 Notification API 를 지원하지 않는다. 
- MDN에서는 Notification API 를 지원한다고 되어 있지만 정확히 말하자면 일부 지원이다... 16.4+에서 홈화면 PWA에만 지원한다.

주석에는 이렇게 적혀 있다.
```
Safari on iOS 16.4 (Release date: 2023-03-27)

Partial support: The Notification interface is undefined, unless the page is a web app saved to the home screen. The app's manifest must have a non-default display value.
```

- eslint-plugin-compat은 caniuse 의 한계(MDN의 주석까지는 확인하지 않는다)로 Notification 미지원 문제를 잡아내지 못 한다
- Playwright e2e webkit test는 데스크톱 엔진이라 이 오류를 못 잡는다.


## 조치

typeof 가드 추가

```typescript
if (typeof Notification === 'undefined') return;
```


## 재발 방지 대책

- lint: no-restricted-globals로 전역 Notification 직접 접근 금지
- 테스트: 단위 테스트에서 사용하는 jsdom은 Notification이 없다. 그러니 Notification을 stub하지 않으면 쉽게 이 오류를 테스트 할 수 있다.


## 3줄 요약

1. iOS/iPadOS Safari 는 Notification API를 일부(!) 지원
1. no-restricted-globals lint로 직접 접근 금지 + 테스트로 부재 분기 커버해 재발 방지
1. MDN을 자세히 읽어야 한다.

20260608
