---
layout: single
title: Deploy python telegram bot on IBM BlueMix
date: 2018-03-27 21:07:30.000000000 +09:00
type: post
header:
  teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
  image: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
categories:
- IT
tags: [IBM BlueMix, BlueMix, bot, python, telegram bot, deploy, cloud foundry]
---

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
If you're app name is shamanKing_bot_v0.0.1, you must write same name in manifest.yml.
```sh
- name: shamanKing_bot_v0.0.1
  instances: 1
  memory: 128M
```

And if you're app is not web app, you must describe it. Health checker will it turn off if you do not.
```sh
applications:
- name: shamanKing_bot_v0.0.1
  instances: 1
  memory: 128M
  disk_quota: 1024M
  no-route: true
```

Another option when deploying
* reference: https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html#no-route
* reference: https://console.ng.bluemix.net/docs/manageapps/depapps.html#deployingapps
```sh
cf set-health-check shamanKing_bot_v0.0.1 none
cf push --no-route
```

### setup Procfile
Procfile is for run.
```sh
web: python bot.py
```

### setup requirements.txt
Must write requirements.txt, and put some lib for BlueMix. 
```sh
cf-deployment-tracker==1.0.2
cloudant==2.4.0
```

### setup runtime.txt
Describe what runtime environement you want
```sh
python-3.5.0
```
