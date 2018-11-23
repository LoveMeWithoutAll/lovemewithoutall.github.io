---
layout: single
title: iframe에서 부모 frame으로 메시지 전달
date: 2018-11-23 12:45:30.000000000 +09:00
type: post
header:
    teaser: "https://cdn-images-1.medium.com/max/930/1*lGsxjcY4cOPHXPQ_iJHO7w.jpeg"
    image: "https://cdn-images-1.medium.com/max/930/1*lGsxjcY4cOPHXPQ_iJHO7w.jpeg"
categories:
- IT
tags: [JavaScript, HTML]
---

나는 iframe을 싫어하지만 이런 저런 이유로 부득불 사용해야만 할 때가 있다. 아래 코드는 iframe에서 부모 frame으로 데이터를 전송하는 방법이다. iframe에서는 `postMessage` 함수로 메세지(데이터)를 쏴주고, 부모 프레임에서는 iframe에서 전달받을 메시지(데이터)를 위한 이벤트를 등록하여 처리한다.

```html
<!-- iframe(child frame) -->
<script>
    window.parent.postMessage('data', '*'); // 메시지 전송
</script>
```

```html
<!-- parent frame -->
<script>
    window.addEventListener('message', handleDocHeightMsg, false); // 메시지 수신 이벤트 등록

    function handleDocHeightMsg(eventObj) { // 메시지 수신 처리를 위한 함수
        console.log(eventObj.data);
    }
</script>
```

참고: http://blog.302chanwoo.com/2016/08/postmessage/
