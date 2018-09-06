---
layout: single
title: Vue login(sign in) with firebase auth
date: 2018-09-06 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
categories:
- IT
tags: [javascript, firebase, Vue]
---

# Login with Email and password

Building login(sign in) function with [Firebase auth] is simple and powerful. The below code is using Email address and password. Vue is watching user's session state change.

# Demo

## Scenario

1. Login with Email address and password
1. On login, move to index page
1. Login session is persisted even after user closes the browser
1. And logout!

![vue-login-with-firebase-auth](/assets/images/vue-login-with-firebase-auth.gif)

# Code

It works with [vuetify]. [Login.vue](https://github.com/LoveMeWithoutAll/book-blog/blob/master/src/components/Login.vue) send request to [Firebase auth]. And [Vuex store](https://github.com/LoveMeWithoutAll/book-blog/blob/master/src/vuex/store.js) catch change of auth state.

```html
<!-- Login.vue -->
<template>
  <v-flex xs12 md7 offset-md1>
    <div class="login">
      <h3>LOG IN</h3>
      <v-form>
        <v-text-field type="text" v-model="email" placeholder="Email"></v-text-field>
        <v-text-field type="password" v-model="password" placeholder="Password"></v-text-field>
      </v-form>
      <v-btn v-on:click="signIn">Connection</v-btn>
    </div>
  </v-flex>
</template>

<script>
import firebase from 'firebase'
import { mapGetters } from 'vuex'

export default {
  name: 'login',
  data: function () {
    return {
      email: '',
      password: ''
    }
  },
  computed: {
    ...mapGetters({user: 'getUser'})
  },
  methods: {
    signIn () {
      // login with firebase!
      firebase
        .auth()
        .signInWithEmailAndPassword(this.email, this.password)
        .then(
          (user) => {},
          (err) => {
            alert('Oops. ' + err.message)
          }
        )
    }
  },
  watch: {
    // On login success, move!
    user (user) {
      if (user) this.$router.replace('/')
    }
  }
}
</script>

<style scoped>
  .login {
    margin-top: 40px;
  }
  input {
    margin: 10px 0;
    width: 20%;
    padding: 15px;
  }
  button {
    margin-top: 20px;
    width: 10%;
    cursor: pointer;
  }
  p {
    margin-top: 40px;
    font-size: 13px;
  }
  p a {
    text-decoration: underline;
    cursor: pointer;
  }
</style>
```


```javascript
// getter.js
export default {
  // ...
  getUser: state => state.user
}
```

```javascript
// store.js
import Vue from 'vue'
import Vuex from 'vuex'
import getters from './getters'
import mutations from './mutations'
import firebase from 'firebase'

Vue.use(Vuex)

// Watching Auth state change
firebase.auth().onAuthStateChanged((user) => {
  if (user) {
    state.user = user
  } else {
    state.user = null
  }
})

const state = {
  // ...
  user: null
}

export default new Vuex.Store({
  state,
  getters
  // ...
})
```

This is working code's repository: https://github.com/LoveMeWithoutAll/book-blog

And working app: https://book-blog-with-largo.firebaseapp.com/

Anyway, it works!

[vuetify]: https://github.com/vuetifyjs/vuetify
[Firebase auth]: https://firebase.google.com/docs/auth/