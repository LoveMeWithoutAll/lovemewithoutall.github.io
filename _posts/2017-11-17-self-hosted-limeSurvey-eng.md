---
layout: single
title: Installing a Self-Hosted LimeSurvey Server
date: 2017-11-17 17:07:30.000000000 +09:00
type: post
header:
  teaser: "https://www.limesurvey.org/images/logos/logo_main_white.png"
  image: "https://www.limesurvey.org/images/logos/logo_main_white.png"
categories:
- IT
tags: [survey, self hosted survey, LimeSurvey, self host]
---
# 0. Getting Started

At work, I occasionally get peculiar requests, like being asked to build a dedicated survey service just for someone's department. They don't want to use [Google Forms] because of some concern about privacy exposure or whatever, and they don't want to use the general-purpose survey service we already provide either. I'm sure they have their reasons... Anyway, whenever this comes up, I just quietly build them a web page through tedious grunt work, every single time. But today, for some reason, I got a bit cranky. So I tried something I'd never done before. You don't want to use the existing survey service, and you don't want to use [Google Forms] either? Then what if I just hand you a full-featured survey service that runs on a self-hosted server? What kind of reaction would I get if I solved every single complaint?

# 1. The Choice

It would have been great to build a survey service entirely from scratch myself, but unfortunately my actual job kept me far too busy. So I started looking for a reasonable solution that supports self-hosting. Two candidates came up. The conclusion is [LimeSurvey].

1. [TellForm]: I liked its bold self-introduction, *Free Opensource Alternative to TypeForm or Google Forms*, but unfortunately its [Docker] support was broken. With that, setting up the environment becomes far too much of a hassle. I filed the problem along with logs as an issue on its [repository](https://github.com/tellform/docker_files).
1. [LimeSurvey]: Feature-rich, well-documented, easy to use, and thanks to [Docker], easy to set up. The community edition is free. This is the one I chose.

# 2. Installation

It's very simple. Just do the following. The server's OS is [Ubuntu 16.04.3 LTS](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes?_ga=2.245309125.1154947023.1510894532-877806816.1510894532).

## 1. docker pull

I got help from [here](https://hub.docker.com/r/crramirez/limesurvey/).

```bash
docker pull crramirez/limesurvey
```

## 2. Writing docker-compose.yml

Since I already have another service running on port 80, I decided to assign port 8080 to LimeSurvey.

```bash
version: '2'
services:
  limesurvey:
    ports:
      - "8080:8080"
    volumes:
      - mysql:/var/lib/mysql
      - upload:/app/upload
    image:
      crramirez/limesurvey:latest
volumes:
  mysql:
  upload:
```

## 3. Running

Run the Docker container from the directory where you saved the docker-compose.yml created above.

```bash
docker-compose up -d
```

Now if you go to http://localhost:8080 in your browser, the LimeSurvey screen should appear.

## 4. A Quick Setup

The LimeSurvey installation screen will appear, and you just follow what's shown in the Usage section on DockerHub. I've copied it below as well.

* Database type MySQL
* Database location localhost
* Database user root*
* Database password
* Database name limesurvey #Or whatever you like
* Table prefix lime_ #Or whatever you like

# 3. Done

Now everything is ready. Just use it. How exciting!

[Docker]: <https://www.docker.com>
[LimeSurvey]: https://www.limesurvey.org/
[TellForm]: https://www.tellform.com
[Google Forms]: https://www.google.com/forms/about/
