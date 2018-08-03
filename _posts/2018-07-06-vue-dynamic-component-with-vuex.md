---
layout: single
title: Vue.js에서 Vuex와 함께 동적 컴포넌트(다이나믹 컴포넌트) 사용하기
date: 2018-07-04 11:45:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/vue-dynamic-component.png"
    image: "/assets/images/vue-dynamic-component.png"
categories:
- IT
tags: [IT, Vue.js]
---

[Vue.js]에서 화면을 이동 혹은 변경할 때, 나는 2가지 방법 중 하나를 사용한다.

1. [Vue Router]를 사용하여 다른 컴포넌트로 이동한다
1. [Dynamic component]를 사용하여 렌더링하는 컴포넌트를 변경한다

1번 방법은 [Vue.js 로그인 구현]에서 이미 설명했다. 
그러니 2번 방법으로 구현해보자.

## Component 갈아끼우기

Component를 내가 원하는대로 갈아치우며 렌더링을 한다면 좋을 것이다. 이것이 동적 컴포넌트 그 자체다. 아래 코드를 보자.

```html
<template>
  <div class="container">
    <!-- 바로 아래에서 component를 갈아끼운다 -->
    <component :is="getIsAuth ? 'Result' : 'AdminLogin'"></component> 
  </div>
</template>

<script>
import AdminLogin from './AdminLogin'
import Result from './Result'

import { mapGetters } from 'vuex'

export default {
  components: {
    AdminLogin,
    Result
  },
  computed: {
    ...mapGetters(['getIsAuth'])
  }
}
</script>
```

*getIsAuth*는 [Vuex]에서 computed로 가져온다. 따라서 *getIsAuth*의 값이 변경되면, 바로 *component* 엘리먼트의 *is* 속성에 바인딩 된 값이 변경된다. [Vuex]에서 *getIsAuth*를 셋팅하는 방법은 [Vue.js 로그인 구현]에 나와있다. 위 템플릿에서는 *getIsAuth*의 값이 true면 **Result** 컴포넌트를 렌더링하고, false면 **AdminLogin** 컴포넌트를 렌더링한다. *keep-alive* 엘리먼트를 사용하지 않았기에 렌더링하는 컴포넌트가 변경될 때마다 destroy -> create를 한다.

위 코드가 어떻게 동작할지 이해가 잘 안간다면 아래 코드를 보자. 위 예제 코드는 아래 코드와 동일하게 동작한다.

```html
<template>
  <div class="container">
    <admin-login v-if="!getIsAuth"></admin-login>
    <result v-if="getIsAuth"></result>
  </div>
</template>

<script>
import AdminLogin from './AdminLogin'
import Result from './Result'

import { mapGetters } from 'vuex'

export default {
  components: {
    AdminLogin,
    Result
  },
  computed: {
    ...mapGetters(['getIsAuth'])
  }
}
</script>
```

## 2. Component를 필요할 때만 로딩하기

큰 규모의 SPA를 만들면 첫 화면에서 모든 component를 로딩하기 버겁다. 그러니 필요한 때에 component를 로딩하면 좋다. 아래 코드를 보자.

```html
<template>
  <div>
    <el-steps :active="active" finish-status="success">
      <el-step title="Step 1"></el-step>
      <el-step title="Step 2"></el-step>
      <el-step title="Step 3"></el-step>
      <el-step title="Step 4"></el-step>
    </el-steps>
    <component :is="whichStep"></component>
    <el-button style="margin-top: 12px;" @click="previous">Previous step</el-button>
    <el-button style="margin-top: 12px;" @click="next">Next step</el-button>
  </div>
</template>

<script>
export default {
  components: { // 여기서 동적으로 로딩한다
    'CounselType': () => import('../components/CounselType'),
    'Family': () => import('../components/Family'),
    'CounselSubject': () => import('../components/CounselSubject'),
    'CounselTime': () => import('../components/CounselTime')
  },
  data () {
    return {
      active: 0
    }
  },
  methods: {
    previous() {
      if (this.active-- === 0) this.active = 2
    },
    next() {
      if (this.active++ > 2) this.active = 0
    }
  },
  computed: {
    whichStep () {
      switch (this.active) {
        case 0:
          return 'CounselType'
        case 1:
          return 'Family'
        case 2:
          return 'CounselSubject'
        case 3:
          return 'CounselTime'
        default:
          return 'CounselType'
      }
    }
  }
}
</script>

<style>
</style>
```

장황해졌는데, 핵심은 이거다.

```javascript
components: { // 여기서 동적으로 로딩한다
  '컴포넌트명': () => import('../components/컴포넌트-파일-위치')
}
```

물론 현실의 거대 SPA에서는 import로 달리 번들링된 모듈을 가져와야 할 것이다.

끝!

[Vue.js]: https://vuejs.org/
[Vue Router]: https://router.vuejs.org/kr/guide/
[Dynamic component]: https://vuejs.org/v2/guide/components-dynamic-async.html
[Vuex]: https://vuex.vuejs.org/kr/
[Vue.js 로그인 구현]: (https://lovemewithoutall.github.io/it/vue-login-demo/)