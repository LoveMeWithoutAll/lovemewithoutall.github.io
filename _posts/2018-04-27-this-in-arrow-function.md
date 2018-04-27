---
layout: single
title: 화살표 함수의 this(feat. Vue.js)
date: 2018-04-25 01:00:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/arrow-function-with-vue.png"
    image: "/assets/images/arrow-function-with-vue.png"
categories:
- IT
tags: [Vue.js, es6]
---

Vue.js로 만든 기존 코드를 es6로 바꾸는 작업을 하고 있다. 하다보니 methods를 정의할 때, 화살표 함수를 사용하면 안된다는 사실을 알게됐다. 아래와 같이 정리해 보았다. [이 글](https://gist.github.com/JacobBennett/7b32b4914311c0ac0f28a1fdc411b9a7)로부터 큰 도움을 받았다.

우선 기존 코드를 보자.

```javascript
// 기존 코드
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
    // 이 부분을
    methods: {
        addTodo: function () {
            console.log(this.newTodoItem)
        }
    }
    // es6로 바꿀 것이다
}
</script>

<style scoped>

</style>
```

methods를 function으로 정의하는게 보기 싫다. arrow function으로 바꿔보자.

```javascript
methods: {
    addTodo: () => {
        console.log(this.newTodoItem) // this.newTodoItem === undefined
    }
}
```
안된다. 콘솔에 *undefined*만 찍힌다. 화살표 함수의 this는 기존 es5의 function에서의 this와는 다르기 때문이다. 화살표 함수에서는 this를 새로 정의하지 않는다. 화살표 함수 바로 바깥의 함수(혹은 class)의 this를 사용한다. 클로저다. 

혹시 어떻게든 Vue 인스턴스를 잡을 수 있지 않을까 테스트를 해보았다.
```javascript
methods: {
    addTodo: () => {
        console.log(this.$data.newTodoItem) // this.$data throw error
    }
}
```
여전히 안된다. this.$data를 통해 접근하려고 해도, 화살표 함수 속의 this는 Vue 인스턴스를 가리키지 않는다. 화살표 함수 바로 위에 있는 methods 객체에서 this가 따로 만들어져 있지 않기에, 바라볼 수 있는 this는 저 멀리 날아가 window 객체에서야 찾을 수 있기 때문이다. window 객체에는 $data 프로퍼티가 있을리 없기에 에러가 발생한다.

그러니 화살표 함수를 methods 정의할 때 쓰는건 포기하자. 대신 다른 방법이 있다.
```javascript
methods: {
    addTodo () {
        console.log(this.newTodoItem) // 된다!
    }
}
```
된다! es6의 객체 리터럴 프로퍼티 축약 기능을 사용했다. function syntax를 쓰는 것보다는 이 방법을 사용하는 편이 깔끔하고 좋다.

하지만 methods에서 화살표 함수를 아예 쓰지 말라는 것은 아니다. 함수 안에 함수가 들어갈 경우에는 화살표 함수가 유용하다.
```javascript
// use strict 아님
methods: {
    addTodo () {
        let self = this // 늘 치던 코드지만 귀찮다
        function A () {
            console.log(self.newTodoItem)
        }
        function B () {
            console.log(self.newTodoItem)
        }
    }
}
```
화살표 함수를 사용하면 귀찮은 *let self = this*를 없앨 수 있다.
```javascript
methods: {
    addTodo () {
        let A = () => {
            console.log(this.newTodoItem)
        }
        let B = () => {
            console.log(this.newTodoItem)
        }
        A() // 잘 나옴
    }
}
```
화살표 함수 A와 B의 this는 addTodo 함수의 this를 바라보기 때문이다.

## 마무리
1. 객체의 method를 정의할 때는 화살표 함수를 쓰면 안된다.
1. 메소드 정의할 때는 객체 프로퍼티 축약 기능을 쓰자.
1. 화살표 함수의 this는 일반 함수의 this와 다르니 주의하라(이것만 다른게 아니다. 화살표 함수는 생성자가 못되는 등...)