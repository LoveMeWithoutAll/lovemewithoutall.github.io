---
layout: single
title: google map API 일본해(동해)를 동해로 바꾸는 방법
date: 2019-10-22 12:33:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2019-10-17-sea-of-japan/urgent_order.png"
    image: "/assets/images/2019-10-17-sea-of-japan/urgent_order.png"
categories:
- IT
tags: []
---

![국무총리 긴급 지시사항](/assets/images/2019-10-17-sea-of-japan/urgent_order.png)

국무총리 긴급 지시사항이다. 구글 지도에 `동해`가 `일본해(동해)`가 표기되어 있으므로, 이를 `동해`로 display 되도록 수정하라는 지시다. 정부와 연이 닿은 모든 모바일앱을 수정해야 한다. 기한은 이틀 후다.

내가 담당하는 앱도 `일본해(동해)`로 되어 있다.

![일본해](/assets/images/2019-10-17-sea-of-japan/sea-of-japan.jpg)

다행히 해결책은 간단하다.

google map API를 호출하는 get parameter에 `region=KR`을 추가하면 된다.

```javascript
"https://maps.googleapis.com/maps/api/js?key=blah&region=KR";
```

![동해](/assets/images/2019-10-17-sea-of-japan/east-sea.jpg)

끝!
