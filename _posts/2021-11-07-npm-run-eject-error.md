---
layout: single
title: create-react-app에서 npm run eject 두 번은 못한다고?
date: 2021-11-07 01:45:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
categories:
- IT
tags: [react]
---

create-react-app으로 react 개발을 하다가, webpack 설정이 궁금해서 npm run eject를 해봤다.
설정을 훑어보고 git checkout -f 로 원복했는데...
다시는 npm run eject를 할 수 없었다.

npm log에는 별다른 정보가 없었다.

```log
  14   │ 12 verbose stack spawn ENOENT
  15   │ 12 verbose stack     at ChildProcess.<anonymous> (/usr/local/lib/node_modules/npm/node_modules/npm-lifecycle/lib/spawn.js:48:18)
  16   │ 12 verbose stack     at ChildProcess.emit (events.js:400:28)
  17   │ 12 verbose stack     at maybeClose (internal/child_process.js:1055:16)
  18   │ 12 verbose stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:288:5)
```

stackoverflow에도 해결책이 나오지 않았다. 그래서 혼자 궁리하여 해결책을 찾았다. 방법은 간단하다.

```bash
npm uninstall react-scripts
npm install react-scripts
```

순서대로 하면 된다. 끝.

20211017
