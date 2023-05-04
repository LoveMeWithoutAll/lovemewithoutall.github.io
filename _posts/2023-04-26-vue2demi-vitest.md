---
layout: single
title: Vue 2.7 demi + vitest 사용시 주의점
date: 2023-04-26 12:50:30.000000000 +09:00
type: post
header:
    teaser: "https://www.google.com/url?sa=i&url=https%3A%2F%2Fvitest.dev%2F&psig=AOvVaw1L4S3rdvRyEEvwG_dpAu7o&ust=1683281255615000&source=images&cd=vfe&ved=0CBEQjRxqFwoTCNj_g7y12_4CFQAAAAAdAAAAABAE"
    image: "https://www.google.com/url?sa=i&url=https%3A%2F%2Fvitest.dev%2F&psig=AOvVaw1L4S3rdvRyEEvwG_dpAu7o&ust=1683281255615000&source=images&cd=vfe&ved=0CBEQjRxqFwoTCNj_g7y12_4CFQAAAAAdAAAAABAE"
categories:
- IT
tags: [Three.js]
---

1. vitest 설정

vitest.config.ts는 vite.confit.ts 파일을 '확장'하지 않는다. 덮어쓴다. 따라서 vitest 실행에 필수적인 설정은 vitest.config.ts에 모두 설정해놔야 한다. 예를 들어, 아래 코드처럼 vue2 plugin 설정을 하지 않으면 vue3로 test를 실행한다. 따라서 vue syntax 오류가 발생한다

```typescript
import vue2 from '@vitejs/plugin-vue2';
import { defineConfig } from 'vitest/config';

export default defineConfig({
  plugins: [vue2()],
  test: {
    globals: true,
    environment: 'jsdom',
    alias: [{ find: /^vue$/, replacement: 'vue/dist/vue.runtime.common.js' }],
  },
});
```

2. vue-test-utils 버전
vue-test-utils를 사용한다면 버전은 ^1.3.0으로 설치해야 한다. 최신 버전은 vue3 전용이다

20230406
