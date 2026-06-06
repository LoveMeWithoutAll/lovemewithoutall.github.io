---
layout: single
title: Things to Watch Out for When Managing an Object with useState in React
date: 2021-12-23 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
    image: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
categories:
- IT
tags: [react]
---

If you manage an object with useState in React, always create a brand-new object and pass it in when you call setState.

If you only change a property of the existing object and pass it as the argument to setState, useEffect won't detect the change. React recognizes a diff by comparing objects based on their reference identity. Since it's the same object, the reference is the same, so React thinks nothing has changed.

codeSandBox example
https://codesandbox.io/s/react-use-state-watch-class-l7r54?file=/src/App.tsx

20211220
