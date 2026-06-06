---
layout: single
title: Things to Watch Out for When Using Vue 2.7 + vitest
date: 2023-04-26 12:50:30.000000000 +09:00
type: post
header:
    teaser: "https://vitest.dev/logo-shadow.svg"
    image: "https://vitest.dev/logo-shadow.svg"
categories:
- IT
tags: [Vue.js]
---

## 1. vitest configuration

vitest.config.ts does not 'extend' the vite.confit.ts file. It overwrites it. So you have to put all the settings essential for running vitest into vitest.config.ts. For example, if you don't configure the vue2 plugin like in the code below, the tests will run with vue3. As a result, you'll get vue syntax errors.

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

## 2. vue-test-utils version
If you use vue-test-utils, you must install version ^1.3.0. The latest version is vue3-only.

## 3. vue-test-utils and the composite API
vue-test-utils recognizes an SFC that uses the composite API as a vue3 component. So you can't guarantee that all of your test code works exactly as the official docs describe. For example, let's write a test like the one below.

```typescript
const wrapper = mount(component);
const buttonA = wrapper.findComponent(`.ButtonA`);
await buttonA.trigger('click');
```

When you click `buttonA`, does the function that gets called (`v-on:click` or `watchEffect`) actually run? It doesn't. So you can't test the `click` event.

If you look at the discussion in `https://github.com/vuejs/vue-test-utils/issues/1983`, it doesn't look like it'll be resolved any time soon either.

## 4. So how do I test, then?
I test by directly changing the store's values in the test code. After all, most user interactions are operations meant to change the store's values anyway. I separate the business logic that changes the store values into a dedicated function, and call that function in the test code. Many people will probably be using pinia, which is all the rage these days, as their store, and fortunately pinia supports vue 2.7.

```typescript
const component = mount(component);

const store = useStore();
store.data = 'test'; // change the value of the pinia store

await wrapper.vm.$nextTick(); // wait for vue to re-render

expect(component.html()).toContain('test');
```

If the store has an initial value, you can specify it in the `initState` provided by `createTestingPinia`.

```typescript
const wrapper = mount(method, {
  localVue,
  pinia: createTestingPinia({
    initialState: {
      productDetail: { pageType: 'UPDATE' }, // product edit mode
      salesInfo: { sellMethodCode: SELL_METHOD_CODE.USED }, // used-goods sale
    },
  }),
});
```

For reference, a getter created with computed can't be modified in pinia, so you have no choice but to use the method above.

## 5. So I can't test user interactions at all?
For that, go adopt e2e testing (I haven't done it myself either).
Vue3 has e2e test code written using vitest and puppeteer, so you can use it as a reference.
`https://github.com/vuejs/core/blob/main/packages/vue/__tests__/e2e/e2eUtils.ts`

## 6.Type

When you do `mount` or `shallowMount`, you get type errors like these.

```typescript
import { mount } from '@vue/test-utils';
import WhatComponent from '@/WhatComponent.vue'

const wrapper = mount(WhatComponent);
```

```typescript
TS2321: Excessive stack depth comparing types 'ComponentPublicInstance {     | WritableComputedOptions > = {}, M extends MethodOptions = {}, Mixin extends ComponentOptionsMixin = ComponentOptionsMixin, Extends extends ComponentOptionsMixin = ComponentOptionsMixin, Emits extends EmitsOpti...' and 'Vue  , Record , never, never, (event: string, ...args: any[]) => Vue  , Record , never, never, ...>>'.
TS2321: Excessive stack depth comparing types 'DefineComponent{     | WritableComputedOptions > = {}, M extends MethodOptions = {}, Mixin extends ComponentOptionsMixin = ComponentOptionsMixin, Extends extends ComponentOptionsMixin = ComponentOptionsMixin, Emits extends EmitsOptions = {}, EmitsNa...' and 'VueClass   , Record , never, never, (event: string, ...args: any[]) => Vue  , Record , never, never, ...>>>'.
TS2321: Excessive stack depth comparing types 'Vue3Instance{}, Readonly{     | WritableComputedOptions > = {}, M extends MethodOptions = {}, Mixin extends ComponentOptionsMixin = ComponentOptionsMixin, Extends extends ComponentOptionsMixin = ComponentOptionsMixin, Emits extends EmitsOptions = {...' and 'Vue  , Record , never, never, (event: string, ...args: any[]) => Vue  , Record , never, never, ...>>'.
TS2769: No overload matches this call.   The last overload gave the following error.     Argument of type 'DefineComponent<{ <RawBindings, D = {}, C extends Record<string, ComputedGetter<any> | WritableComputedOptions<any>> = {}, M extends MethodOptions = {}, Mixin extends ComponentOptionsMixin = ComponentOptionsMixin, Extends extends ComponentOptionsMixin = ComponentOptionsMixin, Emits extends EmitsOptions = {}, EmitsNa...' is not assignable to parameter of type 'ExtendedVue<Vue<Record<string, any>, Record<string, any>, never, never, (event: string, ...args: any[]) => Vue<Record<string, any>, Record<string, any>, never, never, ...>>, ... 6 more ..., ComponentOptionsMixin>'.       Type 'ComponentPublicInstanceConstructor<Vue3Instance<{}, Readonly<{ <RawBindings, D = {}, C extends Record<string, ComputedGetter<any> | WritableComputedOptions<any>> = {}, M extends MethodOptions = {}, Mixin extends ComponentOptionsMixin = ComponentOptionsMixin, Extends extends ComponentOptionsMixin = ComponentOptionsMi...' is missing the following properties from type 'VueConstructor<ExtractComputedReturns<{}> & Record<string, any> & Vue<Record<string, any>, Record<string, any>, never, never, (event: string, ...args: any[]) => Vue<...>> & ShallowUnwrapRef<...> & Vue<...>>': extend, nextTick, set, delete, and 10 more. 
```

This is [because `vue-test-utils` doesn't properly support Vue 2.7](https://github.com/vuejs/vue-test-utils/pull/1999). This issue doesn't look like it'll be resolved going forward either. But there is a workaround.

```typescript
import { mount } from '@vue/test-utils';
import WhatComponent from '@/WhatComponent.vue'

const component: Component = WhatComponent; // restrict the type of the imported component

const wrapper = mount(component);
```

As shown above, you just restrict the type of the imported component.

## 7. export default
When you `export default` a component, you get this error.

![vitest-export-default-error](/assets/images/vitest-export-default-error.png)

This error occurs because vitest doesn't properly support vue 2.7's composite api.

Since you can't `export default` inside `<script setup>/* vue setup code */</script>`, you can solve it by creating one more `<script>export default {}</script>` tag and pulling the `export default` out, as in the code below.

```typescript
<script lang="ts">
import BaseDropdown from '../BaseDropdown.vue';

export default {
  inheritAttrs: false,
  components: { BaseDropdown },
};
</script>
```

20230426
