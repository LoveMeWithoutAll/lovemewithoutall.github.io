---
layout: single
title: Fixing gitlab-ci ERROR Job failed exit status 4
date: 2018-11-08 08:03:30.000000000 +09:00
type: post
header:
    teaser: "https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png"
    image: "https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png"
categories:
- IT
tags: [Git, Gitlab, Gitlab-CI, gitlab-runner, CI, CD]
---

## tl;dr

If you use git on Windows, increase the character limit.

```cmd
git config --system core.longpaths true
```

## Symptom

I [use gitlab-ci for CI/CD](https://lovemewithoutall.github.io/it/deploy-example-by-gitlab-ci/). It had been running well for over half a year, but then I ran into this error.

`exit status 4`

It looks like there's some kind of error message too, but the Korean text is garbled and unreadable.

```cmd
Running with gitlab-runner 10.5.0 (80b03db9)
  on shared Runner on 165.246.15.51 0aade441
Using Shell executor...
Running on SVN-15-51...
Fetching changes...
warning: Could not stat path 'office_password/client/node_modules/.cache/uglifyjs-webpack-plugin/content-v2/sha512/64/c8/ad010afc311df110709048bbcb60b8dea84752d285a399277975ee6fe16178b63b81d836171cdaf2da4c85ff88062b14f2d75a921513167654209060dda2': Filename too long
warning: Could not stat path 'office_password/client/node_modules/.cache/uglifyjs-webpack-plugin/content-v2/sha512/cf/31/114da13d22cdaf7eda074f9e19a8b194407575a29395ac800dfbf75ac2ca157a8ef7fb427e4578df9b5f9ffad3ccb80c3a8bae56ecdb986c88ac0e1d8d35': Filename too long
HEAD is now at 499fc1e Merge tag 'v1.1.0' into develop
From http://gitlab.inha.com/unit-website/info_comm
 * [new branch]      master     -> origin/master
Checking out d2ce5f23 as master...
Skipping Git submodules setup
$ echo 'deploy to production server'
'deploy to production server'
$ net use o: \\165.246.13.167\inhasite\info_comm INHA+alfo= /user:administrator
������ �� �����߽��ϴ�.

$ xcopy . o:\ /h /k /y /e /r /d /exclude:.\gitlab-ci-copy-exclude-list.txt
.\.gitlab-ci.yml
.\office_password\client\node_modules\.cache\uglifyjs-webpack-plugin\content-v2\sha512\64\c8\ad010afc311df110709048bbcb60b8dea84752d285a399277975ee6fe16178b63b81d836171cdaf2da4c85ff88062b14f2d75a921513167654209060dda2
���� ������ �����Դϴ� - ������ ���θ� ã�� �� �����ϴ�.

Running after script...
$ net use /delete o:
o:��(��) ���ŵǾ����ϴ�.

ERROR: Job failed: exit status 4
```

## Cause

On Windows, file and directory names cannot exceed 260 characters. The two lines below in the error log above were the problem.

`warning: Could not stat path 'office_password/client/node_modules/.cache/uglifyjs-webpack-plugin/content-v2/sha512/64/c8/ad010afc311df110709048bbcb60b8dea84752d285a399277975ee6fe16178b63b81d836171cdaf2da4c85ff88062b14f2d75a921513167654209060dda2': Filename too long`

`warning: Could not stat path 'office_password/client/node_modules/.cache/uglifyjs-webpack-plugin/content-v2/sha512/cf/31/114da13d22cdaf7eda074f9e19a8b194407575a29395ac800dfbf75ac2ca157a8ef7fb427e4578df9b5f9ffad3ccb80c3a8bae56ecdb986c88ac0e1d8d35': Filename too long`


## Fix

Just change the setting.

On the server where the `gitlab-ci runner` is running, open `cmd (administrator mode)` and run

```cmd
git config --system core.longpaths true
```

Now it works! 

## The End!

Reference: 
https://ourcodeworld.com/articles/read/109/how-to-solve-filename-too-long-error-in-git-powershell-and-github-application-for-windows
