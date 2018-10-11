---
layout: single
title: Javascript logger Winston 사용 예제
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

JavaScript logging library 중 몇 개를 검토해보니 [winston]이 훌륭했다. 사용법은 공식 홈페이지에 잘 나와있지만, 나중을 위해 간단한 예제를 적어두려 한다.

사용한 버전과 환경은 다음과 같다.

1. winston: v3.1.0
1. winston-daily-rotate-file: v3.3.3
1. Node.js: v8.12.0
1. OS: Windows 10 & Windows Server 2008

## 1. 설치

[winston]과 [winston-daily-rotate-file]를 설치하자. [winston]은 로그를 남기는 본체고, [winston-daily-rotate-file]은 1일 단위로 로그를 쌓도록 관리해준다.

```javascript
npm install --save winston winston-daily-rotate-file
```

## 2. winston 설정

[winston]의 설정을 구성하는 파일을 따로 만들어준다. 다음 설정이 구현되어 있다.

1. 콘솔창에 로그를 남긴다.
1. 콘솔창의 로그는 컬러로 찍힌다.
1. 로그 파일을 남긴다.
1. 콘솔창은 info 레벨부터 로그를 남기고, 로그 파일에는 debug 레벨부터 로그를 남긴다.
1. 로그 파일은 매일 하나씩 남긴다.
1. log 폴더에 로그 파일을 쌓는다.
1. 로그 파일은 최대 20Mbyte다.
1. 로그 파일은 14일간 보관한다.
1. 로그 파일은 압축한다.
1. 개발 환경에서는 debug 레벨부터 로그를 찍고, 운영 환경에서는 info 레벨부터 로그를 찍는다.

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

위에서 구성한 logging 설정을 가지고 log를 찍는 function을 만든다. 예제라 적당히 만들었지만, 밥벌어먹고 살기 위한 어플리케이션이라면 잘 고민해서 로그를 찍어야 한다. we all love logs!

![I ♥ logs](https://i1.wp.com/blog.parse.ly/wp-content/uploads/2014/12/heart_logs.png?w=360&ssl=1)

(본 포스팅과 위 책의 내용은 거의 무관합니다.)

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

## 4. 로그 남기기

이제 로그 함수로 로그를 찍자.

```javascript
// ./index.js
const { onStart, onSendingMsgError } = require("../service/log")

onStart()
// ...
onSendingMsgError(error, msg)
```

## 5. 마무리

[winston]이 버전이 올라감에 따라 공식 문서에서의 권장 사용법이 많이 바뀌었다. 그러니 철지난 한국어 블로그 자료에 의존하지 말고, 꼭 공식 문서를 보면서 개발해야 한다. 

이 예제를 만드는데 다음 자료가 도움이 되었다. 

1. [공식 예제](https://github.com/winstonjs/winston/tree/master/examples)
1. [업데이트가 활발한 영어 자료](https://thisdavej.com/using-winston-a-versatile-logging-library-for-node-js/)

끝!

[winston]: https://github.com/winstonjs/winston
[winston-daily-rotate-file]: https://github.com/winstonjs/winston-daily-rotate-file

