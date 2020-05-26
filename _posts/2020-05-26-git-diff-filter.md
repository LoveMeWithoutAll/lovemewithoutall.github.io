---
layout: single
title: Use git filter by type of change
date: 2020-05-26 18:23:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/Git-Logo-2Color.png"
    image: "/assets/images/Git-Logo-2Color.png"
categories:
- IT
tags: [git]
---

Which files have been deleted or modified? Use `git diff --diff-filter=[(A|C|D|M|R|T|U|X|B)…​[*]]`

```bash
# Select all files
$ git diff --name-status  HEAD~1
M       package-lock.json
M       package.json
M       src/App.vue
M       src/plugins/vuetify.ts
M       src/router/index.ts
A       src/service/index.js
M       src/store/index.ts
D       src/views/Home.vue
A       src/views/Login.vue
M       tsconfig.json
M       vue.config.js
```

---------

## Include

Use Upper-case.

```bash
# Select modified files only
$ git diff --name-status --diff-filter=M HEAD~1
M       package-lock.json
M       package.json
M       src/App.vue
M       src/plugins/vuetify.ts
M       src/router/index.ts
M       src/store/index.ts
M       tsconfig.json
M       vue.config.js
```

---------

## Exclude

Use lower-case.

```bash
# Select not modified files only
$ git diff --name-status --diff-filter=m HEAD~1
A       src/service/index.js
D       src/views/Home.vue
A       src/views/Login.vue
```

### Reference: [git diff --diff-filter](https://git-scm.com/docs/git-diff#Documentation/git-diff.txt---diff-filterACDMRTUXB82308203)