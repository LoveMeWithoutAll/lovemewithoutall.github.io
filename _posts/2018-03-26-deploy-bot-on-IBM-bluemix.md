---
layout: single
title: Deploy Node.js telegram bot on IBM BlueMix
date: 2018-03-22 19:07:30.000000000 +09:00
type: post
header:
  teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
  image: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
categories:
- IT
tags: [IBM BlueMix, BlueMix, bot, Node.js, telegram bot, deploy, cloud foundry]
---

### It works on my [Node.js project](https://github.com/LoveMeWithoutAll/HanGifBot).

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
If you're app is not web app, you must describe it. Health checker will it turn off if you do not.
```sh
applications:
- name: hangifbot
  instances: 1
  memory: 128M
  disk_quota: 1024M
  no-route: true
```

Another option when deploying
* reference: https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html#no-route
* reference: https://console.ng.bluemix.net/docs/manageapps/depapps.html#deployingapps
```sh
cf set-health-check hangifbot none
cf push --no-route
```
### setup package.json
package.json for run.
```sh
"scripts": {
    "start": "node bot.js"   
  }
```

### setup Dependencies
Must describe dependencies in package.json.
```sh
"dependencies": {
    "install": "^0.8.7",
    "node-bing-api": "^3.2.1",
    "node-telegram-bot-api": "^0.27.0",
    "npm": "^4.4.1"
  }
```

