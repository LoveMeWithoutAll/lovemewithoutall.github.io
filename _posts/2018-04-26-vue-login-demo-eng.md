---
layout: single
title: Implementing Login with Vue.js
date: 2018-04-26 18:40:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/vue-login.png"
    image: "/assets/images/vue-login.png"
categories:
- IT
tags: [Vue.js]
---

No matter what kind of service it is, implementing login (authentication) is quite tricky. These days there are plenty of good libraries and modules that have freed us from much of that pain, but for various reasons there are still cases where you have to implement it yourself. This time, that's the case for me. So I built one. It's an application where a login screen blocks the way like a gatekeeper.

* Goal: Add a login (authentication) feature to an SPA built with Vue.js
* Technologies used
    * [Vue.js]
    * [Vuex]
    * [Vue-router]
    * [Axios]
    * [vee-validate]

You can find the source code [here](https://github.com/LoveMeWithoutAll/vue-login-demo).

## 1. Vuex
Install Vuex,
```bash
# install vuex
npm install --save vuex
```

and set up the store and everything that goes with it.

### 1. store
You need to set up the getters, actions, and mutations below, or you'll get errors.
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

### 2. getters
```javascript
// src/vuex/getters.js
export default {
  getUid: state => state.uid,
  getErrorState: state => state.errorState,
  getIsAuth: state => state.isAuth
}
```

### 3. mutation_types
```javascript
// src/vuex/mutation_type.js
export const UID = 'UID'
export const ERROR_STATE = 'ERROR_STATE'
export const IS_AUTH = 'IS_AUTH'
```

### 4. mutations
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

### 5. actions
```javascript
// src/vuex/actions.js
export default {
  async login (store, {uid, password}) {
    /* Login needs to hit the backend, so it is handled asynchronously */
  }
}

```

### 6. Add vuex to vue
Add the store to Vue.
```javascript
// src/main.js
import Vue from 'vue'
import App from './App'
import router from './router'
import store from './vuex/store' // add the vuex store

Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```

## 2. Login component

### 1. Build the component shell
Create the login component. First, let's build a shell that just logs to the console.
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

### 2. Register it in the router
Register the login component in the router.
```javascript
// src/router/index.js
import Vue from 'vue'
import Router from 'vue-router'
import Login from '@/components/Login' // import the login component

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/', // set the first screen to the login screen
      name: 'Login',
      component: Login
    }
  ]
})

```

## 3. Login

### 1. Install axios
First, let's install axios.
```bash
npm install --save axios
```

### 2. Call the REST API
Let's create a service to communicate with the backend API for login.
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
      const isFinishedPromise = await isFinished(uid) // included as an example of Promise.all
      const [userInfoResponse, isFinishedResponse] = await Promise.all([getUserInfoPromise, isFinishedPromise])
      if (userInfoResponse.data.length === 0) return 'noAuth' // branch based on the login result
      if (isFinishedResponse.data[0].CNT > 0) return 'done'
      return userInfoResponse
    } catch (err) {
      console.error(err)
    }
  }
}
```

### 3. Register the REST API in the service
Let's manage the service with an index too.
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

### 4. action
Now, let's call the API in the action and, depending on the result, mutate the vuex states.
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

// Take the result returned from the backend and store the login success/failure status in vuex.
let processResponse = (store, loginResponse) => {
  switch (loginResponse) {
    case 'noAuth':
      setErrorState(store, 'Wrong ID or Password')
      setIsAuth(store, false)
      break
    case 'done':
      setErrorState(store, 'No period')
      setIsAuth(store, false)
      break
    default:
      setUID(store, loginResponse.UID)
      setErrorState(store, '')
      setIsAuth(store, true)
  }
}

export default {
  async login (store, {uid, password}) {
    let loginResponse = await api.login(uid, password)
    processResponse(store, loginResponse)
    return store.getters.getIsAuth  // return the login result
  }
}
```

Now everything that needs to happen between vuex and the backend is done.

### 5. Connect to the component
Let's call the action above from the Login component. If login succeeds, true is logged with console.log; if it fails, false is logged.
```javascript
// src/components/Login.vue
/* omitted */
  methods: {
    ...mapActions(['login']),
    async onSubmit () {
      try {
        let loginResult = await this.login({uid: this.uid, password: this.password})
        console.log(loginResult) // true if login succeeds, false otherwise
      } catch (err) {
        console.error(err)
      }
    }
  }
/* omitted */
```

## 4. Handling the login result

### 1. Handling a successful login
When login succeeds, navigate to another page. We'll move to the HelloWorld component. Let's start by registering it in the router.
```javascript
// src/router/index.js
/* omitted */
export default new Router({
  routes: [
    {
      path: '/', // set the first screen to the login screen
      name: 'Login',
      component: Login
    },
    {
      path: '/helloWorld', // the path to add
      name: 'HelloWorld',
      component: HelloWorld // the component to add
    }
  ]
})
```

