---
layout: single
title: docker로 ubuntu를 desktop처럼 돌리기
date: 2020-04-14 18:23:30.000000000 +09:00
type: post
header:
    teaser: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
    image: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
categories:
- IT
tags: [docker]
---

docker로 ubuntu를 desktop처럼 돌리려면 아래 docker image를 pull 받아서 실행하면 된다.

[docker pulls](https://hub.docker.com/r/dorowu/ubuntu-desktop-lxde-vnc)

접속 방법에 따라 `docker run` 방법이 갈린다.

## 1. 웹브라우저로 접속


```cmd
docker run -p 6080:80 -v /dev/shm:/dev/shm dorowu/ubuntu-desktop-lxde-vnc
```

로 실행 후, 웹브라우저의 URL을 `127.0.0.1:6080`로 해서 접속한다.

## 2. VNC Viewer

```cmd
docker run -p 6080:80 -p 5900:5900 -v /dev/shm:/dev/shm dorowu/ubuntu-desktop-lxde-vnc
```

로 실행 후, VNC Viewer에서 VNC Server로 `127.0.0.1:5800`으로 접속하면 된다. VNC Viewer는 [TigerVNC](https://tigervnc.org/)를 추천한다.

## 3. 그 외

해상도 조절은 아래 파라미터를 추가하면 된다.

```cmd
-e RESOLUTION=1920x1080
```

물론 해상도 숫자는 원하는 수로 적당히 바꾸자.

------------

사실 readme 파일에 다 나온 얘기다.

이런 걸 어디다 쓰냐 싶겠으나, 제한된 자원 하에서는 필요할 때도 있더라. 당연히 응용도 가능하다. [docker-ros-vnc](https://github.com/henry2423/docker-ros-vnc)는 [ROS](https://www.ros.org/)는 골치아픈 개발 환경 설정을 한방(?)에 해결해준다. docker 없이는 못 사는 세상이다.
