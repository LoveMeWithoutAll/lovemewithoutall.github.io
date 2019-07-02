---
layout: single
title: Deploying Node.js telegram bot on IBM BlueMix
date: 2019-07-02 19:07:30.000000000 +09:00
type: post
header:
  teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
  image: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
categories:
- IT
tags: [IBM Cloud, bot, Node.js, telegram]
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

## Setup

### Setup manifest.yml

If you're app is not web app, you must describe it. In the below, app name is `hangifbot`

```sh
applications:
- name: hangifbot
  instances: 1
  memory: 128M
  disk_quota: 1024M
  no-route: true # bot does not need router
```

Another option when deploying
* no-route: https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html#no-route
* deploying: https://cloud.ibm.com/docs/cli?topic=cloud-cli-developing

But there is a problem. Health checker will it turn off in only 1 minute if you do not like below.

```sh
cf set-health-check hangifbot none
cf push --no-route
```

### Setup package.json

Describe package.json for run.

```sh
# ...
"scripts": {
    "start": "node bot.js"   
  }
# ...  
```

## Deploy

### 1. install IBM Cloud Develop Tools

Install [IBM Cloud Develop Tools](https://console.bluemix.net/docs/cli/index.html#overview). It's a superset of [Cloud Foundry cli](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html).

### 2. Deploy

Fisrt, set up IBM Cloud.

```bash
ibmcloud login
# type your ID/PW

# set a location for running your bot
ibmcloud target --cf
```

And push to Cloud server.

```bash
# push to IBM Cloud and run!
ibmcloud cf push
```

That's all. `cf push` is good. `npm install` is executed before running automatically.
