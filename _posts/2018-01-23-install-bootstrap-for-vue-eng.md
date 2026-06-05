---
layout: single
title: Using Bootstrap and jQuery in Vue
date: 2018-01-23 17:07:30.000000000 +09:00
type: post
header:
  teaser: "/assets/images/bootstrap-vue.png"
  image: "/assets/images/bootstrap-vue.png"
categories:
- IT
tags: [Vue.js, Bootstrap, jQuery]
---

## Update! 2019-08-04

With [Bootstrap Vue] being updated to v2, [jQuery] is no longer needed!

Now you can just follow the steps described [here](https://bootstrap-vue.js.org/docs#using-module-bundlers).

But if even that feels like a hassle, I've made a template for you. Just clone [this repository](https://github.com/LoveMeWithoutAll/vue-bootstrap-template) and use it as is.

I'm leaving the content below for reference, so I won't delete it.

## The End

------------

## Start

### A Few Methods

To use [Bootstrap] and [jQuery] in [Vue.js], you need a bit of extra work. There are a few ways to do this.

1. Use the vue cli template provided by [Bootstrap Vue]
1. Use [expose-loader]: refer to [this article](http://vuejs.kr/jekyll/update/2017/03/02/vuejs-jquery-bootstrap/)
1. Use [webpack]

### Choice

I found the approach using [webpack] to be the cleanest. I referred to [this article](https://brendaniel.github.io/2017/11/17/Vue에서-jquery와-bootstrap-전역으로-사용하기/) and made a few tweaks. The structure is based on a project using the [webpack template](https://github.com/vuejs-templates/webpack) from [vue cli]. The versions of each library are as follows.

1. [Bootstrap] v4.0.0
1. [jQuery] v3.3.1
1. [popper.js] v1.12.9

## Installation

```bash
npm install --save jquery bootstrap popper.js
```

## Configuration

### webpack

```javascript
#build/webpack.base.conf.js

const webpack = require('webpack')

module.exports = {
  ...
  plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery',
      jquery: 'jquery',
      'window.jQuery': 'jquery',
      jQuery: 'jquery'
    })
  ]
  ...
}
```

### eslint

```javascript
#.enlintrs.js

module.exports = {
  ...
  globals: {
    "$": true,
    "jQuery": true
  }
}
```

## import

```javascript
#src/main.js

import 'bootstrap'
import 'bootstrap/dist/css/bootstrap.min.css'
```

Now let's build something beautiful!

[Vue.js]: https://vuejs.org
[jQuery]: https://jquery.com
[Bootstrap]: http://getbootstrap.com
[Bootstrap Vue]: https://bootstrap-vue.js.org
[expose-loader]: https://github.com/webpack-contrib/expose-loader
[webpack]: https://webpack.js.org
[vue cli]: https://github.com/vuejs/vue-cli
[popper.js]: https://popper.js.org
