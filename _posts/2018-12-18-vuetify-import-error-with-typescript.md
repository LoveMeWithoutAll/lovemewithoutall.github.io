---
layout: single
title: typescript에서 Vuetify import 오류 해결
date: 2018-12-18 09:05:30.000000000 +09:00
type: post
header:
    teaser: "https://cdn.vuetifyjs.com/images/logos/v-alt.svg"
    image: "https://cdn.vuetifyjs.com/images/logos/v-alt.svg"
categories:
- IT
tags: [Typescript, Vuetify, Vue]
---

## 환경

* OS: Windows10
* Vue.js: 2.5.21
* Vuetify: 1.3.14
* typescript: 3.2.2
* tslint: 5.11.0

## 현상

`[vue-cli]`에서 typescript를 선택하고 프로젝트를 시작한 후, `[Vuetify]`를 Add하여 build하면 아래와 같은 오류가 뜬다.

```javascript
ERROR in C:/workspace/inhasite/general/ctlt_apply/english_clinic/src/plugins/vuetify.ts
2:21 Could not find a declaration file for module 'vuetify/lib'. 'C:/workspace/inhasite/general/ctlt_apply/english_clinic/node_modules/vuetify/lib/index.js' implicitly has an 'any' type.
  Try `npm install @types/vuetify` if it exists or add a new declaration (.d.ts) file containing `declare module 'vuetify/lib';`
    1 | import Vue from 'vue';
  > 2 | import Vuetify from 'vuetify/lib';
      |                     ^
    3 | import 'vuetify/src/stylus/app.styl';
    4 |
    5 | Vue.use(Vuetify, {
Version: typescript 3.2.2, tslint 5.11.0
```

## 해결책

`tsconfig.json` 파일에 다음 옵션을 추가해준다.

```json
// tsconfig.json
"compilerOptions": {
    "types": ["vuetify"],
```

### 참고: https://github.com/vuetifyjs/vue-cli-plugin-vuetify/issues/43

[vue-cli]: https://github.com/vuejs/vue-cli/blob/dev/docs/README.md
[Vuetify]: https://github.com/vuetifyjs/vuetify
