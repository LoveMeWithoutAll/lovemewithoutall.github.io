---
layout: single
title: gitlab-ce docker 버전 복구 삽질기
date: 2019-09-05 16:07:30.000000000 +09:00
type: post
header:
    teaser: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
    image: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
categories:
- IT
tags: [docker, gitlab]
---

# 0. 상황

gitlab이 돌고 있는 Ubuntu 서버를 업그레이드(16.04 LTS to 18.04 LTS)하다가 실패, 서버는 터져버렸다. 복구는 불가능했다. 그래서 그냥 포맷하고 Ubuntu 클린 설치를 했다. 그리고 [내가 쓴 글](https://lovemewithoutall.github.io/it/start-docker)에 나온대로 백업된 데이터의 복구를 시도했다. 하지만 문제가 터졌다.

# 1. 현상

gitlab 서비스가 올라오지 않았다. docker의 gitlab 컨테이너 상태를 보니 start가 되지 않아 끝없이 계속 restart를 하고 있었다.

# 2. 원인

gitlab 컨테이너의 log를 까봤다. 

```bash
sudo docker logs gitlab 
```

확인해보니 docker 이미지의 gitlab 버전과, 백업 데이터의 gitlab 버전이 달라서 오류가 발생했다.

# 3. 해결책

gitlabdocker 이미지의 버전을 백업 데이터에 맞춰준다. 나는 `docker-compose`를 사용하고 있으니, yml 파일의 `image` 부분을 수정하면 됐다. 이미지의 태그를 백업 데이터의 버전에 맞추면 된다.

아래는 예시다.

```yml
web:
  image: 'gitlab/gitlab-ce:11.11.0-ce.0' # Set tag!
#   ...
```

# 4. 마무리

복구가 끝난 다음, 반드시 `docker-compose`의 image `tag`를 `latest`로 변경한 후, gitlab을 업데이트하자.

복구가 바로 되지 않자 순간이나마 몹시 놀랐다. 내 블로그를 보고 gitlab 설치를 하신 분들이 많은데 복구가 안되면 어떻게 하나? 다행히 쉽게 해결했지만 개발 블로그로서 나름의 책임감이 필요함을 실감했다.

아무튼 이제 잘 된다. 끝!
