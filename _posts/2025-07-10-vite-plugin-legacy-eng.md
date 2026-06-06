---
layout: single
title: 'A Summary of @vitejs/plugin-legacy'
date: 2025-07-10 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://vitejs.dev/logo-with-shadow.png"
    image: "https://vitejs.dev/logo-with-shadow.png"
categories:
- IT
tags: [javascript, vite]
---

# A Summary of @vitejs/plugin-legacy

## build output

@vitejs/plugin-legacy produces two build outputs.

1. modern build
2. legacy build

On modern browsers, the web app runs from the modern build. On legacy browsers, it runs from the legacy build.

## The criterion that separates modern from legacy

It comes down to whether the browser supports

```html
<script type="module">
```

In other words, does it support ES modules or not?

- Supported: modern browser
- Not supported: legacy browser

So `Chrome >= 64, Safari >= 12` are modern browsers.

There are more criteria beyond ES module support, but I'll skip the detailed explanation.

## Browsers in the gray zone

Safari 12 supports modules, but it has the awkward problem of not supporting some modern APIs.

Chrome 61 ~ 63 are the same. Fortunately, though, this didn't end up causing any problems for me.

## So how do you configure which browsers to support?

- The targets property: specifies the browser versions the legacy build should support
- The modernTargets property: specifies the browser versions the modern build should support

## Didn't you say there were browsers in the gray zone?

Right. That's why setting modernTarget: ['safari >= 13'] causes problems.

The modernTarget setting only specifies "to what level" the transpiler should ensure backward compatibility when it rewrites the JS code. Safari 12 is unconditionally classified as a modern browser. So if you want the transpiler to support Safari 12, you have to set 'safari >= 12'.

Since it gets complicated, don't set modernTarget. By default it's set to `safari >= 12`.

## The modernTarget property alone isn't enough

Safari 12 doesn't support 100% of modern APIs. So if you want to support Safari 12,

### set the modernPolyfills property to true.

When you do this, only the polyfills needed for the modern build are added automatically.

modernPolyfills defaults to false, so make sure to set it. You can also configure it to manually include only the polyfills you want. But that's a real pain, so just set it to true.

## You still need more polyfill configuration

@vitejs/plugin-legacy

### only adds polyfills for ECMAScript APIs automatically
### it does NOT add polyfills for Web, DOM, or CSS APIs!

## So what do you do about Web, DOM, and CSS APIs?

You have to explicitly list each polyfill name in the additionalModernPolyfills & additionalLegacyPolyfills properties.

It'll be a headache. IntersectionObserver support is one example.

```js
additionalModernPolyfills: [
  'intersection-observer',           // Web APIs must be done manually
]
```

I don't understand why they made it such a headache. Don't they know what developers want from @vitejs/plugin-legacy?

## Conclusion: it's not automatic

@vitejs/plugin-legacy does not add polyfills automatically. You have to read the docs carefully before using it.

Below is a configuration example.

```js
// vite.config.js
import legacy from '@vitejs/plugin-legacy'
import { defineConfig } from 'vite'

legacy({
  targets: ['chrome >= 50', 'safari >= 11'],  // the minimum browser versions the legacy build output should support
  modernPolyfills: true, // automatically add JS polyfills to the modern build output too, for Safari 12
  additionalModernPolyfills: [
    'intersection-observer',       // npm package name
    'whatwg-fetch'
  ],
  additionalLegacyPolyfills: [    // don't leave it out of the legacy build either
    'intersection-observer'       // it's a Web API, so you have to specify it manually
  ]
})
```

EOD

20250710
