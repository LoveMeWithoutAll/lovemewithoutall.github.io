---
layout: single
title: Passing Messages from an iframe to the Parent Frame
date: 2018-11-23 12:45:30.000000000 +09:00
type: post
header:
    teaser: "https://cdn-images-1.medium.com/max/930/1*lGsxjcY4cOPHXPQ_iJHO7w.jpeg"
    image: "https://cdn-images-1.medium.com/max/930/1*lGsxjcY4cOPHXPQ_iJHO7w.jpeg"
categories:
- IT
tags: [JavaScript, HTML]
---

I dislike iframes, but for one reason or another there are times when I have no choice but to use them. The code below shows how to send data from an iframe to the parent frame. In the iframe, you fire off the message (data) using the `postMessage` function, and in the parent frame you register an event to handle the message (data) received from the iframe.

```html
<!-- iframe(child frame) -->
<script>
    window.parent.postMessage('data', '*'); // send the message
</script>
```

```html
<!-- parent frame -->
<script>
    window.addEventListener('message', handleDocHeightMsg, false); // register the message-receive event

    function handleDocHeightMsg(eventObj) { // function for handling the received message
        console.log(eventObj.data);
    }
</script>
```

Reference: http://blog.302chanwoo.com/2016/08/postmessage/
