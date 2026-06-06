---
layout: single
title: "Vue.js Project Structure - The Difference Between views and components"
date: 2018-12-14 17:05:30.000000000 +09:00
type: post
header:
    teaser: "https://kr.vuejs.org/images/logo.png"
    image: "https://kr.vuejs.org/images/logo.png"
categories:
- IT
tags: [Vue]
---

I was about to explain this to someone, but suddenly my mind went blank and I got flustered. So I decided to write it down.

When you start a project with [vue-cli], there are some confusing folders. The `views` folder and the `components` folder are the ones I'm talking about. Both contain Vue component files in the `aaa.vue` format. So what's the difference between the `views` folder and the `components` folder? The answer is simple.

## Put the component files that are displayed by the `router` in the `views` folder. Everything else goes in the `components` folder.

Of course, you can structure the folders however you like. The distinction above is just an example suggested by [vue-cli]. Do whatever works best for you!

* Reference: https://stackoverflow.com/questions/50865828/what-is-the-difference-between-the-views-and-components-folders-in-a-vue-project

[vue-cli]: https://github.com/vuejs/vue-cli/blob/dev/docs/README.md
