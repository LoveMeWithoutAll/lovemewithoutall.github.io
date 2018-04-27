---
layout: single
title: Vue SPA 로그인 구현
date: 2018-04-26 18:40:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/vue-login.png"
    image: "/assets/images/vue-login.png"
categories:
- IT
tags: [Vue.js]
---

무슨 서비스가 되었든 로그인(인증)을 구현하기란 상당히 까다롭다. 요즘에는 좋은 라이브러리나 모듈이 많이 있어 그 고통에서 많이 해방되었지만, 여전히 이런저런 사정상 직접 구현해야 하는 경우도 있다. 이번에는 내가 그런 경우다. 그래서 하나 만들어보았다. 로그인 화면이 수문장처럼 길을 가로막고 있는 어플리케이션이다.

* 목표: Vue.js로 만드는 SPA에 로그인(인증) 기능을 넣자
* 사용한 기술
    * [Vue.js]
    * [Vuex]
    * [Vue-router]
    * [Axios]
    * [vee-validate]

## 1. Vuex
Vuex를 설치하고
```bash
# vuex 설치
npm install --save vuex
```

store와 그에 딸린 것들을 잡아준다.
```javascript
// src/vuex/store.js
import Vue from 'vue'
import Vuex from 'vuex'
import getters from './getters'
import actions from './actions'
import mutations from './mutations'

Vue.use(Vuex)

const state = {
  uid: '',
  errorState: '',
  isAuth: false
}

export default new Vuex.Store({
  state,
  mutations,
  getters,
  actions
})
```

```javascript
// src/vuex/getters.js
export default {
  getUid: state => state.uid,
  getErrorState: state => state.errorState,
  getIsAuth: state => state.isAuth
}
```

```javascript
// src/vuex/mutation_type.js
export const UID = 'UID'
export const ERROR_STATE = 'ERROR_STATE'
export const IS_AUTH = 'IS_AUTH'
```

```javascript
// src/vuex/mutation.js
import * as types from './mutation_types'

export default {
  [types.UID] (state, uid) {
    state.uid = uid
  },
  [types.ERROR_STATE] (state, errorState) {
    state.errorState = errorState
  },
  [types.IS_AUTH] (state, isAuth) {
    state.isAuth = isAuth
  }
}
```

```javascript
// src/vuex/actions.js
export default {
  async login (store, {uid, password}) {
    /* 로그인은 백엔드를 다녀와야 하냐 비동기 처리를 한다 */
  }
}

```

```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'
import store from './vuex/store' // vuex 저장소 추가

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>',
  store // // vuex 저장소 추가
})
```

## 2. Login component

로그인 컴포넌트를 만든다. 일단 콘솔에 로그만 찍히는 껍데기부터 만들자.
```javascript
// src/components/Login.vue
<template>
  <div>
      <h2>Log In</h2>
      <form @submit="onSubmit">
          <input placeholder="Enter your ID" v-model="uid">
          <input placeholder="Enter your password" v-model="password">
          <button type="submit">Login</button>
      </form>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data: () => ({
    uid: '',
    password: ''
  }),
  methods: {
    onSubmit () {
      console.log(this.uid)
      console.log(this.password)
    }
  }
}
</script>

<style>

</style>
```

router에 등록한다.
```javascript
// src/router/index.js
import Vue from 'vue'
import Router from 'vue-router'
import Login from '@/components/Login' // 로그인 컴포넌트를 import 한다

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/', // 첫 화면을 로그인 화면으로 설정한다
      name: 'Login',
      component: Login
    }
  ]
})

```

## 3. action을 만들어 비동기 통신

먼저 axios를 설치하자.
```bash
npm install --save axios
```

백엔드와 API 통신할 서비스를 만들자.
```javascript
// src/sercice/loginAPI.js
import axios from 'axios'

const getUserInfo = (uid, password) => {
  return axios.get('/endpoint-for-get-user-info', {
    params: {
      'uid': uid,
      'password': password
    }
  })
}

const isFinished = uid => {
  return axios.get('/endpoint-for-is-finished', {
    params: {
      'uid': uid
    }
  })
}

export default {
  async login (uid, password) {
    try {
      const getUserInfoPromise = await getUserInfo(uid, password)
      const isFinishedPromise = await isFinished(uid) // Promise.all의 예시를 위해 집어넣음
      const [userInfoResponse, isFinishedResponse] = await Promise.all([getUserInfoPromise, isFinishedPromise])
      if (userInfoResponse.data.length === 0) return 'noAuth'
      if (isFinishedResponse.data[0].CNT > 0) return 'done'
      return userInfoResponse
    } catch (err) {
      console.error(err)
    }
  }
}
```

