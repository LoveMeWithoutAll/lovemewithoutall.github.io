---
layout: single
title: 마이크로 프론트엔드에서 변수명 충돌 오류
date: 2025-04-04 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://vite.dev/logo-with-shadow.png"
    image: "https://vite.dev/logo-with-shadow.png"
categories:
- IT
tags: [vite]
---

## 목표

### module federation 없이 간단한 마이크로 프론트엔드 구현

## 환경
* vite v6.2.0
* [vite + react + ts](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts)

## 현상

`index.html`에 복수의 마이크로 프론트엔드 build output을 아래와 같이 script tag를 이용하여 불러오면

```html
<script type="module" src="https://domain.com/home.js"></script>
<script type="module" src="https://domain.com/footer.js"></script>
```

다음과 같은 오류가 발생한다

```js
footer.js:49 Uncaught TypeError: O1.createRoot is not a function
    at footer.js:49:34687
(anonymous) @ footer.js:49
left-navigation-bar.js:48 Uncaught TypeError: Cannot read properties of undefined (reading 'S')
    at zy (left-navigation-bar.js:48:41683)
    at Oy (left-navigation-bar.js:49:33935)
    at left-navigation-bar.js:49:33958
zy @ left-navigation-bar.js:48
Oy @ left-navigation-bar.js:49
```

## 원인

import 한 마이크로 프론트엔드의 build output들이 같은 변수명과 함수명을 쓰고 있어서 그렇다.

## 해결법

### 방법 1
각 build output마다 다른 Namespace를 지정하면 된다.

이런 방식이다.
- footer.js -> footer
- header.js -> header

Namespace 지정을 위해 `rollupOptions`를 수정하자. `vite.config.ts`에서 아래와 같이 설정하면 된다.

```tsx
/** vite.config.ts */
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  build: {
    target: 'esnext',
    rollupOptions: {
      output: {
        // ...
        format: 'umd',
        inlineDynamicImports: true, // 동적 import 를 inline 으로 처리
        name: 'header', // namespace 지정. 다른 모듈에서는 다른 Namespace로 설정한다.
      }
    }
  }
})
```

### 방법 2
[vite에서 Libary로 빌드한다](https://ko.vite.dev/guide/build#library-mode). 이미 문서화가 잘 되어 있기 때문에 더 이상의 자세한 설명은 생략한다.

EOD

20250404
