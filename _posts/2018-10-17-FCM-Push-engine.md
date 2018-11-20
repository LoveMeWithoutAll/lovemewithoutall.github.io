---
layout: single
title: FCM Push engine
date: 2018-10-18 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png"
categories:
- IT
tags: [javascript, FCM]
---

## Server for FCM Admin

I am [Node.js] novice. But anyway it works!

### Send messages via Admin FCM API! 

This Application get messages from MS SQL DB, and send to individual devices.

## Github Repository

[LoveMeWithoutAll/FcmPushEngine](https://github.com/LoveMeWithoutAll/FcmPushEngine)

## This app is using

1. [Node.js] v8.12.0
1. Microsoft SQL Server 2016
1. Javascript library
    1. [Admin FCM API] v6.0.0
    1. [mssql](https://github.com/tediousjs/node-mssql) v4.2.1
    1. [winston] v3.1.0
    1. eslint v5.6.0
    1. [pm2] v3.2.2

## To run this app

1. Set DB config on config/dbConfig.json
1. Set FCM service account key on config/fcmServiceAccountKey.json
1. Set FCM database URL on config/fcmConfig.json
1. Write codes for get datas on service/db.js getPushList function
1. Write codes for send push to devices on service/fcm.js sendMsgList funciton
1. Write codes for push feedback on service/db.js pushFeedback function

## Project setup

```
npm install -g pm2
```

```
pm2 install pm2-logrotate
```

```
npm install
```

## Run
```
npm start
```

### Lints and fixes files
```
npm run lint
```

Contributing
-------------
Changes and improvements are more than welcome! Feel free to fork and open a pull request. 

License
-------------
This project is licensed under the MIT License


[Node.js]: https://nodejs.org/ko/
[Admin FCM API]: https://firebase.google.com/docs/cloud-messaging/admin/
[winston]: https://github.com/winstonjs/winston
[pm2]: http://pm2.keymetrics.io/
