---
layout: single
title: A Tale of Struggling to Restore the gitlab-ce Docker Version
date: 2019-09-05 16:07:30.000000000 +09:00
type: post
header:
    teaser: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
    image: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
categories:
- IT
tags: [docker, gitlab]
---

# 0. The Situation

While upgrading the Ubuntu server running gitlab (16.04 LTS to 18.04 LTS), the upgrade failed and the server blew up. Recovery was impossible. So I just formatted it and did a clean install of Ubuntu. Then I tried to restore the backed-up data following [the article I wrote](https://lovemewithoutall.github.io/it/start-docker). But a problem cropped up.

# 1. The Symptom

The gitlab service wouldn't come up. When I checked the status of the gitlab container in docker, it wasn't starting and kept restarting endlessly.

# 2. The Cause

I dug into the log of the gitlab container.

```bash
sudo docker logs gitlab 
```

After checking, it turned out the error occurred because the gitlab version of the docker image and the gitlab version of the backup data were different.

# 3. The Solution

Match the version of the gitlab docker image to the backup data. Since I'm using `docker-compose`, I just needed to modify the `image` part of the yml file. Just align the image tag with the version of the backup data.

Here's an example.

```yml
web:
  image: 'gitlab/gitlab-ce:11.11.0-ce.0' # Set tag!
#   ...
```

# 4. Wrapping Up

Once the recovery is done, be sure to change the `docker-compose` image `tag` back to `latest`, then update gitlab.

When the recovery didn't work right away, I was extremely alarmed for a moment. Many people installed gitlab by following my blog, so what would happen if recovery failed? Fortunately I solved it easily, but it made me realize that I need a certain sense of responsibility as a development blogger.

Anyway, it works fine now. The end!
