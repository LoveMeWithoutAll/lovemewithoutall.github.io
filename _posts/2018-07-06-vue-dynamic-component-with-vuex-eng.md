---
layout: single
title: Using Dynamic Components with Vuex in Vue.js
date: 2018-07-04 11:45:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/vue-dynamic-component.png"
    image: "/assets/images/vue-dynamic-component.png"
categories:
- IT
tags: [IT, Vue.js]
---

When navigating between or changing screens in [Vue.js], I use one of two approaches.

1. Use [Vue Router] to navigate to a different component
1. Use a [Dynamic component] to change the component being rendered

I already covered approach 1 in [Implementing login in Vue.js].
So let's implement approach 2 this time.

## Swapping out components

It would be nice to render components by swapping them in and out however I want. That's exactly what a dynamic component is. Take a look at the code below.

```html
<template>
  <div class="container">
    <!-- We swap the component right below here -->
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

*getIsAuth* is pulled in as a computed property from [Vuex]. So whenever the value of *getIsAuth* changes, the value bound to the *is* attribute of the *component* element changes right away. How to set *getIsAuth* in [Vuex] is explained in [Implementing login in Vue.js]. In the template above, when *getIsAuth* is true it renders the **Result** component, and when it's false it renders the **AdminLogin** component. Since we didn't use the *keep-alive* element, the component is destroyed and then created every time the rendered component changes.

If it's hard to understand how the code above works, take a look at the code below. The example code above behaves the same as the code below.

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

## 2. Loading components only when needed

When you build a large-scale SPA, loading every component on the first screen is too much. So it's better to load components when they're needed. Take a look at the code below.

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
  components: { // This is where we load dynamically
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

That got long-winded, but the key is this.

```javascript
components: { // This is where we load dynamically
  'ComponentName': () => import('../components/component-file-location')
}
```

Of course, in a real-world giant SPA you'll need to import separately bundled modules.

The end!

[Vue.js]: https://vuejs.org/
[Vue Router]: https://router.vuejs.org/kr/guide/
[Dynamic component]: https://vuejs.org/v2/guide/components-dynamic-async.html
[Vuex]: https://vuex.vuejs.org/kr/
[Implementing login in Vue.js]: (https://lovemewithoutall.github.io/it/vue-login-demo/)
