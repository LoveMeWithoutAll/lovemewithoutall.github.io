---
layout: single
title: Vuetify에서 tslint 설정
date: 2018-12-19 09:05:30.000000000 +09:00
type: post
header:
    teaser: "https://ih0.redbubble.net/image.269279748.8219/st%2Csmall%2C215x235-pad%2C210x230%2Cf8f8f8.lite-1.jpg"
    image: "https://ih0.redbubble.net/image.269279748.8219/st%2Csmall%2C215x235-pad%2C210x230%2Cf8f8f8.lite-1.jpg"
categories:
- IT
tags: [Typescript, tslint, Vuetify, Vue]
---

처음 [Vuetify]를 Add하고 프로젝트를 빌드하면 `warning`이 많이 뜬다.

```javascript
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/App.vue
25:49 Unnecessary semicolon
    23 |
    24 | <script>
  > 25 | import HelloWorld from './components/HelloWorld';
       |                                                 ^
    26 |
    27 | export default {
    28 |   name: 'App',
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/components/HelloWorld.vue
102:8 Missing trailing comma
    100 |           text: 'awesome-vuetify',
    101 |           href: 'https://github.com/vuetifyjs/awesome-vuetify'
  > 102 |         }
        |        ^
    103 |       ],
    104 |       importantLinks: [
    105 |         {
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/components/HelloWorld.vue
124:8 Missing trailing comma
    122 |           text: 'Articles',
    123 |           href: 'https://medium.com/vuetify'
  > 124 |         }
        |        ^
    125 |       ],
    126 |       whatsNext: [
    127 |         {
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/components/HelloWorld.vue
138:8 Missing trailing comma
    136 |           text: 'Frequently Asked Questions',
    137 |           href: 'https://vuetifyjs.com/getting-started/frequently-asked-questions'
  > 138 |         }
        |        ^
    139 |
    140 |       ]
    141 |     })
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/components/HelloWorld.vue
142:2 Unnecessary semicolon
    140 |       ]
    141 |     })
  > 142 |   };
        |  ^
    143 | </script>
    144 |
    145 | <style>
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/main.ts
1:22 Unnecessary semicolon
  > 1 | import Vue from 'vue';
      |                      ^
    2 | import './plugins/vuetify';
    3 | import App from './App.vue';
    4 | import router from './router';
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/main.ts
2:27 Unnecessary semicolon
    1 | import Vue from 'vue';
  > 2 | import './plugins/vuetify';
      |                           ^
    3 | import App from './App.vue';
    4 | import router from './router';
    5 | import store from './store';
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/main.ts
3:28 Unnecessary semicolon
    1 | import Vue from 'vue';
    2 | import './plugins/vuetify';
  > 3 | import App from './App.vue';
      |                            ^
    4 | import router from './router';
    5 | import store from './store';
    6 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/main.ts
4:30 Unnecessary semicolon
    2 | import './plugins/vuetify';
    3 | import App from './App.vue';
  > 4 | import router from './router';
      |                              ^
    5 | import store from './store';
    6 |
    7 | Vue.config.productionTip = false;
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/main.ts
5:28 Unnecessary semicolon
    3 | import App from './App.vue';
    4 | import router from './router';
  > 5 | import store from './store';
      |                            ^
    6 |
    7 | Vue.config.productionTip = false;
    8 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/main.ts
7:33 Unnecessary semicolon
     5 | import store from './store';
     6 |
  >  7 | Vue.config.productionTip = false;
       |                                 ^
     8 |
     9 | new Vue({
    10 |   router,
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/main.ts
13:18 Unnecessary semicolon
    11 |   store,
    12 |   render: (h) => h(App),
  > 13 | }).$mount('#app');
       |                  ^
    14 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/plugins/vuetify.ts
1:22 Unnecessary semicolon
  > 1 | import Vue from 'vue';
      |                      ^
    2 | import Vuetify from 'vuetify/lib';
    3 | import 'vuetify/src/stylus/app.styl';
    4 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/plugins/vuetify.ts
2:34 Unnecessary semicolon
    1 | import Vue from 'vue';
  > 2 | import Vuetify from 'vuetify/lib';
      |                                  ^
    3 | import 'vuetify/src/stylus/app.styl';
    4 |
    5 | Vue.use(Vuetify, {
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/plugins/vuetify.ts
3:37 Unnecessary semicolon
    1 | import Vue from 'vue';
    2 | import Vuetify from 'vuetify/lib';
  > 3 | import 'vuetify/src/stylus/app.styl';
      |                                     ^
    4 |
    5 | Vue.use(Vuetify, {
    6 |   iconfont: 'md',
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/plugins/vuetify.ts
7:3 Unnecessary semicolon
    5 | Vue.use(Vuetify, {
    6 |   iconfont: 'md',
  > 7 | });
      |   ^
    8 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/router.ts
1:22 Unnecessary semicolon
  > 1 | import Vue from 'vue';
      |                      ^
    2 | import Router from 'vue-router';
    3 | import Home from './views/Home.vue';
    4 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/router.ts
2:32 Unnecessary semicolon
    1 | import Vue from 'vue';
  > 2 | import Router from 'vue-router';
      |                                ^
    3 | import Home from './views/Home.vue';
    4 |
    5 | Vue.use(Router);
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/router.ts
3:36 Unnecessary semicolon
    1 | import Vue from 'vue';
    2 | import Router from 'vue-router';
  > 3 | import Home from './views/Home.vue';
      |                                    ^
    4 |
    5 | Vue.use(Router);
    6 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/router.ts
5:16 Unnecessary semicolon
    3 | import Home from './views/Home.vue';
    4 |
  > 5 | Vue.use(Router);
      |                ^
    6 |
    7 | export default new Router({
    8 |   mode: 'history',
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/router.ts
25:3 Unnecessary semicolon
    23 |     },
    24 |   ],
  > 25 | });
       |   ^
    26 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/store.ts
1:22 Unnecessary semicolon
  > 1 | import Vue from 'vue';
      |                      ^
    2 | import Vuex from 'vuex';
    3 |
    4 | Vue.use(Vuex);
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/store.ts
2:24 Unnecessary semicolon
    1 | import Vue from 'vue';
  > 2 | import Vuex from 'vuex';
      |                        ^
    3 |
    4 | Vue.use(Vuex);
    5 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/store.ts
4:14 Unnecessary semicolon
    2 | import Vuex from 'vuex';
    3 |
  > 4 | Vue.use(Vuex);
      |              ^
    5 |
    6 | export default new Vuex.Store({
    7 |   state: {
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/store.ts
16:3 Unnecessary semicolon
    14 |
    15 |   },
  > 16 | });
       |   ^
    17 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/views/Home.vue
9:56 Unnecessary semicolon
     7 |
     8 | <script lang="ts">
  >  9 | import { Component, Vue } from 'vue-property-decorator';
       |                                                        ^
    10 | import HelloWorld from '@/components/HelloWorld.vue'; // @ is an alias to /src
    11 |
    12 | @Component({
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/views/Home.vue
10:53 Unnecessary semicolon
     8 | <script lang="ts">
     9 | import { Component, Vue } from 'vue-property-decorator';
  > 10 | import HelloWorld from '@/components/HelloWorld.vue'; // @ is an alias to /src
       |                                                     ^
    11 |
    12 | @Component({
    13 |   components: {
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/tests/unit/example.spec.ts
1:47 Unnecessary semicolon
  > 1 | import { shallowMount } from '@vue/test-utils';
      |                                               ^
    2 | import HelloWorld from '@/components/HelloWorld.vue';
    3 |
    4 | describe('HelloWorld.vue', () => {
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/tests/unit/example.spec.ts
2:53 Unnecessary semicolon
    1 | import { shallowMount } from '@vue/test-utils';
  > 2 | import HelloWorld from '@/components/HelloWorld.vue';
      |                                                     ^
    3 |
    4 | describe('HelloWorld.vue', () => {
    5 |   it('renders props.msg when passed', () => {
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/tests/unit/example.spec.ts
6:30 Unnecessary semicolon
    4 | describe('HelloWorld.vue', () => {
    5 |   it('renders props.msg when passed', () => {
  > 6 |     const msg = 'new message';
      |                              ^
    7 |     const wrapper = shallowMount(HelloWorld, {
    8 |       propsData: { msg },
    9 |     });
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/tests/unit/example.spec.ts
9:7 Unnecessary semicolon
     7 |     const wrapper = shallowMount(HelloWorld, {
     8 |       propsData: { msg },
  >  9 |     });
       |       ^
    10 |     expect(wrapper.text()).toMatch(msg);
    11 |   });
    12 | });
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/tests/unit/example.spec.ts
10:40 Unnecessary semicolon
     8 |       propsData: { msg },
     9 |     });
  > 10 |     expect(wrapper.text()).toMatch(msg);
       |                                        ^
    11 |   });
    12 | });
    13 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/tests/unit/example.spec.ts
11:5 Unnecessary semicolon
     9 |     });
    10 |     expect(wrapper.text()).toMatch(msg);
  > 11 |   });
       |     ^
    12 | });
    13 |
WARNING in C:/workspace/inhasite/general/ctlt_apply/english_clinic/tests/unit/example.spec.ts
12:3 Unnecessary semicolon
    10 |     expect(wrapper.text()).toMatch(msg);
    11 |   });
  > 12 | });
       |   ^
    13 |
No type errors found
```

