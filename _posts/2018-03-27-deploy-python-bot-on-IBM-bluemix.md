---
layout: single
title: Deploy python telegram bot on IBM Cloud
date: 2019-06-24 10:41:30.000000000 +09:00
type: post
header:
  teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
  image: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
categories:
  - IT
tags: [IBM Cloud, BlueMix, bot, python, telegram bot, deploy, cloud foundry]
---

# Updated! For new IBM Cloud on 2019.06.24.

### It works on my [python project](https://github.com/LoveMeWithoutAll/ShamanKing_bot).

## If you do not follow this settings, you would see like this.

```sh
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 down
0 of 1 instances running, 1 down
0 of 1 instances running, 1 down
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 down
0 of 1 instances running, 1 down
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 down
FAILED
Start app timeout
```

## setup

### setup manifest.yml

If you're app name is `Shamanking`, you must write same name in manifest.yml.

```sh
- name: Shamanking
  instances: 1
  memory: 128M
```

But your bot is not web app, so IBM's health checker will turn it off in only 1 minute.

```sh
applications:
- name: Shamanking
  instances: 1
  memory: 128M
  disk_quota: 1024M
  no-route: true
```

And type like below on console.

```sh
ibmcloud cf set-health-check Shamanking none
ibmcloud cf push
```

Below for reference.

- no-route: https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html#no-route
- deploying: https://cloud.ibm.com/docs/cli?topic=cloud-cli-developing

### setup Procfile

Procfile is for run.

```sh
web: python bot.py
```

### setup requirements.txt

Must write requirements.txt, and put some lib for IBM Cloud.

```sh
pip freeze > requirements.txt
```

### setup runtime.txt

Describe what runtime environement you want

```sh
python-3.6.7
```
