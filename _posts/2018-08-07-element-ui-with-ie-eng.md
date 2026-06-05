---
layout: single
title: Using element-ui in Internet Explorer (Korean Input Bug)
date: 2018-08-07 16:45:30.000000000 +09:00
type: post
header:
  teaser: "https://camo.githubusercontent.com/462f24153b8e8739c8ea71f7102585c4cb0e1575/68747470733a2f2f63646e2e7261776769742e636f6d2f456c656d6546452f656c656d656e742f6465762f656c656d656e745f6c6f676f2e737667"
  image: "https://camo.githubusercontent.com/462f24153b8e8739c8ea71f7102585c4cb0e1575/68747470733a2f2f63646e2e7261776769742e636f6d2f456c656d6546452f656c656d656e742f6465762f656c656d656e745f6c6f676f2e737667"
categories:
  - IT
tags: [Vue.js, JavaScript]
---

## UPDATE: Bug confirmed (v2.9.1)

## UPDATE: Bug confirmed (v2.5.4)

The bug where Korean input does not work properly has resurfaced ([issue link](https://github.com/ElemeFE/element/issues/11665#issuecomment-465379995)).
I have not been able to verify whether the solution below resolves it. ~~Apparently v2.5.2 is fine ([issue link](https://github.com/ElemeFE/element/issues/11665#issuecomment-463073670)), so if you really want to use [element-ui], try this version.~~

## The Problem

As of v2.9.1, when you use [element-ui] in IE (Internet Explorer) 11, you get the error shown below. Korean input does not work well.

![korean-input-error](https://user-images.githubusercontent.com/8110371/41639651-a22c6916-7499-11e8-93d8-cebf80eafb79.gif)

In [Google Chrome] it works fine, naturally (?).

![normal-case](https://user-images.githubusercontent.com/8110371/41639648-9f2073e8-7499-11e8-95a8-3ab6c1397107.gif)

## The Cause

[element-ui] is built primarily for Chinese-speaking users. The default language is even set to Chinese. Because of this, the form's input box is also designed with Chinese input in mind first, and there is logic that waits until a single character being typed into the input box is fully composed. This is what malfunctions in IE.

## The Solution

You can refer to [this](https://github.com/jhlee8804/element/commit/0c1d0b3d66c30d3182534a20ea706c951424e3a7?diff=unified). ~~Just delete all the code related to **valueBeforeComposition**.~~ I recommend method 2.

### ~~1. Edit node_modules directly~~

~~You can fix this by modifying the files below in your project's node_modules directory.~~

~~- /node_modules/element-ui/lib/input.js~~

~~- /node_modules/element-ui/lib/element-ui.common.js~~

### 2. Modify the element-ui source, then build a distribution

~~After cloning the [element-ui github repository](https://github.com/ElemeFE/element), modify the `/packages/input/src/input.vue` file. Then run *npm run dist* and put the output into your project.~~

I have uploaded a version that fixes the problem above (based on v2.9.1) to [my github repository](https://github.com/LoveMeWithoutAll/element). Clone either the master branch or the dev branch, go into that path in your console, run `npm run dist`, and then copy and paste the build output into the `node_modules/element-ui` folder of the project you're working on.

If you want to modify the source directly, refer to the following links.

1. issue: https://github.com/ElemeFE/element/pull/15069
1. source code: https://raw.githubusercontent.com/ElemeFE/element/c1e76a3f01d7c51a5bdd6cb485e1e49d09007882/packages/input/src/input.vue

## Other Notes

~~[JiHyung Lee](https://github.com/jhlee8804) made a [contribution](https://github.com/ElemeFE/element/issues/11665) that fixes the problem above, so I believe it will be resolved in the next version.~~
The Korean input problem will probably only be fixed in version 3.0.

Of course, polyfills are essential. Refer to [this post](https://lovemewithoutall.github.io/it/vue-ie-support-with-es6-promise/).

The end!

[element-ui]: https://github.com/ElemeFE/element
[google chrome]: https://www.google.com/chrome/
