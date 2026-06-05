---
layout: single
title: Changing the Vue CLI Package Manager
date: 2019-05-24 18:07:30.000000000 +09:00
type: post
header:
    teaser: "https://cli.vuejs.org/favicon.png"
    image: "https://cli.vuejs.org/favicon.png"
categories:
- IT
tags: [Vue.js, JavaScript]
---

While using the [Vue CLI], you may run into an error like this.

```
error 404 Not Found - GET https://cdn.npm.taobao.org/electron-to-chromium/-/electron-to-chromium-1.3.137.tgz
error 404
error 404 'electron-to-chromium@^1.3.133' is not in the npm registry.
error 404 You should bug the author to publish it (or use the name yourself!)
```

This error occurs because the [Taobao Registry], a mirror of NPM, is broken. (Since NPM's registry is dreadfully slow, people usually use the [Taobao Registry].)

There's no time to wait for the [Taobao Registry] to recover, so let's change the package manager used by the [Vue CLI]. If you're using NPM, switch to [yarn], or vice versa.

First, let's check the [Vue CLI] configuration file. If you enter the command below in your console,

```cmd
vue config
```

it prints out something like the following. The Resolved path is the path to the [Vue CLI] configuration file. You just need to edit the **packageManager** entry in that file. In the example below, *npm* was changed to *yarn*.

```javascript
// The OS here is Windows 10
Resolved path: C:\Users\ys\.vuerc
 {
  "useTaobaoRegistry": true,
  "packageManager": "yarn", // edit this part
  "presets": {
    "client": {
      "useConfigFiles": false,
      "plugins": {
        "@vue/cli-plugin-babel": {},
        "@vue/cli-plugin-eslint": {
          "config": "standard",
          "lintOn": [
            "save"
          ]
        }
      },
      "router": true,
      "vuex": true
    }
  }
}
```

Done!

[Vue CLI]: https://cli.vuejs.org/
[Taobao Registry]: https://npm.taobao.org/
[yarn]: https://yarnpkg.com/lang/en/
