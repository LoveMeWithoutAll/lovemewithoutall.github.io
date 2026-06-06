---
layout: single
title: Variable Name Collision Error in Micro Frontends
date: 2025-04-04 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://vite.dev/logo-with-shadow.png"
    image: "https://vite.dev/logo-with-shadow.png"
categories:
- IT
tags: [vite]
---

## Goal

### Implement a simple micro frontend without module federation

## Environment
* vite v6.2.0
* [vite + react + ts](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts)

## Symptom

When you load multiple micro frontend build outputs in `index.html` using script tags as shown below

```html
<script type="module" src="https://domain.com/home.js"></script>
<script type="module" src="https://domain.com/footer.js"></script>
```

the following error occurs

```js
footer.js:49 Uncaught TypeError: O1.createRoot is not a function
    at footer.js:49:34687
(anonymous) @ footer.js:49
left-navigation-bar.js:48 Uncaught TypeError: Cannot read properties of undefined (reading 'S')
    at zy (left-navigation-bar.js:48:41683)
    at Oy (left-navigation-bar.js:49:33935)
    at left-navigation-bar.js:49:33958
zy @ left-navigation-bar.js:48
Oy @ left-navigation-bar.js:49
```

## Cause

It happens because the imported micro frontend build outputs use the same variable names and function names.

## Solution

### Option 1
You just need to assign a different namespace to each build output.

It works like this.
- footer.js -> footer
- header.js -> header

To assign a namespace, let's modify `rollupOptions`. You can configure it in `vite.config.ts` as shown below.

```tsx
/** vite.config.ts */
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  build: {
    target: 'esnext',
    rollupOptions: {
      output: {
        // ...
        format: 'umd',
        inlineDynamicImports: true, // handle dynamic imports inline
        name: 'header', // assign a namespace. Use a different namespace for other modules.
      }
    }
  }
})
```

### Option 2
[Build as a library in vite](https://ko.vite.dev/guide/build#library-mode). Since it's already well documented, I'll skip a more detailed explanation.

EOD

20250404
