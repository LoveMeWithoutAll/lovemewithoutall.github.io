---
layout: single
title: "Error: Cannot find module '@vue/babel-preset-application'"
date: 2020-10-19 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://cli.vuejs.org/favicon.png"
    image: "https://cli.vuejs.org/favicon.png"
categories:
- IT
tags: [JavaScript, Vue.js]
---

After Upgrading `vue-cli` from v3 to v4, you got an error message like below on typing any npm script.

```bash
 ERROR  Error: Cannot find module '@vue/babel-preset-application' 
```

The message occurred because of babel setting. So you must fix `babel.config.js` like below.

```js
// from vue-cli v3
module.exports = {
  presets: ['@vue/application'],
};

// to vue-cli v4
module.exports = {
  presets: ['@vue/cli-plugin-babel/preset'],
};
```

[This github pull request](https://github.com/aws-kr-tnc/moving-to-serverless-renew/pull/115) could be refered. That's all. Good luck.