---
layout: single
title: Solving Android NetworkOnMainThreadException with AsyncTask
date: 2018-05-08 20:00:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Android]
---

Starting from [Android Honeycomb](https://developer.android.com/about/versions/android-3.0-highlights), [you must not do networking on the Main Thread.](https://developer.android.com/reference/android/os/NetworkOnMainThreadException) Break this rule and you'll get an error like this!

```java
android.os.NetworkOnMainThreadException
```

For example, running the method below throws a NetworkOnMainThreadException.
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
This is meant to prevent the app from freezing because everything is being run on the main thread.

To solve this, you can use AsyncTask. There's no need to create a new thread yourself. I put together a simple example.

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

Use AsyncTask to handle inter-thread communication with ease!