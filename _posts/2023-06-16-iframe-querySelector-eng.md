---
layout: single
title: How to find the parent window's DOM from JS code in an iframe
date: 2023-06-16 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://cdn.pixabay.com/photo/2013/07/13/11/50/sand-158804_1280.png"
    image: "https://cdn.pixabay.com/photo/2013/07/13/11/50/sand-158804_1280.png"
categories:
- IT
tags: [javascript]
---

To find the parent window's DOM from JS code inside an iframe, you need to search the `parent.document` object.

```javascript
parent.document.querySelector('what you want');
```

In the browser's developer tools, searching the `window` object works just fine, but if you try to search the DOM that way from within an iframe, you'll run into an endless string of `null` search results.

~ End of today's one-hour rabbit hole ~

20230616
