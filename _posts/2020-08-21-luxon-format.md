---
layout: single
title: Luxon formatting to 2020-08-21 AM 9:19:11
date: 2020-08-21 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://cms-assets.tutsplus.com/uploads/users/34/posts/30258/preview_image/luxon.jpg"
    image: "https://cms-assets.tutsplus.com/uploads/users/34/posts/30258/preview_image/luxon.jpg"
categories:
- IT
tags: [JavaScript]
---

Luxon is a small version of Moment. Let's get it.

```javascript
const DateTime = luxon.DateTime;
const date = DateTime.fromISO('2020-08-20 16:00:11.727')
const newDate = date.toFormat('yyyy-MM-dd a h:mm:ss')
console.log(newDate) // 2020-08-20 PM 4:00:11
```

That's all.
