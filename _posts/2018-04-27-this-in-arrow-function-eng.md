---
layout: single
title: this in Arrow Functions (feat. Vue.js)
date: 2018-04-25 01:00:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/arrow-function-with-vue.png"
    image: "/assets/images/arrow-function-with-vue.png"
categories:
- IT
tags: [Vue.js, es6]
---

I've been working on converting existing Vue.js code to es6. While doing so, I learned that you should not use arrow functions when defining methods. I've put together my notes below. [This article](https://gist.github.com/JacobBennett/7b32b4914311c0ac0f28a1fdc411b9a7) was a huge help.

First, let's look at the existing code.

```javascript
// existing code
<template>
  <div>
      <input type="text" v-model="newTodoItem">
      <button v-on:click="addTodo">add</button>
  </div>
</template>

<script>
export default {
    data: () => ({
        newTodoItem: ''
    }),
    // this part
    methods: {
        addTodo: function () {
            console.log(this.newTodoItem)
        }
    }
    // is what we'll convert to es6
}
</script>

<style scoped>

</style>
```

I don't like defining methods with `function`. Let's switch to arrow functions.

```javascript
methods: {
    addTodo: () => {
        console.log(this.newTodoItem) // this.newTodoItem === undefined
    }
}
```
It doesn't work. The console only prints *undefined*. This is because `this` in an arrow function is different from `this` in an es5 `function`. Arrow functions don't define a new `this`. They use the `this` of the function (or class) immediately surrounding the arrow function. It's a closure.

I tested whether there might be some way to grab the Vue instance after all.
```javascript
methods: {
    addTodo: () => {
        console.log(this.$data.newTodoItem) // this.$data throw error
    }
}
```
It still doesn't work. Even when trying to access it through `this.$data`, the `this` inside the arrow function does not point to the Vue instance. Since no separate `this` is created in the `methods` object directly above the arrow function, the only `this` it can see is way up in the `window` object, where it finally finds one. The `window` object can't possibly have a `$data` property, so an error occurs.

So let's give up on using arrow functions when defining methods. Instead, there's another way.
```javascript
methods: {
    addTodo () {
        console.log(this.newTodoItem) // it works!
    }
}
```
It works! I used the es6 object literal property shorthand feature. This approach is cleaner and nicer than using the function syntax.

That said, this doesn't mean you should never use arrow functions in methods at all. When you have a function inside a function, arrow functions are useful.
```javascript
// not use strict
methods: {
    addTodo () {
        let self = this // the code I always type, but it's annoying
        function A () {
            console.log(self.newTodoItem)
        }
        function B () {
            console.log(self.newTodoItem)
        }
    }
}
```
Using arrow functions lets you get rid of the annoying *let self = this*.
```javascript
methods: {
    addTodo () {
        let A = () => {
            console.log(this.newTodoItem)
        }
        let B = () => {
            console.log(this.newTodoItem)
        }
        A() // comes out fine
    }
}
```
This is because the `this` of arrow functions A and B refers to the `this` of the `addTodo` function.

## Wrapping up
1. Don't use arrow functions when defining an object's methods.
1. When defining methods, use the object property shorthand feature.
1. The `this` of an arrow function is different from the `this` of a regular function, so be careful (and that's not the only difference. Arrow functions can't be constructors, among other things...)
