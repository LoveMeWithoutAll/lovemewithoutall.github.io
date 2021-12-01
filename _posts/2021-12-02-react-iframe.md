---
layout: single
title: react에서 iframe contentWindow가 null일 때
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

을 하려는 데, iframe.contentWindow가 null일 경우,

아래와 같이 useEffect를 쓰면 된다. 렌더링 끝난 후에 iframe을 불러와야 하기 때문이다.

```javascript
useEffect(() => {
  setSumerian(document.getElementById('sumerianHost'));
  console.log('ready to start')
}, [])
```


20211024
