---
layout: single
title: Finding a DOM Buried Deep Inside a Vue Component
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

## Situation

I want to find a DOM element located deep inside a Vue component.

## Approach

You can also use `querySelector` on a `ref`.

```typescript
<template>
  <NumberInput ref="inputRef" /> // a component that contains an input tag
</template>

<script setup lang="ts">
import { ref } from 'vue';

const inputRef = ref<HTMLInputElement | null>(null);

const getInputTag = () => inputRef.value.$el.querySelector('input'); // use it exactly like the DOM API's querySelector
</script>
```

## Application

Let's set focus on the input tag inside the component.

```typescript
<template>
  <NumberInput ref="inputRef" /> // a component that contains an input tag
</template>

<script setup lang="ts">
import { nextTick, ref } from 'vue';

const inputRef = ref<HTMLInputElement | null>(null);

const getInputTag = () => inputRef.value.$el.querySelector('input'); // use it exactly like the DOM API's querySelector

const focusOnInput = () => {
  getInputTag().focus(); // put focus on the input tag
}

nextTick(() => focusOnInput()); // set focus after rendering is done
</script>
```

----

Afterthought: I just want to use React.
