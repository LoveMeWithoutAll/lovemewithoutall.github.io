---
layout: single
title: Android WebView Back & Forward 버튼으로 도달할 URL 확인
date: 2018-07-16 17:45:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [android]
---

Android에서 WebView로 앱을 개발하다보면 Back 버튼과 Forward 버튼이 신경쓰인다. 이 버튼들을 눌렀을 때 도달할 URL이 민감한(?) 화면일 수 있기 때문. 그래서 URL을 확인할 수 있는 방법을 정리해보았다.

```java
import android.webkit.WebView;
import android.webkit.WebBackForwardList;

// WebView
WebView wv = (WebView) findViewById(R.id.webview);

// history list
WebBackForwardList historyList = wv.copyBackForwardList();

// Back button URL
String backTargetUrl = historyList.getItemAtIndex(historyList.getCurrentIndex() - 1).getUrl();

// Forward button URL
String forwardTargetUrl = historyList.getItemAtIndex(historyList.getCurrentIndex() + 1).getUrl();
```

위 API를 사용하여 얻은 URL을 기준으로 필요한 로직을 구현하면 될 것이다.

끝!

## 참고
[copyBackForwardList](https://developer.android.com/reference/android/webkit/WebView.html#copyBackForwardList())

[WebBackForwardList](https://developer.android.com/reference/android/webkit/WebBackForwardList.html)

[WebHistoryItem](https://developer.android.com/reference/android/webkit/WebHistoryItem)