서비스도 index를 만들어 관리하자.
```javascript
// src/service/index.js
import loginAPI from './loginAPI'

export default {
  async login (uid, password) {
    try {
      const loginResponse = await loginAPI.login(uid, password)
      return loginResponse
    } catch (err) {
      console.error(err)
    }
  }
}
```

이제 action에서 API를 호출하고 vuex의 state들을 mutation 해주자.
```javascript
// src/vuex/actions.js
import {UID, IS_AUTH, ERROR_STATE} from './mutation_types'
import api from '../service'

let setUID = ({commit}, data) => {
  commit(UID, data)
}

let setErrorState = ({commit}, data) => {
  commit(ERROR_STATE, data)
}

let setIsAuth = ({commit}, data) => {
  commit(IS_AUTH, data)
}

export default {
  async login (store, {uid, password}) {
    let loginResponse = await api.login(uid, password)
    switch (loginResponse) {
      case 'noAuth':
        setErrorState(store, 'Not qualified or Wrong student number/Password. Please check it and log in again.')
        setIsAuth(store, false)
        break
      case 'done':
        setErrorState(store, 'You have already finished the questionnaire. Thank you.')
        setIsAuth(store, false)
        break
      default:
        setUID(store, loginResponse.data[0].UID)
        setErrorState(store, '')
        setIsAuth(store, true)
    }
    return store.getters.getIsAuth  // 결과를 리턴한다
  }
}
```

Login 컴포넌트에서 위 action을 호출하자. 로그인에 성공하면 console.log로 true가 찍히고, 실패하면 false가 찍힌다.
```javascript
// src/components/Login.vue
/* 생략 */
  methods: {
    ...mapActions(['login']),
    async onSubmit () {
      try {
        let loginResult = await this.login({uid: this.uid, password: this.password})
        console.log(loginResult) // 로그인 성공하면 true, 아니면 false
      } catch (err) {
        console.error(err)
      }
    }
  }
/* 생략 */
```

## 4. 로그인 결과 처리

로그인 성공하면 페이지를 이동한다. 라우터부터 등록해보자.
```javascript
// src/router/index.js
/* 생략 */
export default new Router({
  routes: [
    {
      path: '/', // 첫 화면을 로그인 화면으로 설정한다
      name: 'Login',
      component: Login
    },
    {
      path: '/helloWorld', // 추가하는 path
      name: 'HelloWorld',
      component: HelloWorld
    }
  ]
})
```

페이지를 이동시키자.
```javascript
// src/components/Login.vue
/* 생략 */
  methods: {
    ...mapActions(['login']),
    async onSubmit () {
      try {
        let loginResult = await this.login({uid: this.uid, password: this.password})
        if (loginResult) this.goToPages() // 페이지 이동!
      } catch (err) {
        console.error(err)
      }
    },
    goToPages () {
      this.$router.push({
        name: 'HelloWorld'
      })
    }
  }
/* 생략 */
```

로그인 실패할 경우의 처리도 해주자. 이미 로그인에 실패할 경우 actions에서 errorState에 값을 집어넣었으니, 그걸 기준으로 한다. errorState에 값이 없으면 빨간 글씨로 errorState를 띄워준다.
```html
<!-- src/components/Login.vue -->
<template>
  <div>
      <h2>Log In</h2>
      <div class="alert-danger" v-if="errorState">
        <p>{{ errorState }}</p> <!-- errorState가 있으면 표시한다 -->
      </div>
  </div>
</template>
```

```javascript
// src/components/Login.vue
<script>
import { mapActions, mapGetters } from 'vuex' // mapGetters를 추가한다

export default {
  name: 'Login',
  data: () => ({
    uid: '',
    password: ''
  }),
  methods: {
    ...mapActions(['login']),
    async onSubmit () {
      try {
        let loginResult = await this.login({uid: this.uid, password: this.password})
        if (loginResult) this.goToPages() // 로그인 실패시 loginResult === false 이므로 goToPages 메소드는 실행되지 않는다.
      } catch (err) {
        console.error(err)
      }
    },
    goToPages () {
      this.$router.push({
        name: 'HelloWorld' // HelloWorld로 가자
      })
    }
  },
  computed: {
    ...mapGetters({
      errorState: 'getErrorState' // errorState를 받는다
    })
  }
}
</script>

<style scoped>
.alert-danger p{ // 색깔도 칠해준다. 경고의 빨간 맛
  color: red;
}
</style>
```

