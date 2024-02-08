---
layout: single
title: Vite의 polyfill은 vue-tsc와 별도로 동작한다
date: 2023-05-16 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://vitejs.dev/logo-with-shadow.png"
    image: "https://vitejs.dev/logo-with-shadow.png"
categories:
- IT
tags: [Vite]
---

`Vite`의 자동 폴리필과 `vue-tsc`의 타입 체크는 완전히 따로 논다. 

`Vite`는 `vite.config.ts`의 하기 `legacy target` 설정에 따라 빌드 시점에 폴리필을 넣어준다.

```typescript
// vite.config.ts
import legacy from '@vitejs/plugin-legacy';
import vue2 from '@vitejs/plugin-vue2';
import { defineConfig } from 'vite';

export default defineConfig(() => {
  return {
    // ...
    plugins: [
      vue2(),
      legacy({
        targets: ['ie >= 11'], // IE11 이상 지원
        additionalLegacyPolyfills: ['regenerator-runtime/runtime'], // 수동으로 추가하는 폴리필
      }),
    ], 
    // ...
  };
});
```

하지만 `vue-tsc`는 `Vite`가 폴리필을 넣어주는지 여부를 알지 못한다. 그러니 자바스크립트 최신 문법에 대한 타입 체크를 위하여 `tsconfig.json`에 아래와 같이 설정을 추가해야 한다. 이 설정을 해주지 않으면 `String` 객체에는 `replaceAll` 메소드가 없다는 등의 타입 오류를 보게 될 것이다.


```json
// tsconfig.json
{
  "compilerOptions": {
    "lib": [
      "ESNext", // ES 최신 버전 사용. 필요시 ES2021.String 처럼 일부 메소드만 사용 가능
      // ...
    ],
  }
}
```

마음 편하게 `ESNext` 로 설정해주자.

만약 tsconfig.json에 자바스크립트 버전을 명시하지 않고, `replaceAll` 같은 최신(?) 문법을 사용하여 프로젝트를 빌드하면 어떻게 될까? `Vite`가 `replaceAll`을 polyfill로 추가해준다. 직접 빌드해서 확인해보았으니 의심하지 않아도 된다.

20230516
