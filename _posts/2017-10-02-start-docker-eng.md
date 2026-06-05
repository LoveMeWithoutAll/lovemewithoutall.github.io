---
layout: single
title: Installing GitLab with Docker
date: 2018-04-03 13:07:30.000000000 +09:00
header:
  teaser: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
  image: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
type: post
categories:
- IT
tags: [docker, gitlab]
---

## 1. Going with git
My company decided to adopt git. Until then, the company I work for had been using svn as its in-house version control tool, but I'm not smart enough to develop without git. I can never remember when I made, fixed, or deleted what... In this respect, **the advantage of git is clear: you can see the history.** Fortunately, my suggestion to use git was accepted, and I took sole charge of all the groundwork for it.

## 2. Going with [gitlab]
According to company policy that source code must be kept on an internal company server, I had to set up a separate git server. I got hold of an idle desktop and started by installing Linux (Ubuntu 16.04 LTS). Linus Torvalds... when will I ever escape from your grip... I pondered for a while over which one to use as the git server. The conclusion was [gitlab].
- [yona]: I heard it's widely used at Naver. They say it's nice because even non-developers can use it productively for their work. However, I couldn't verify whether it integrates well with CI tools like jenkins, so I rejected it.
- [gitlab]: I'm familiar with it since I've used it in various projects in the past, and its integration with CI tools is already proven. There are plenty of references too. Blindly following the crowd... herd-mentality development.

## 3. Going with [docker]
Up until now, I had always used gitlab by installing it directly on the OS. But this time I decided to try using [docker]. There was no reason other than wanting to give docker a try. I think that to make a living as a developer, you have to at least dabble in technologies that are making waves. Of course, docker has been a hot topic for quite a while now... Anyway, as you'll soon see, I had no regrets.

## 4. Installing docker & gitlab
### docker
Installing docker was simple. Just follow along with the [Ubuntu Docker installation guide] and you're done. apt-get is a wonderful thing.
### gitlab
A single command does it. It even runs it for you. This is based on the [gitlab Community Edition]. Change the url in the `--hostname` option as appropriate. The 22 in the `--publish` option means it uses port 22, so if ssh is running, adjust it appropriately. Based on the ':', the number in front is the port of the physical server (hereafter host), and the number behind is the port of the docker container connected to the host. Since docker is recognized as a single server and communicates with the host, you need to make this kind of setting. The `--volume` option is explained in the next section. Because I gave the `--restart` option, docker will bring gitlab up on its own even after the OS is rebooted. For further details, refer to the [guide](https://docs.gitlab.com/omnibus/docker/README.html).
```
sudo docker run --detach \
    --hostname gitlab.example.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```
To check whether it runs properly, open a web browser and access http://127.0.0.1. From another computer on the network, you can access it via the host's ip in a browser. If the gitlab login page shows up, it worked.

## 5. docker-compose
You could use it as is, but having to remember all those options isn't pretty. For people like me, there is docker-compose. It's also needed for later integration with CI tools. First, you have to remove the container that's already been created. Here's something worth quickly going over: images and containers. Think of an image as a CD for installing a container. A container is the server installed on the host. It holds the data. So if you delete a container, all the data is gone. Then doesn't that mean you shouldn't delete the container? This is explained in the next section.

```bash
sudo docker stop gitlab
sudo docker rm gitlab
```

There's an [official guide](https://docs.gitlab.com/omnibus/docker/README.html#install-gitlab-using-docker-compose) for installing docker-compose, so just follow along with it. My docker-compose configuration looks like this. It's identical to the example in the [official guide](https://docs.gitlab.com/omnibus/docker/README.html#install-gitlab-using-docker-compose) except that I added a backup folder to *volumes*.

```bash
# docker-compose.yml
web:
  image: 'gitlab/gitlab-ce:latest'
  restart: always
  hostname: 'gitlab.example.com'
  container_name: gitlab
  environment:
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'http://gitlab.example.com'
      # Add any other gitlab.rb configuration here, each on its own line
  ports:
    - '80:80'
    - '443:443'
    - '22:22'
  volumes:
    - '/srv/gitlab/config:/etc/gitlab'
    - '/srv/gitlab/logs:/var/log/gitlab'
    - '/srv/gitlab/data:/var/opt/gitlab'
    - 'my backup folder:/var/opt/gitlab/backups' # added backup folder

```

After you've finished configuring the `docker-compose.yml` file, just run the command below. Likewise, write the url and port to suit your situation.

```bash
docker-compose up -d
```

Now I've recreated and brought up the gitlab container. There's no need to use it right now, but from now on, this is the command to use when bringing up the gitlab container.

```bash
sudo docker start gitlab
```

## 6. backup
I was instructed to back up the gitlab server's data in case of an emergency. This is where I had to do a lot of trial and error. How do I back up a container? You said all the data is stored in the container? Do I have to turn the container into an image and keep a copy? At first that's what I thought. You can do it that way. But there's no need. You'll remember the `--volume` option among the docker run options in section 4. What this is, is an option that says the corresponding folder in the container will be used as a specific folder on the host. And the data that accumulates while using gitlab, including git commit data, all piles up inside the three folders specified in the `--volume` option. So you only need to back up those three folders. To restore, move the three backed-up folders into the three folders set in the `--volume` option, then run the gitlab container.

## 7. update
It's the same when updating the gitlab version. Delete the gitlab container, update the docker image, and then create a container from that image. There are various ways to set up backups, but I mounted a NAS via [samba] and set it up to drop a compressed file once a day with crontab. The sin lies with ransomware, not with [samba]. The commands are as follows. I referred to [here](https://docs.gitlab.com/omnibus/docker/#upgrade-gitlab-to-newer-version).

```bash
sudo docker stop gitlab                   # stop the docker container
sudo docker rm gitlab                     # delete the docker image
sudo docker pull gitlab/gitlab-ce:latest  # pull the latest gitlab version image
docker-compose up -d                      # run gitlab
```

If you've written a **docker-compose.yml**, you can update in an even simpler way.

```bash
# move to the location of docker-compose.yml
docker-compose pull   # gitlab update
docker-compose up -d  # restart gitlab
```

## 8. Configuring email delivery
In case a user loses their password, it's convenient if they can reset the password by email. Let's connect directly to the docker container and modify the configuration file.

```bash
sudo docker exec -it gitlab /bin/bash # connect to the container
vi etc/gitlab/gitlab.rb # let's modify the configuration file
```

There's [official documentation](https://docs.gitlab.com/omnibus/settings/smtp.html), so look at the example for the smtp server you want to use and just modify yours the same way. Now let's apply the changed settings. While still connected to the docker container, just enter the command below as is.

```bash
# after changing /etc/gitlab/gitlab.rb, always apply the changes with this command (Omnibus GitLab only)
gitlab-ctl reconfigure
```

Now you no longer have to receive urgent requests to reset passwords.

## 9. Wrapping up
My first encounter with docker was convenient and powerful. So much so that I wondered why I hadn't tried this great thing sooner. I have a habit of slamming the keyboard hard while typing when I get in a good mood during development, and after using docker I pounded the keyboard for three days straight. In fact, I had a hard time believing it could be this good, so I had to waste a lot of time testing it in every direction. I hope the readers of this post won't waste their lives on needless doubt like I did.

[yona]: <https://repo.yona.io/>
[gitlab]: <https://gitlab.com>
[docker]: <https://www.docker.com>
[Ubuntu Docker installation guide]: <https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#install-using-the-repository>
[gitlab Community Edition]: <https://hub.docker.com/r/gitlab/gitlab-ce/>
[samba]: <https://www.samba.org>
