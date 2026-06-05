---
layout: single
title: Automating work folder backups with git
date: 2018-06-29 18:42:30.000000000 +09:00
header:
  teaser: "/assets/images/Git-Logo-2Color.png"
  image: "/assets/images/Git-Logo-2Color.png"
type: post
categories:
- IT
tags: [git, automate, shell, backup]
---

I am a lazy person. And backing something up is a really tedious chore. Source code isn't so bad. Whenever I work on and modify it, I can just push it to a git remote server. But backing up the work folder I keep for tasks and management is just way too tedious. So I decided to automate it. The steps go like this:

1. Create a script that commits the changes in the work folder using git.
1. Make the script run as a batch job once a day.

I've put together the setup methods for Windows 7 and Windows 10 respectively.

## 1. Windows7

Create the shell script below

```bash
#! /bin/bash
cd /d/work # the folder I want to back up
git status
git add .
git commit -m $(date +"%Y%m%d_%T")
git push -u origin master
```

and register it in Task Scheduler.

## 2. Windows10

Thanks to the Windows Subsystem for Linux (WSL), SSH Key management has become somewhat more complicated. After creating an SSH Key by referring to the article on [this blog](http://webdir.tistory.com/547), register the batch script below in Task Scheduler (no shell script! bat).

```cmd
@echo off
for /f "tokens=2 delims==" %%a in ('wmic OS Get localdatetime /value') do set "dt=%%a"
set "YY=%dt:~2,2%" & set "YYYY=%dt:~0,4%" & set "MM=%dt:~4,2%" & set "DD=%dt:~6,2%"
set "HH=%dt:~8,2%" & set "Min=%dt:~10,2%" & set "Sec=%dt:~12,2%"
set "datestamp=%YYYY%%MM%%DD%" & set "timestamp=%HH%%Min%%Sec%"
set "fullstamp=%YYYY%-%MM%-%DD%_%HH%-%Min%-%Sec%"

REM d:\work is the folder to back up
d:
cd work
git add .
git commit -m "%fullstamp%"
git push -u origin master 
```

With this, I get to save 1 minute of my life every day.

> **Tip:** When you open an **MS Office** document file, a temporary file starting with **~$** gets created, and because of this file, commits fail. So let's add it to gitignore. You can set it up like this.

```bash
~$*
```

Done!

Reference: https://stackoverflow.com/questions/11037831/filename-timestamp-in-windows-cmd-batch-script/11037921
