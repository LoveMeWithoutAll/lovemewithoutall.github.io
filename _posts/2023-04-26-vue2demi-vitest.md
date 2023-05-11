---
layout: single
title: Vue 2.7 demi + vitest 사용시 주의점
date: 2023-04-26 12:50:30.000000000 +09:00
type: post
header:
    teaser: "https://vitest.dev/logo-shadow.svg"
    image: "https://vitest.dev/logo-shadow.svg"
categories:
- IT
tags: [Vue.js]
---

* vitest 설정

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

* vue-test-utils 버전
vue-test-utils를 사용한다면 버전은 ^1.3.0으로 설치해야 한다. 최신 버전은 vue3 전용이다

* vue-test-utils와 composite API
vue-test-utils은 composite API를 사용한 SFC를 vue3component로 인식한다. 그래서 모든 테스트 코드가 공식 문서대로 동작한다고 보장할 수 없다. 예를 들어 아래와 같은 테스트 코드를 작성해보자.

```typescript
const wrapper = mount(component);
const buttonA = wrapper.findComponent(`.ButtonA`);
await buttonA.trigger('click');
```

`buttonA`를 클릭할 때 호출되는 함수(`v-on:click` 혹은 `watchEffect`)는 호출될까? 호출되지 않는다. 그러니 `click` 이벤트를 테스트 할 수 없다.

* 그러면 테스트는 어떻게 해?
나는 테스트 코드에서 store의 값을 직접 변경하는 방식으로 테스트한다. 어차피 대부분의 사용자 인터렉션은 store의 값을 변경하기 위한 작업이다. store 값을 변경하는 비즈니스 로직을 별도 함수로 분리하고, 테스트 코드에서 해당 함수를 호출한다. 요새 대세인 pinia를 store로 많이들 사용할텐데, 다행히 pinia는 vue 2.7을 지원한다.

```typescript
const component = mount(component);

const store = useStore();
store.data = 'test'; // pinia store의 갑 변경

await wrapper.vm.$nextTick(); // vue가 새로 렌더링 하기를 기다린다

expect(component.html()).toContain('test');
```

* 그러면 사용자 인터렉션은 테스트 못하나?
그건 e2e 테스트를 도입 하시라.

20230426
