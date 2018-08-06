---
layout: single
title: Vuex two way binding
date: 2018-08-03 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://vuex.vuejs.org/vuex.png"
    image: "https://vuex.vuejs.org/vuex.png"
categories:
- IT
tags: [Vue.js, JavaScript]
---

[Vuex] is very useful library for [Vue.js]. However... when handling form like input tag with [Vuex], setting up two way binding is somehow complicated and you need a job something to do. Without the job, you would encounter an error message on console like below.

```javascript
Computed property “name” was assigned to but it has no setter
```

Of coures, [Vuex Guide document](https://vuex.vuejs.org/guide/forms.html#two-way-computed-property) is a good solution.

But I want to use [mapGetters](https://vuex.vuejs.org/guide/getters.html#the-mapgetters-helper) and [mapMutations](https://vuex.vuejs.org/guide/getters.html#the-mapgetters-helper) with [Vuex]. Without **$store**, code is more beautiful. Example is below. Vuex files are ommited.

```html
<template>
  <div>
    <input v-model="something" />
  </div>
</template>

<script>
import { mapMutations, mapGetters } from 'vuex'
import { UPATE_SOMETHING } from '../vuex/mutation_type'

export default {
  methods: {
    ...mapMutations({updateSomething: UPATE_SOMETHING})
  },
  computed: {
    ...mapGetters(['getSomething']),
    something: {
      get () {
        return this.getSomething
      },
      set (value) {
        this.updateSomething(value)
      }
    }
  }
}
</script>
```

If you want more, [vuex-map-fields] is helpful. Especially, [Multi-row fields](https://github.com/maoberlehner/vuex-map-fields#multi-row-fields) may save your life.

[Vuex]: https://vuex.vuejs.org/kr/
[Vue.js]: https://vuejs.org/
[vuex-map-fields]: https://github.com/maoberlehner/vuex-map-fields