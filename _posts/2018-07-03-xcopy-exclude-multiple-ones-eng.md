---
layout: single
title: Copying with xcopy While Excluding Multiple Files and Directories
date: 2018-07-03 11:45:30.000000000 +09:00
type: post
header:
    teaser: "https://www.computerhope.com/cdn/dos/xcopy-command.gif"
    image: "https://www.computerhope.com/cdn/dos/xcopy-command.gif"
categories:
- IT
tags: [IT]
---

tl;dr: If you pass a **txt** file listing the items to exclude to the **/exclude:** option of [xcopy], you can copy while excluding multiple files and directories.

Here is how to exclude multiple directories and files when copying with [xcopy].

When you use the exclude option, you don't pass a file name or a directory name but rather a txt file:

```cmd
rem xcopy command
xcopy src-path destination-path /exclude:.\exclude-list.txt
```

In the txt file, you list the directories or files.

```bash
# .\copy-exclude-list.txt
\.git\ # exclude directory
.css # exclude CSS files
test* # exclude file names starting with test
# and so on
```

Done!

[xcopy]: https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/xcopy
