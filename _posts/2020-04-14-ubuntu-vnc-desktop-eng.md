---
layout: single
title: Running Ubuntu Like a Desktop with Docker
date: 2020-04-14 18:23:30.000000000 +09:00
type: post
header:
    teaser: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
    image: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
categories:
- IT
tags: [docker]
---

To run Ubuntu like a desktop with Docker, just pull and run the Docker image below.

[docker pulls](https://hub.docker.com/r/dorowu/ubuntu-desktop-lxde-vnc)

The way you run `docker run` differs depending on how you want to connect.

## 1. Connecting via a web browser


```cmd
docker run -p 6080:80 -v /dev/shm:/dev/shm dorowu/ubuntu-desktop-lxde-vnc
```

After running this, connect by pointing your web browser's URL to `127.0.0.1:6080`.

## 2. VNC Viewer

```cmd
docker run -p 6080:80 -p 5900:5900 -v /dev/shm:/dev/shm dorowu/ubuntu-desktop-lxde-vnc
```

After running this, connect from your VNC Viewer to the VNC Server at `127.0.0.1:5800`. For the VNC Viewer, I recommend [TigerVNC](https://tigervnc.org/).

## 3. Other

To adjust the resolution, just add the following parameter.

```cmd
-e RESOLUTION=1920x1080
```

Of course, change the resolution numbers to whatever values you want.

------------

To be honest, all of this is already covered in the readme file.

You might wonder what something like this is even useful for, but it does come in handy when you're working with limited resources. And of course, you can build on it. [docker-ros-vnc](https://github.com/henry2423/docker-ros-vnc) solves the headache of setting up the [ROS](https://www.ros.org/) development environment all in one go (?). It's a world where you just can't live without Docker.
