---
layout: single
title: Javascript logger Winston usage example
date: 2018-10-11 12:50:30.000000000 +09:00
type: post
header:
    teaser: "https://avatars2.githubusercontent.com/u/9682013?s=200&v=4"
    image: "https://avatars2.githubusercontent.com/u/9682013?s=200&v=4"
categories:
- IT
tags: [javascript, log]
---

## 0. JavaScript Logger

After reviewing a few JavaScript logging libraries, I found [winston] to be excellent. The usage is well documented on the official website, but I'm going to write down a simple example for later reference.

The versions and environment I used are as follows.

1. winston: v3.1.0
1. winston-daily-rotate-file: v3.3.3
1. Node.js: v8.12.0
1. OS: Windows 10 & Windows Server 2008

## 1. Installation

Let's install [winston] and [winston-daily-rotate-file]. [winston] is the main component that handles logging, and [winston-daily-rotate-file] manages the logs so they accumulate on a daily basis.

```javascript
npm install --save winston winston-daily-rotate-file
```

## 2. Configuring winston

Let's create a separate file to set up the [winston] configuration. The following settings are implemented.

1. Write logs to the console.
1. Console logs are printed in color.
1. Write log files.
1. The console logs at the info level and above, while the log file logs at the debug level and above.
1. Create one log file per day.
1. Accumulate log files in the log folder.
1. The maximum log file size is 20 Mbyte.
1. Log files are kept for 14 days.
1. Log files are compressed.
1. In the development environment, log from the debug level; in the production environment, log from the info level.

```javascript
// config/logConfig.js
"use strict"
const { createLogger, format, transports } = require("winston")
require("winston-daily-rotate-file")
const fs = require("fs")

const env = process.env.NODE_ENV || "development"
const logDir = "log"

// Create the log directory if it does not exist
if (!fs.existsSync(logDir)) {
	fs.mkdirSync(logDir)
}

const dailyRotateFileTransport = new transports.DailyRotateFile({
  level: "debug",
  filename: `${logDir}/%DATE%-smart-push.log`,
  datePattern: "YYYY-MM-DD",
  zippedArchive: true,
  maxSize: "20m",
  maxFiles: "14d"
})

const logger = createLogger({
  level: env === "development" ? "debug" : "info",
  format: format.combine(
    format.timestamp({
      format: "YYYY-MM-DD HH:mm:ss"
    }),
    format.json()
  ),
  transports: [
    new transports.Console({
      level: "info",
      format: format.combine(
        format.colorize(),
        format.printf(
          info => `${info.timestamp} ${info.level}: ${info.message}`
        )
      )
    }),
    dailyRotateFileTransport
  ]
})

module.exports = {
  logger: logger
}
```

## 3. Logging function

Using the logging configuration set up above, let's create a function that writes logs. Since this is an example, I put it together loosely, but if it's an application you depend on to make a living, you need to think carefully about how to write your logs. we all love logs!

![I ♥ logs](https://i1.wp.com/blog.parse.ly/wp-content/uploads/2014/12/heart_logs.png?w=360&ssl=1)

(This post is almost entirely unrelated to the content of the book above.)

```javascript
// ./service/log.js
const { logger } = require("../config/logConfig")

const onStart = () => {
  logger.info("server start")
}

const onSendingMsgError = (error, msg) => {
  error.msg = msg
  logger.error(`Error on sending message: msgseq=${msg.data.msgseq} | msg=${msg.data.msg}`)
  logger.error(error)
}

// ...

module.exports = {
  onSendingMsgError: onSendingMsgError,
  onStart: onStart
}
```

## 4. Writing logs

Now let's write logs with the log functions.

```javascript
// ./index.js
const { onStart, onSendingMsgError } = require("../service/log")

onStart()
// ...
onSendingMsgError(error, msg)
```

## 5. Wrap-up

As [winston] has been updated over the versions, the recommended usage in the official documentation has changed a lot. So don't rely on outdated Korean blog resources; you should always develop while referring to the official documentation.

The following resources were helpful in creating this example.

1. [Official examples](https://github.com/winstonjs/winston/tree/master/examples)
1. [Actively updated English resource](https://thisdavej.com/using-winston-a-versatile-logging-library-for-node-js/)

The end!

[winston]: https://github.com/winstonjs/winston
[winston-daily-rotate-file]: https://github.com/winstonjs/winston-daily-rotate-file
