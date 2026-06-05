---
layout: single
title: Checking the URL the Android WebView Back & Forward Buttons Will Navigate To
date: 2018-07-16 17:45:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [android]
---

When developing an app with a WebView on Android, the Back and Forward buttons can be a concern. When you press these buttons, the URL they navigate to might lead to a sensitive(?) screen. So I put together a way to check the URL.

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

Based on the URLs obtained using the API above, you can implement whatever logic you need.

That's it!

## References
[copyBackForwardList](https://developer.android.com/reference/android/webkit/WebView.html#copyBackForwardList())

[WebBackForwardList](https://developer.android.com/reference/android/webkit/WebBackForwardList.html)

[WebHistoryItem](https://developer.android.com/reference/android/webkit/WebHistoryItem)
