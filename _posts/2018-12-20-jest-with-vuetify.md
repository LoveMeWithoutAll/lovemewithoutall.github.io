---
layout: single
title: Vuetify에서 Jest 초기 셋업
date: 2019-07-05 15:05:30.000000000 +09:00
type: post
header:
    teaser: "https://jestjs.io/img/jest.svg"
    image: "https://jestjs.io/img/jest.svg"
categories:
- IT
tags: [Vuetify, Vue, Jest]
---

## 환경

* [Vue CLI] v3.8.4
* vue-test-utils v1.0.0-beta.29
* babel-jest v23.6.0

## 현상

[Vue CLI]에서 [Vuetify]를 `add`하고 [Jest]로 `Unit Test`를 하면 아래와 같은 오류가 뜬다.

```typescript
  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-container> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>
// ...
```

[Jest]가 [Vuetify]를 불러오지 않았기 때문이다.

[Vuetify]를 전역으로 사용하면 오류가 해결된다.

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

<b>하지만</b> 보통은 아래와 같이 `createLocalVue`를 사용하는 것이 일반적이다.

```typescript
import { shallowMount, createLocalVue } from '@vue/test-utils'
import Vuetify from 'vuetify'
import TestComponent from '@/views/TestComponent.vue'

const localVue = createLocalVue()
localVue.use(Vuetify)
```

이러면 아래와 같은 오류가 발생한다.

```typescript
  console.error node_modules/vuetify/dist/vuetify.js:22968
    [Vuetify] Multiple instances of Vue detected
    See https://github.com/vuetifyjs/vuetify/issues/4068

    If you're seeing "$attrs is readonly", it's caused by this
```

## 원인

원인은 [Vuetify]에 있다. 이 [issue](https://github.com/vuetifyjs/vuetify/issues/4964#issuecomment-477933121)를 읽어보라.

## 해결책

2019.07.04. 현재로서는 문제를 깔끔하게 해결할 수 없다. 다만 [앞으로 고친다고 한다](https://github.com/vuetifyjs/vuetify/issues/4964#issuecomment-500574050). 그러니 이렇게 해보자.

jest-setup.js 파일을 만들어 `Vue`와 `Vuetify`를 미리 셋업해두자. 파일명은 뭐든 상관없다. 파일 위치는 적당히 두면 되는데, 나라면 `/src/tests/unit` 경로에 두는걸 추천한다.

```typescript
// jest-setup.js
import Vue from 'vue'
import Vuetify from 'vuetify'

Vue.use(Vuetify)
```

test를 수행할 때마다 위 파일이 실행되도록, `jest` 설정 파일이나 `Webpack`의 `jest` 프로퍼티에다 기입한다. `jest` 설정 파일에 기입하는걸 추천한다.

```typescript
// jest.config.js
module.exports = {
  // ...
  setupTestFrameworkScriptFile: '<rootDir>/tests/unit/jest-setup.js'
  // ...
}  
```

여기서 `<rootDir>`은 `package.json`이 있는 위치다.

이제 test를 재기동하면 오류 메시지는 더이상 보이지 않는다.

## 끝!

## 그 외

[jest]의 API 문서를 보면 [`setupTestFrameworkScriptFile`는 deprecated 되었다.](https://jestjs.io/docs/en/configuration#setupfilesafterenv-array). 하지만 내가 이 글을 쓰는 현재는 `vue-test-utils`가 [jest] v23.6.0을 사용하므로, [해당 버전의 API](https://jestjs.io/docs/en/23.x/configuration#setuptestframeworkscriptfile-string를 사용했다. 나중에 [jest]의 버전이 올라간다면, deprecated된 `setupTestFrameworkScriptFile` API 대신 `setupFilesAfterEnv` API를 사용하도록 하자.

[Vuetify]: https://github.com/vuetifyjs/vuetify
[Jest]: https://jestjs.io/
[Vue CLI]: (https://cli.vuejs.org/)
