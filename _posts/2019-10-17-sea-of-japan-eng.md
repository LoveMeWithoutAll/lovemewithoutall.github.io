---
layout: single
title: "How to change Sea of Japan (East Sea) to East Sea in the Google Maps API"
date: 2019-10-22 12:33:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2019-10-17-sea-of-japan/urgent_order.png"
    image: "/assets/images/2019-10-17-sea-of-japan/urgent_order.png"
categories:
- IT
tags: []
---

![Urgent instructions from the Prime Minister](/assets/images/2019-10-17-sea-of-japan/urgent_order.png)

These are urgent instructions from the Prime Minister. Since Google Maps labels the `East Sea` as `Sea of Japan (East Sea)`, the instruction is to fix it so that it displays as `East Sea`. Every mobile app connected to the government has to be updated. The deadline is two days from now.

The app I'm in charge of also shows `Sea of Japan (East Sea)`.

![Sea of Japan](/assets/images/2019-10-17-sea-of-japan/sea-of-japan.jpg)

Fortunately, the solution is simple.

Just add `region=KR` to the GET parameters when calling the Google Maps API.

```javascript
"https://maps.googleapis.com/maps/api/js?key=blah&region=KR";
```

![East Sea](/assets/images/2019-10-17-sea-of-japan/east-sea.jpg)

Done!
