---
layout: single
title: Video buffer 크기 제한
date: 2023-08-20 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://raw.githubusercontent.com/video-dev/hls.js/master/docs/logo.svg"
    image: "https://raw.githubusercontent.com/video-dev/hls.js/master/docs/logo.svg"
categories:
- IT
tags: [video, hls]
---

## 문제
HLS로 video를 재생하려는데, 최초 로딩시 너무 많은 네트워크 데이터를 사용한다.
나의 경우에는 최초 로딩시 무려 1분의 buffer를 하느라 60MB 가까운 데이터를 사용했다.

## 원인
video를 끊김없이 재생하기 위하여 미리 buffer 데이터를 다운로드 받기 때문이다. [hls.js](https://github.com/video-dev/hls.js)를 사용할 경우, default 옵션이라면 거의 60MB의 데이터를 buffer로 미리 받아 놓는다.

## 해결책
* 각 segment는 1~2초 크기가 적합하다.
* buffer는 3~4초로 제한하는 것이 지연 시간과 재생 안정성의 균형을 맞추기에 적절하다

### 주의: HTML Video tag와 javascript만으로는 이 문제를 해결할 방법이 없다.
### 오픈소스 HLS 플레이어를 사용한다면 각 플레이어의 초기화 설정을 변경해서 buffer 크기를 설정할 수 있다.

#### 설정 예시: hls.js
```javascript
new HLS({
  maxBufferLength: 3, // HLS 플레이어가 버퍼링을 시도하는 최대 시간(초)
  maxBufferSize: 3, // HLS 플레이어가 버퍼링을 시도하는 최대 콘텐츠 크기(MB)
});
```

## Reference
* https://aws.amazon.com/ko/blogs/korea/part-3-how-to-compete-with-broadcast-latency-using-current-adaptive-bitrate-technologies/

2023.08.20.
