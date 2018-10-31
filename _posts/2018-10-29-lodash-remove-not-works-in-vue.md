---
layout: single
title: Vue.js에서 lodash remove 오작동
date: 2018-10-31 13:03:30.000000000 +09:00
type: post
header:
    teaser: "https://lodash.com/assets/img/lodash.svg"
    image: "https://lodash.com/assets/img/lodash.svg"
categories:
- IT
tags: [javascript, Vue.js, lodash]
---

[Vue.js]에서 [lodash]의 [remove 함수](https://lodash.com/docs/4.17.10#remove)가 제대로 동작하지 않는다. 보다 정확히 말하자면, [Vue.js]에서 watching 중인 reactive array에서 element를 `_.remove(array, fn)` 해도, [Vue.js]는 이를 감지하지 못한다.

원인은 [Vue.js]와 [lodash]가 `splice`를 `call`하는 방식의 차이 때문이다.

Evan You의 [말씀](https://github.com/vuejs/vue/issues/2673#issuecomment-210565099)이다.

> As a result - I don't think Vue can guarantee reactivity when you use a "blackbox" 3rd party lib. The recommended approach: when using lodash, always use immutable methods that returns a copy. So instead of _.remove, use _.filter and replace the original array.

그러니 이렇게 바꾸자. 나는 `_.filter` 대신 `_.findIndex`를 썼다.


```javascript
/// not nicely working code
function noReactivelyWorkFn (name) {
  _.remove(state.requestsForAdmin, o => o.name === name)
}

// working code
function reactivelyWorkFn (name) {
  const idx = _.findIndex(this.requestsForAdmin, o => o.name === name)
  state.requestsForAdmin.splice(idx, 1)
}
```

[Vuex]의 문제도, [Vuetify]의 문제도 아니니 너무 당황하지 말자!

실행 버전은 다음과 같다. 

* [Vue.js] v2.5.17
* [lodash] v4.17.11

참고: https://github.com/vuejs/vue/issues/2673

[Vue.js]: https://vuejs.org/
[Lodash]: https://lodash.com/
[Vuex]: https://vuex.vuejs.org/kr/
[vuetify]: https://github.com/vuetifyjs/vuetify
