---
layout: single
title: Vuetify에서 Jest 초기 셋업
date: 2018-12-27 15:05:30.000000000 +09:00
type: post
header:
    teaser: "https://jestjs.io/img/jest.svg"
    image: "https://jestjs.io/img/jest.svg"
categories:
- IT
tags: [Vuetify, Vue, Jest]
---

## 현상

[Vuetify]를 `add`하고 [Jest]로 `Unit Test`를 하면 아래와 같은 오류가 뜬다.

```javascript
  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-container> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-layout> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-layout> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-flex> - did you register the component correctly? For
recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-img> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-flex> - did you register the component correctly? For
recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-flex> - did you register the component correctly? For
recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-layout> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-flex> - did you register the component correctly? For
recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-layout> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-flex> - did you register the component correctly? For
recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>

  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-layout> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>
```

## 원인

[Jest]가 [Vuetify]를 불러오지 않았기 때문이다.

## 해결법

[Jest] 설정 파일을 아래와 같이 수정해주자.

```typescript
// \tests\unit\example.spec.ts
import { shallowMount } from '@vue/test-utils'
import HelloWorld from '@/components/HelloWorld.vue'
import Vue from 'vue'
import Vuetify from 'vuetify'

Vue.use(Vuetify)

describe('HelloWorld.vue', () => {
  it('renders props.msg when passed', () => {
    const msg = 'new message'
    const wrapper = shallowMount(HelloWorld, {
      propsData: { msg },
    })
    expect(wrapper.text()).toMatch(msg)
  })
})
```

## 끝!

* 참고: https://github.com/vuetifyjs/vue-cli-plugin-vuetify/issues/9#issuecomment-426215522

[Vuetify]: https://github.com/vuetifyjs/vuetify
[Jest]: https://jestjs.io/