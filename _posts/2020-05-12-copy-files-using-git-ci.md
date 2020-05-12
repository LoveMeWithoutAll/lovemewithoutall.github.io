---
layout: single
title: Copy a changed or new files using GitLab-CI
date: 2020-05-12 18:23:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/Git-Logo-2Color.png"
    image: "/assets/images/Git-Logo-2Color.png"
categories:
- IT
tags: [git, gitlab, CI]
---

When using **GitLab-CI**, it is needed that finding changed or new files, and then copy & paste the files to server sometimes. The job is pretty simple.

## Environment
Windows Server

## git
Fisrt, listing all changed or new files using **git**. And write a text file.

```sh
git diff --name-only HEAD~2 > File-list.txt
```

## Copy

Second, Copying a list of files. In 'File-list.txt', there are lines of full path & name. Using **xcopy**, write a file named `auto-copy.bat`

```bat
@echo off
set src_folder=.\
set dst_folder=o:\target
for /f "tokens=*" %%i in (File-list.txt) DO (
    xcopy /S/E "%src_folder%\%%i" "%dst_folder%"
)
```

## GitLab-CI

Using bat files written above, set GitLab-CI script.

```yml
stages:
  - deploy

after_script:
  - 'net use /delete o:'

deploy-to-prod:
  stage: deploy
  environment:
    name: production server
  only:
    - master
  script:
    - echo 'deploy to production server'
    - 'net use o: \\server_ip\folder password /user:id'
    - 'git diff --name-only HEAD~2 > copy-target.txt'
    - 'auto-copy.bat'
```

## Reference

Copy a list (txt) of files: https://stackoverflow.com/questions/6257948/copy-a-list-txt-of-files