## 5. 보안 강화

JWT를 Request Header에 박아넣자.
```javascript
// src/vuex/actions.js
export default {
  async login (uid, password) {
    try {
      const getUserInfoPromise = await getUserInfo(uid, password)
      const isFinishedPromise = await isFinished(uid)
      const [userInfoResponse, isFinishedResponse] = await Promise.all([getUserInfoPromise, isFinishedPromise])
      if (userInfoResponse.data.length === 0) return 'noAuth'
      if (isFinishedResponse.data[0].CNT > 0) return 'done'
      axios.defaults.headers.common['Authorization'] = userInfoResponse.jwt // Json Web Token을 request header에 넣는다
      return userInfoResponse
    } catch (err) {
      console.error(err)
    }
  }
}
```
백엔드에 Request Header를 검증하는 코드를 추가하자(여기선 프론트엔드만 다루므로 생략).

로그인하지 않고 URL을 통해 로그인 이후의 페이지에 접근할 수 있다. router에 이를 예방하는 코드를 추가하자.
```javascript
// src/router/index.js
/* 생략 */
// 로그인 성공시, actions에서 store에 isAuth값을 true로 바꿔줬다. 그걸 이용한다.
import store from '@/vuex/store' 

const requireAuth = () => (from, to, next) => {
  if (store.getters.getIsAuth) return next() // isAuth === true면 페이지 이동
  next('/') // isAuth === false면 다시 로그인 화면으로 이동
}

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Login',
      component: Login
    },
    {
      path: '/helloWorld',
      name: 'HelloWorld',
      component: HelloWorld,
      beforeEnter: requireAuth() // HelloWorld에 진입하기 전에 requireAuth 함수를 실행한다
    }
  ]
})
```

## 6. validation 적용
로그인을 위한 작업은 얼추 다 되었다. 이제 잡다한 마무리 작업을 하자. vee-validate로 입력값을 편하게 검증하자.

vee-validate를 설치하고,
```bash
npm install --save vuex
```

import를 한다.
```javascript
// src/main.js
/* 생략 */
import VeeValidate from 'vee-validate'
Vue.use(VeeValidate)
/* 생략 */
```

Login 컴포넌트에 적용한다. 반드시 input에 name을 줘야 동작하니 주의할 것.
```html
<!-- src/components/login.vue -->
<!-- 생략 -->
<form @submit.prevent="onSubmit">
    <input name="uid" placeholder="Enter your ID" v-model="uid" v-validate="'required'">
    <input name="password" placeholder="Enter your password" v-model="password" type="password" v-validate="'required|min:6'">
    <button type="submit">Login</button>
    <div class="alert-danger" v-if="errors.has('password')">{{errors.first('password')}}</div>
</form>
<!-- 생략 -->
```
```javascript
// src/components/login.vue
// 생략
methods: {
    ...mapActions(['login']),
    async onSubmit () {
      this.$validator.validateAll() // validation check를 하고
      if (!this.errors.any()) { // 아무 문제 없으면 아래 코드 실행
        try {
          let loginResult = await this.login({uid: this.uid, password: this.password})
          if (loginResult) this.goToPages()
        } catch (err) {
          console.error(err)
        }
      } else {
        console.log('validate err')
      }
    },
// 생략
```

## 7. 마무리
다 만들고 보니 async 범벅의 코드가 되었다. 다행히 동작은 잘 하긴 한다. 부득이 로그인 화면이 대문처럼 막고 있는 어플리케이션을 만들었지만, 요즘 세상에 굳이 이렇게 만들 이유는 없으리라 생각한다. 그렇다면 굳이 aync 범벅의 코드로 짤 필요는 없다. [vue-auth]의 예제를 참고하면 좋을 것이다.

[vee-validate]: https://vee-validate.logaretm.com
[Axios]: https://github.com/axios/axios
[Vue-router]: https://router.vuejs.org/
[Vuex]: https://vuex.vuejs.org/kr/
[Vue.js]: https://vuejs.org/
[vue-auth]: https://github.com/websanova/vue-auth