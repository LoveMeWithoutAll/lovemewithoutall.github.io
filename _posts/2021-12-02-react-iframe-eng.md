---
layout: single
title: When iframe contentWindow is null in React
date: 2021-12-02 07:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
    image: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
categories:
- IT
tags: [react]
---


```javascript
iframe.contentWindow.postMessage(msg, '*');
```

When you try to do the above but `iframe.contentWindow` is null,

you can use `useEffect` as shown below. This is because you need to access the iframe after rendering is finished.

```javascript
useEffect(() => {
  setSumerian(document.getElementById('sumerianHost'));
  console.log('ready to start')
}, [])
```


20211024
