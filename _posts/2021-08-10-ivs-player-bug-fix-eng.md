---
layout: single
title: IVS Player Cannot read property ‘collectLogs’ of undefined Error
date: 2021-08-12 00:10:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2021-08-10-ivs-player-bug-fix.png"
    image: "/assets/images/2021-08-10-ivs-player-bug-fix.png"
categories:
- IT
tags: [aws, ivs-player]
---

# update: 20210811

This error, which drove me absolutely crazy, was fixed in ivs player version 1.4.0.

- Fixed an error in which “Cannot read property ‘collectLogs’ of undefined” is reported in the developer console.

https://docs.aws.amazon.com/ivs/latest/userguide/release-notes.html

-----------

## Symptom
![error](/assets/images/2021-08-10-ivs-player-bug-fix.png)
This is an error that occurs in amazon-ivs-player web SDK version 1.3.1.

-----------

## Cause
The reason the error message “Cannot read property ‘collectLogs’ of undefined” occurs is that the wasm could not be fetched from the web server, so ivs-wasmworker tried to do something on an object that had become undefined. To solve this problem,

-----------

## Solution
Make sure to allow the MIME type on your web server. The type you need to allow is application/wasm.

-----------

## References
https://forums.aws.amazon.com/thread.jspa?threadID=340175

I also filed a strongly worded complaint with AWS...

-----------

20210729