Let's navigate to the page.
```javascript
// src/components/Login.vue
/* omitted */
  methods: {
    ...mapActions(['login']),
    async onSubmit () {
      try {
        let loginResult = await this.login({uid: this.uid, password: this.password})
        if (loginResult) this.goToPages() // navigate!
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
/* omitted */
```

### 2. Handling a failed login
Let's also handle the case where login fails. When login fails, we set a value on errorState in the action (section 3-4 in this post). We use that as the basis. If errorState has no value, we display errorState in red text.
```html
<!-- src/components/Login.vue -->
<template>
  <div>
      <h2>Log In</h2>
      <div class="alert-danger" v-if="errorState"> <!-- show it if errorState has a value -->
        <p>{{ errorState }}</p>
      </div>
  </div>
</template>
```

```javascript
// src/components/Login.vue
<script>
import { mapActions, mapGetters } from 'vuex' // add mapGetters

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
        if (loginResult) this.goToPages() // when login fails, loginResult === false, so the goToPages method is not executed.
      } catch (err) {
        console.error(err)
      }
    },
    goToPages () {
      this.$router.push({
        name: 'HelloWorld' // go to HelloWorld
      })
    }
  },
  computed: {
    ...mapGetters({
      errorState: 'getErrorState' // get errorState via the getter
    })
  }
}
</script>

<style scoped>
.alert-danger p{ // add some color too. the red flavor of a warning
  color: red;
}
</style>
```

## 5. Strengthening security

### 1 jwt
To strengthen security, let's embed a [JWT] in the request header.
```javascript
// src/service/loginAPI.js
export default {
  async login (uid, password) {
    try {
      const getUserInfoPromise = await getUserInfo(uid, password)
      const isFinishedPromise = await isFinished(uid)
      const [userInfoResponse, isFinishedResponse] = await Promise.all([getUserInfoPromise, isFinishedPromise])
      if (userInfoResponse.data.length === 0) return 'noAuth'
      if (isFinishedResponse.data[0].CNT > 0) return 'done'
      axios.defaults.headers.common['Authorization'] = userInfoResponse.jwt // put the Json Web Token in the request header
      return userInfoResponse
    } catch (err) {
      console.error(err)
    }
  }
}
```
Add code on the backend to validate the request header (omitted here since we only cover the frontend).

### 2. Navigation guard
Without logging in, you can access post-login pages through the URL. Let's add code to the router to prevent this.
```javascript
// src/router/index.js
/* omitted */
// On a successful login, the actions set isAuth to true in the store. We use that.
import store from '@/vuex/store' 

const requireAuth = () => (from, to, next) => {
  if (store.getters.getIsAuth) return next() // if isAuth === true, navigate to the page
  next('/') // if isAuth === false, go back to the login screen
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
      beforeEnter: requireAuth() // run the requireAuth function before entering HelloWorld
    }
  ]
})
```

## 6. Applying validation
The work for login is pretty much done. Now let's take care of the miscellaneous finishing touches. Let's use vee-validate to validate the input values more easily. I used vee-validate v2.1.5.

### 1. Install vee-validate
Install vee-validate,
```bash
npm install --save vuex
```

and import it.
```javascript
// src/main.js
/* omitted */
import VeeValidate from 'vee-validate'
Vue.use(VeeValidate)
/* omitted */
```

### 2. Apply vee-validation
Apply it to the Login component. Note that you must give each input a name, or it won't work.
```html
<!-- src/components/login.vue -->
<!-- omitted -->
<form @submit.prevent="onSubmit">
    <input name="uid" placeholder="Enter your ID" v-model="uid" v-validate="'required'">
    <input name="password" placeholder="Enter your password" v-model="password" type="password" v-validate="'required|min:6'">
    <button type="submit">Login</button>
    <div class="alert-danger" v-if="errors.has('password')">{{errors.first('password')}}</div>
</form>
<!-- omitted -->
```
```javascript
// src/components/login.vue
// omitted
methods: {
    ...mapActions(['login']),
    async onSubmit () {
      this.$validator.validateAll() // run the validation check, and
      if (!this.errors.any()) { // if there are no problems, run the code below
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
// omitted
```

## 7. Wrapping up
After building it all, the code ended up full of async. Fortunately, it does work well. I reluctantly built an application where the login screen blocks the way like a front gate, but in this day and age there's probably no need to build it this way. In that case, there's no need to write such async-heavy code. It would be good to refer to the [vue-auth] examples. You might also want to read [Using dynamic components together with Vuex in Vue.js](https://lovemewithoutall.github.io/it/vue-dynamic-component-with-vuex/).

It's convenient to implement login using [Firebase auth](https://firebase.google.com/docs/auth/). I hope [this post](https://lovemewithoutall.github.io/it/vue-login-with-firebase-auth/) is helpful.

The end!

[vee-validate]: https://vee-validate.logaretm.com
[Axios]: https://github.com/axios/axios
[Vue-router]: https://router.vuejs.org/
[Vuex]: https://vuex.vuejs.org/kr/
[Vue.js]: https://vuejs.org/
[vue-auth]: https://github.com/websanova/vue-auth
[JWT]: https://jwt.io/
