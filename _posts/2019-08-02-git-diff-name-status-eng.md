---
layout: single
title: Listing files changed between commits in git
date: 2019-08-02 18:07:30.000000000 +09:00
type: post
header:
    teaser: "assets/images/Git-Logo-2Color.png"
    image: "assets/images/Git-Logo-2Color.png"
categories:
- IT
tags: [git]
---

Let's compare commits and pull out the list of files that changed.

```bash
git diff --name-status <old_commit> <new_commit>
```

The meaning of the marker that precedes each filename is as follows ([source](https://git-scm.com/docs/git-diff#Documentation/git-diff.txt---name-status)):

```
A: addition of a file

C: copy of a file into a new one

D: deletion of a file

M: modification of the contents or mode of a file

R: renaming of a file

T: change in the type of the file

U: file is unmerged (you must complete the merge before it can be committed)

X: "unknown" change type (most probably a bug, please report it)
```

To pull out only files with a specific marker, for example only deleted files, just add the following option ([source](https://git-scm.com/docs/git-diff#Documentation/git-diff.txt---diff-filterACDMRTUXB82308203)):

```bash
--diff-filter=[(A|C|D|M|R|T|U|X|B)…​[*]]
```
