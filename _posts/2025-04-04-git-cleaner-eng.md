---
layout: single
title: Reduce git repository size by deleting directories no longer tracked by git
date: 2025-04-04 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/Git-Logo-2Color.png"
    image: "/assets/images/Git-Logo-2Color.png"
categories:
- IT
tags: [git]
---

## Goal

### The git repository has grown too large because of files that are no longer tracked. Let's remove those directories from past commits.

## Environment
* git v2.45.0

## Solution

### 1. Remove the specified directory from every branch in the repository

```bash
# Let's delete targetDirectory
git filter-branch --force --index-filter \
  "git rm -r --cached --ignore-unmatch targetDirectory" \
  --prune-empty -- --all
```
#### What the flags mean
*	--index-filter "git rm -r --cached --ignore-unmatch targetDirectory" : Remove the directory from Git's index (the staging area)
*	--prune-empty: Remove commits that become empty after the deletion
*	-- --all: Apply to all branches

### Remove git operation history

Git keeps a reflog that records its operation history. We expire it to remove unnecessary records.

```bash
git reflog expire --expire=now --all
```

### Force Garbage Collection

Use Git's garbage collection to remove unused objects. Using the Aggressive option lets you optimize more thoroughly.

```bash
git gc --prune=now --aggressive
```

### Re-pack (compress) git objects

Since we just rewrote a lot of history, let's re-compress git.

```bash
git repack -Ad
```

#### What is object packing?

This is an answer from GPT.

Object packing (pack) is the process of consolidating these individual objects into a single compressed file (or several files). Specifically:
* Saving space:
Managing one large file instead of many small files reduces the disk's metadata usage, so storage space is used more efficiently.
* Improved speed:
Finding the data you need in a single large file can be faster than reading countless small files from the file system.
* Delta compression:
By recording only the differences (deltas) between objects with similar content, you can achieve even greater storage savings.

### git push

Let's push the changes to the remote repository. Since we just rewrote a lot of history, you must use `--force` when pushing.

```bash
git push origin --force --all
git push origin --force --tags
```

EOD

20250404
