---
layout: single
title: Using a bundled file from another project with webpack
date: 2021-11-23 01:17:30.000000000 +09:00
type: post
header:
    teaser: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
    image: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
categories:
- IT
tags: [webpack]
---

Can you take an output file bundled in another project and use it in a different frontend framework such as React? Of course, the whole point of modularization is to make this possible, but every now and then you need to work alongside legacy code. If you use [craco](https://github.com/gsoft-inc/craco), it's simple.

Configure `craco.config.js` as follows.

```javascript
module.exports = {
  webpack: {
    module: {
      rules: [
        {
          test: /\host.three.js$/, // specify the js file to import in test, and
          use: 'bundle-loader' // set the rule to use bundle-loader
        }
      ]
    }
  }
}
```

Import it in your JavaScript as follows.

```javascript
import HOST from '../../host.three'
```

Now you can use the bundled js file.

20211024
