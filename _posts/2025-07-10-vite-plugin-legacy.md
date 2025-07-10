---
layout: single
title: '@vitejs/plugin-legacy 정리'
date: 2025-07-10 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://vitejs.dev/logo-with-shadow.png"
    image: "https://vitejs.dev/logo-with-shadow.png"
categories:
- IT
tags: [javascript, vite]
---

# @vitejs/plugin-legacy 정리

## build output

@vitejs/plugin-legacy 는 build output을 2개 만든다.

1. modern build
2. legacy build

최신 브라우저에서는 modern build로 웹앱을 실행한다. 레거시 브라우저에서는 legacy build로 웹앱을 실행한다.

## modern과 legacy를 가르는 기준

브라우저가 

```html
<script type="module">
```

을 지원하느냐에 달렸다. ES module을 지원하느냐, 못 하냐?

- 지원 o: modern 브라우저
- 지원 x: legacy 브라우저

그러니까 `Chrome >= 64, Safari >= 12` 는 modern 브라우저다.

ES module 지원 외에도 기준이 더 있지만 자세한 설명은 생략한다.

## 회색 지대의 브라우저

safari 12는 module을 지원하지만, 애매하게 modern 한 API를 지원하지 않는 문제가 있다. 

Chrome 61 ~ 63 도 마찬가지다. 하지만 다행히 이것 때문에 문제가 발생하지는 않더라.

## 그래서 어느 브라우저까지 지원하는지는 어떻게 설정하나?

- targets 속성: legacy build가 지원할 브라우저 버전을 지정한다
- modernTargets 속성: modern build가 지원할 브라우저 버전을 지정한다

## 회색 지대의 브라우저가 있다고 하지 않았나?

맞다. 그래서 modernTarget: ['safari >= 13'] 으로 지정하면 문제가 발생한다.

modernTarget 설정은 트랜스파일러가 js 코드를 바꿀 때, '어느 수준까지' 하위 호환성을 확보하도록 바꾸는지 지정할 뿐이다. safari 12는 무조건 modern 브라우저로 분류된다. 그러니 트랜스파일러가 safari 12를 지원하게 하려면 'safari >= 12'로 지정해야 한다.

복잡해지니 modernTarget은 설정하지 말라. 기본값에 `safari >= 12` 로 설정되어 있다.

## modernTarget 속성만으로는 부족하다

safari 12를 modern API를 100% 지원하지는 않는다. 그러니 safari 12를 지원하고 싶으면 

### modernPolyfills 속성을 true로 설정해라. 

이러면 modern build에 필요한 polyfill만 자동으로 들어간다.

modernPolyfills는 기본값이 false니까 꼭 설정하라. 수동으로 원하는 polyfill만 넣도록 설정할 수도 있다. 하지만 몹시 귀찮으니 그냥 true로 해라.

## 아직도 polyfill 설정은 더 필요하다

@vitejs/plugin-legacy 는

### ECMAScript API만 자동으로 polyfill을 넣어준다
### Web, DOM, CSS API는 polyfill을 안 넣어준다!

## 그러면 Web, DOM, CSS API는 어떻게 하나?

additionalModernPolyfills & additionalLegacyPolyfills 속성에 각각 polyfill 이름을 명시해야 한다

골치 아플 것이다. 예를 들면 IntersectionObserver 지원이 그렇다. 

```js
additionalModernPolyfills: [
  'intersection-observer',           // Web API는 수동
]
```

왜 이렇게 골치아프게 만들어놨는지 모르겠다. 개발자가 @vitejs/plugin-legacy에 원하는 걸 모르나?

## 결론: 자동 아님

@vitejs/plugin-legacy 는 자동으로 polyfill을 넣지 않는다. 문서를 잘 읽고 사용해야 한다.

아래는 설정 예시

```js
// vite.config.js
import legacy from '@vitejs/plugin-legacy'
import { defineConfig } from 'vite'

legacy({
  targets: ['chrome >= 50', 'safari >= 11'],  // legacy build output이 지원할 브라우저 최소 버전
  modernPolyfills: true, // safari 12를 위하여 modern bulid output에도 JS 폴리필 자동 추가
  additionalModernPolyfills: [
    'intersection-observer',       // npm 패키지 이름
    'whatwg-fetch'
  ],
  additionalLegacyPolyfills: [    // legacy build에도 빼놓지 말라
    'intersection-observer'       // Web API라서 수동으로 지정해줘야 한다
  ]
})
```

EOD

20250710
