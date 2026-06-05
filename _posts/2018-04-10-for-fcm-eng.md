---
layout: single
title: Migrating from GCM to FCM
date: 2018-04-10 16:07:30.000000000 +09:00
header:
  teaser: "https://www.gstatic.com/mobilesdk/160503_mobilesdk/logo/2x/firebase_96dp.png"
  image: "https://www.gstatic.com/mobilesdk/160503_mobilesdk/logo/2x/firebase_96dp.png"
type: post
categories:
- IT
tags: [fcm, Android, FireBase]
---

One of the services my company operates is an Android app, and this app still sends push messages via GCM.
But it has gotten to the point where we can no longer keep doing that.

### **We need to migrate from GCM to FCM.**

So I put together a short report on this, and I'm sharing it in case it's useful to others as well.

1. Current situation
    - The Android app is sending push messages via GCM
    - Starting with devices running Android Oreo, receiving push messages that don't use FCM is restricted
        - Cause
            - Background execution is restricted starting from Oreo
            - Receiving push messages and displaying notifications are also subject to these background execution restrictions
            - However, push messages received through FCM are an exception
            - Official documentation: https://developer.android.com/about/versions/oreo/background.html?hl=ko
        - Result: push notifications are not displayed when the app is closed or the device is in sleep mode
        - Action required: migrate push message delivery from GCM to FCM

2. FCM and GCM
    - FCM is an extension of GCM (reference: https://joshua1988.github.io/web-development/fcm-gcm-difference/)
    - FCM (https://firebase.google.com/docs/cloud-messaging/)
        - A push message delivery service provided by Firebase
        - Firebase
            - Firebase: Google's cloud development platform (https://firebase.google.com/?hl=ko)
            - Provides BaaS and FaaS

3. Development requirements
    - Android app
        - Change the device ID issuance API (GCM -> FCM)
        - Handle received push messages
        - Remove GCM and other unnecessary legacy code
    - Push delivery server
        - Complete rebuild if necessary (Node.js is the recommended option)
        - If a rebuild is not necessary
            - Change the HTTP endpoint
            - Change the auth_key
            - Apply the FCM delivery JSON format
            - Handle delivery results
        - Migration reference document: https://developers.google.com/cloud-messaging/android/android-migrate-fcm#update_the_usage_of_gcmpubsub

4. FCM push delivery server
    - [LoveMeWithoutAll/FcmPushEngine](https://github.com/LoveMeWithoutAll/FcmPushEngine)
