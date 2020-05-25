---
layout: single
title: Jetbrains IDE update 403 error
date: 2020-05-25 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/1a/JetBrains_Logo_2016.svg/200px-JetBrains_Logo_2016.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/1/1a/JetBrains_Logo_2016.svg/200px-JetBrains_Logo_2016.svg.png"
categories:
- IT
tags: []
---

Since Jetbrains IDE(IntelliJ, WebStorm and etc) 2020.1 version, `UPDATE AND RESTART` would fail on the IDE and Plugin Updates menu.

```
Connection Error
Failed to prepare an update:
Request failed with status code 403
Open download page.
```

403 error? It's because 2020.1 IDE does not support 1.8 JDK. So you have to download new version of IDE with JDK 11 from [official download page](https://www.jetbrains.com/idea/download). Inconvenience.

### Reference

* https://blog.jetbrains.com/idea/2020/01/intellij-idea-2020-1-eap/#jbr_end_of_support
* https://blog.jetbrains.com/idea/2020/04/intellij-idea-2020-1-1/
* Reply by Andrey Dernov from link above