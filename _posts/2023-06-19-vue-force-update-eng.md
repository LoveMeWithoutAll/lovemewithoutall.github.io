---
layout: single
title: "What if the model value of a Vue3 input doesn't match what's shown on screen?"
date: 2023-06-19 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
categories:
- IT
tags: [Vue]
---

## Environment
* Vue 2.7
* Using the `<script setup>` syntax

## The Problem
I built an ordinary `select` box that receives the selected value through `v-model`. I wanted to implement business logic where selecting a certain `option` triggers a validation function, which displays a message and prevents that `option` from being selected.

But a problem arose. The validation function worked fine, and it blocked the change to v-model... yet on screen, the `option` I had just selected was still showing as selected. Since v-model wasn't changed, shouldn't the option value remain the one it was before the change?

When I looked inside the `Vue` instance, the v-model value had been changed exactly as I intended. But the select box bound to v-model was displaying the wrong value.

## The Cause
* One-line summary: because v-model didn't change, no rerender happened.

1. A `select` tag's value is a value provided by the HTML DOM API.
1. When you set an input's value (here, the selected value) via Vue's v-model, that is a value inside the Vue instance.
1. Vue rerenders when a value inside the Vue instance changes.
1. The HTML DOM API's input value had already changed the moment the user selected it.
1. But since the value inside the Vue instance didn't change, no rerender happened.
1. The input value set by Vue's v-model and the input value from the DOM API ended up out of sync.

## The Solution
There are several approaches, but changing the `key` is the cleanest way.

```javascript
<template>
  <MyComponent :key="componentKey" />
</template>
<script setup>
import { ref } from 'vue';
const componentKey = ref(0);

const forceRerender = () => {
  componentKey.value += 1;
};
</script>
```

Don't waste your time attempting weird hacks like `$forceUpdate`.

## References
* [Vue select value not changing?](https://stackoverflow.com/questions/69279746/vue-3-binding-problem-with-select-if-option-selected-is-not-showing-in-the-ne)
* [Vue3 official docs: forceUpdate](https://vuejs.org/api/component-instance.html#forceupdate)
* [The inner workings of Vue's Composition API & why getInstance returns null](https://4ark.me/post/87ba8d8b.html)
* [Various ways to force Vue to rerender](https://michaelnthiessen.com/force-re-render/)
