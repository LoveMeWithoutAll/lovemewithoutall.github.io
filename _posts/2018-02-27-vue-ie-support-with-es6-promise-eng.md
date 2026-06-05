---
layout: single
title: Using Vue.js in IE
date: 2018-02-27 08:07:30.000000000 +09:00
type: post
header:
  teaser: "/assets/images/old_ie.jpg"
  image: "/assets/images/old_ie.jpg"
categories:
- IT
tags: [Vue.js, IE, axios, es6-promise]
---

# 1. 'Promise' is undefined.

## The error
When making ajax requests in [Vue.js], you'll usually reach for [axios]. It's a convenient, well-made library, but when you run it on older versions of IE you'll see an error like this.

```javascript
ReferenceError: 'Promise'이(가) 정의되지 않았습니다.
   {
      [functions]: ,
      description: "'Promise'이(가) 정의되지 않았습니다.",
      message: "'Promise'이(가) 정의되지 않았습니다.",
      name: "ReferenceError",
      number: -2146823279
   }
```
This happens because [axios] doesn't support IE. The same goes for [Vuex].

## The solution
You just need to use [es6-promise](https://github.com/stefanpenner/es6-promise).

```html
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/es6-promise@4/dist/es6-promise.min.js"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/es6-promise@4/dist/es6-promise.auto.min.js"></script> 
```

Once you add the scripts above, asynchronous http communication using [axios] now works fine even in IE.

If you're using [webpack], you can do the following.

First install it,

```bash
# install
npm install es6-promise --save
```

then add the following to the javascript file you use as the entry, and it will be applied to the global environment.
You don't need to import or require it separately in other files. Pick one of the two and add it.

```javascript
// CommonJs style
require('es6-promise').polyfill();
```

```javascript
// ES6 style
import 'es6-promise/auto'
```

If you still get the *'Promise' is undefined.* error even after this, load [es6-promise] earlier. It needs to be loaded at least before the point where you use [Vuex] or [axios] for it to work correctly.

# 2. Symbol is undefined

## The error

When you set up a project with `es6` and `typescript`, you may run into an error like this.

```javascript
SCRIPT5009: 'Symbol'이(가) 정의되지 않았습니다.
lang.js (12,1)
```

This is because [IE doesn't support](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Symbol) [`symbol`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Symbol).

## The solution

Apply `babel-polyfill`.

First add it to your project,

```bash
# install
npm install --save babel-polyfill
```

then add the following to the javascript file you use as the entry. In the code below it's imported using ES6 syntax. Just like with `es6-promise`, you need to add it at the very top.

```javascript
// main.js or index.js or main.ts
import 'babel-polyfill' // <== added
import Vue from 'vue'

// ...

new Vue({
   // ...
})
```

[Vue.js]: https://vuejs.org
[axios]: https://github.com/axios/axios
[webpack]: https://webpack.js.org/
[Vuex]: https://vuex.vuejs.org/kr/