보기에 좋지 않으니 안뜨게 해보자.

## 환경

* OS: Windows10
* Vue.js: 2.5.21
* Vuetify: 1.3.14
* typescript: 3.2.2
* tslint: 5.11.0

## 방법

`[tslint]`의 `rules`를 설정해주면 된다.

`tslint.json` 파일에 다음 설정을 추가해주자.

```json
// tslint.json
// ...
"rules": {
    // ...
    "trailing-comma": [
      true,
      {
        "multiline": {
          "objects": "ignore",
          "arrays": "always",
          "functions": "never",
          "typeLiterals": "ignore"
        },
        "esSpecCompliant": true
      }
    ],
    "space-before-function-paren": [
      true,
      {
        "anonymous": "always",
        "named": "never",
        "asyncArrow": "always"
      }
    ],
    "semicolon": [
      true,
      "never"
    ]
}
```

## 마무리

물론 `tslint` 설정을 꼭 이렇게 해줄 필요는 없다. 각자 취향껏(?) 해도 좋다.

`npm run lint --fix` 를 해주는 것도 잊지말자.

## 끝!

[vue-cli]: https://github.com/vuejs/vue-cli/blob/dev/docs/README.md
[Vuetify]: https://github.com/vuetifyjs/vuetify
[tslint]: https://palantir.github.io/tslint/
