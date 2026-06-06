---
layout: single
title: "You can't run npm run eject twice in create-react-app?"
date: 2021-11-07 01:45:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
categories:
- IT
tags: [react]
---

While doing React development with create-react-app, I was curious about the webpack configuration, so I tried running npm run eject.
I looked over the configuration and then reverted it with git checkout -f, but...
I could never run npm run eject again.

The npm log didn't have any useful information.

```log
  14   │ 12 verbose stack spawn ENOENT
  15   │ 12 verbose stack     at ChildProcess.<anonymous> (/usr/local/lib/node_modules/npm/node_modules/npm-lifecycle/lib/spawn.js:48:18)
  16   │ 12 verbose stack     at ChildProcess.emit (events.js:400:28)
  17   │ 12 verbose stack     at maybeClose (internal/child_process.js:1055:16)
  18   │ 12 verbose stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:288:5)
```

There was no solution on Stack Overflow either. So I figured it out on my own and found a fix. The method is simple.

```bash
npm uninstall react-scripts
npm install react-scripts
```

Just run them in order. Done.

20211017
