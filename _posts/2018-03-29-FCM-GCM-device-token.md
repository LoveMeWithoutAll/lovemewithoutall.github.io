---
layout: single
title: FCM's device token & GCM's
date: 2018-03-29 02:07:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
categories:
- IT
tags: [android, GCM, FCM, device token]
---

**[FCM]'s device token is same with [GCM]'s device token** because FCM includes GCM. But FCM's token has more information than GCM's. So if you want to convert FCM's token to GCM's, do something.

```java
String fcmToken = FirebaseInstanceId.getInstance().getToken();
String gcmToken = fcmTocken.substring(fcmTocken.indexOf(':') + 1);

// fcmToken.equals("dtvc2g3nsrc:APA91bGEPZlxgZoh7sV9HROrPtOA8R2oQKB_u3LZr1QeX9Q9MW7UBqxmO73FPXppUUEpVaWcP35WdsZLHZV-tsFSdaKTZfSJV4c5E-yuVUCv3TDkaPPBovDiAU8v5xE0ANq7-47iqObS") == true;
// gcmToken.equals("APA91bGEPZlxgZoh7sV9HROrPtOA8R2oQKB_u3LZr1QeX9Q9MW7UBqxmO73FPXppUUEpVaWcP35WdsZLHZV-tsFSdaKTZfSJV4c5E-yuVUCv3TDkaPPBovDiAU8v5xE0ANq7-47iqObS"_ == true;
```

[FCM]: <https://firebase.google.com/docs/cloud-messaging>
[GCM]: <https://developers.google.com/cloud-messaging/>