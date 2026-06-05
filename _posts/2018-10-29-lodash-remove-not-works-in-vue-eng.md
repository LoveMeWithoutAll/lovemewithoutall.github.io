---
layout: single
title: lodash remove malfunctions in Vue.js
date: 2018-10-31 13:03:30.000000000 +09:00
type: post
header:
    teaser: "https://lodash.com/assets/img/lodash.svg"
    image: "https://lodash.com/assets/img/lodash.svg"
categories:
- IT
tags: [javascript, Vue.js, lodash]
---

In [Vue.js], [lodash]'s [remove function](https://lodash.com/docs/4.17.10#remove) doesn't work properly. To be more precise, when you remove an element from a reactive array that [Vue.js] is watching with `_.remove(array, fn)`, [Vue.js] fails to detect it.

The cause is the difference in how [Vue.js] and [lodash] `call` `splice`.

Evan You [said this](https://github.com/vuejs/vue/issues/2673#issuecomment-210565099).

> As a result - I don't think Vue can guarantee reactivity when you use a "blackbox" 3rd party lib. The recommended approach: when using lodash, always use immutable methods that returns a copy. So instead of _.remove, use _.filter and replace the original array.

So let's change it like this. Instead of `_.filter`, I used `_.findIndex`.


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

It's not a problem with [Vuex], nor with [Vuetify], so don't panic too much!

The versions used were as follows.

* [Vue.js] v2.5.17
* [lodash] v4.17.11

Reference: https://github.com/vuejs/vue/issues/2673

[Vue.js]: https://vuejs.org/
[Lodash]: https://lodash.com/
[Vuex]: https://vuex.vuejs.org/kr/
[vuetify]: https://github.com/vuetifyjs/vuetify
