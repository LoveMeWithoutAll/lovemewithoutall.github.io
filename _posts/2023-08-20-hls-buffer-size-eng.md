---
layout: single
title: Limiting Video Buffer Size
date: 2023-08-20 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://raw.githubusercontent.com/video-dev/hls.js/master/docs/logo.svg"
    image: "https://raw.githubusercontent.com/video-dev/hls.js/master/docs/logo.svg"
categories:
- IT
tags: [video, hls]
---

## Problem
I was trying to play video using HLS, but it consumed too much network data on the initial load.
In my case, the initial load buffered a full minute of video, using nearly 60MB of data.

## Cause
This happens because buffer data is downloaded in advance so that the video can play without interruptions. When using [hls.js](https://github.com/video-dev/hls.js), with the default options it pre-fetches nearly 60MB of data into the buffer.

## Solution
* A segment size of 1 to 2 seconds is appropriate for each segment.
* Limiting the buffer to 3 to 4 seconds is suitable for balancing latency and playback stability.

### Note: There is no way to solve this problem using only the HTML video tag and JavaScript.
### If you use an open-source HLS player, you can set the buffer size by changing each player's initialization settings.

#### Example configuration: hls.js
```javascript
new HLS({
  maxBufferLength: 3, // The maximum duration (in seconds) the HLS player attempts to buffer
  maxBufferSize: 3, // The maximum amount of content (in MB) the HLS player attempts to buffer
});
```

## Reference
* https://aws.amazon.com/ko/blogs/korea/part-3-how-to-compete-with-broadcast-latency-using-current-adaptive-bitrate-technologies/

2023.08.20.
