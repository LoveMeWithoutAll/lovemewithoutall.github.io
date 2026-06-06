---
layout: single
title: Vite's polyfills work independently of vue-tsc
date: 2023-05-16 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://vitejs.dev/logo-with-shadow.png"
    image: "https://vitejs.dev/logo-with-shadow.png"
categories:
- IT
tags: [Vite]
---

`Vite`'s automatic polyfilling and `vue-tsc`'s type checking operate completely independently of each other.

`Vite` injects polyfills at build time according to the `legacy target` setting in `vite.config.ts` shown below.

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
        targets: ['ie >= 11'], // Support IE11 and above
        additionalLegacyPolyfills: ['regenerator-runtime/runtime'], // Polyfill added manually
      }),
    ], 
    // ...
  };
});
```

But `vue-tsc` has no idea whether `Vite` is injecting polyfills. So, to get type checking for modern JavaScript syntax, you need to add the following setting to `tsconfig.json`. If you don't, you'll run into type errors such as the one saying that the `String` object has no `replaceAll` method.


```json
// tsconfig.json
{
  "compilerOptions": {
    "lib": [
      "ESNext", // Use the latest ES version. If needed, you can use only specific methods, like ES2021.String
      // ...
    ],
  }
}
```

Just set it to `ESNext` and put your mind at ease.

So what happens if you don't specify a JavaScript version in tsconfig.json and build a project that uses modern(?) syntax like `replaceAll`? `Vite` adds `replaceAll` as a polyfill. I built it myself and confirmed this, so there's no need to doubt it.

20230516
