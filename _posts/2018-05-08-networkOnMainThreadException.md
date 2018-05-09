---
layout: single
title: Android NetworkOnMainThreadException 해결 by AsyncTask
date: 2018-05-08 20:00:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Android]
---

[Android Honeycomb](https://developer.android.com/about/versions/android-3.0-highlights)부터는 [Main Thread에서 네트워킹 처리를 하면 안된다.](https://developer.android.com/reference/android/os/NetworkOnMainThreadException) 이를 어겼다가는 이런 에러가 뜬다!

```java
android.os.NetworkOnMainThreadException
```

예를 들어 아래 메서드를 실행하면 NetworkOnMainThreadException가 뜬다.
```java
// old code raising error
public HttpResponse getResponse(String url) {
  try {
    HttpGet request = new HttpGet(url);
    HttpResponse response = new DefaultHttpClient().execute(request); // raise error!
    return response;
  }
  catch (Exception e) {
    // android.os.NetworkOnMainThreadException!
  }
}
```
main thread에서 뭐든 다 돌리다가 앱이 멈추는 꼴을 막기 위함이다.

이를 해결하기 위해서는 AsyncTask를 사용하면 된다. 굳이 새로 thread를 만들 필요가 없다. 간단한 예제를 만들어보았다.

```java
// Use AsyncTask and no error!
public HttpResponse getResponse(String url) {
  try {
    AsyncTask<String, Void, HttpResponse> asyncTask = new AsyncTask<String, Void, HttpResponse>() {
      @Override
      protected HttpResponse doInBackground(String... url) {
        HttpGet request = new HttpGet(url[0]);
        HttpResponse response = null;
        try {
          response = new DefaultHttpClient().execute(request);
        } catch (IOException e) {
          e.printStackTrace();
        }
        return response;
      }
    };

    HttpResponse response = asyncTask.execute(url).get();
    return response;
  }
  catch (Exception e) {
    // No error
  }
}
```

AsyncTask를 사용하여 Thread간 통신을 편하게 하자!