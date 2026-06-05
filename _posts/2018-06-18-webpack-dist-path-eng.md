---
layout: single
title: Setting the output path for webpack builds
date: 2018-06-18 19:50:30.000000000 +09:00
type: post
header:
    teaser: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
    image: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
categories:
- IT
tags: [Javascript, webpack]
---

This post is based on webpack version 4.11.1.

When you build with webpack, the deployment artifacts are generated under the `dist` directory. If you're going to place these artifacts at the project root, there's no problem at all. But when you're working on only some modules of a large project separately, you need to tweak the webpack configuration a bit. You need to configure the path of the static files referenced by the `index.html` produced as the build output. This is because webpack's default configuration sets the path of static files to be under the project's root path. You can change it as shown below.

```javascript
// config/index.js
'use strict'
// Template version: 1.3.1
// see http://vuejs-templates.github.io/webpack for documentation.

const path = require('path')

module.exports = {
    // ...
    build: {
        // ...
        // Just change assetsPublicPath to the path you want. The default value is /
        assetsPublicPath: '/survey/dist/', // for example, like this...
        // ...
    }
    // ...
}
```

(For the [Vue CLI] webpack configuration, please refer to [this post](https://lovemewithoutall.github.io/it/vue-cli-3-webpack/))

The end!

[Vue CLI]: https://cli.vuejs.org/
