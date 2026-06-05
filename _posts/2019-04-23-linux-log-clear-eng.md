---
layout: single
title: How to Free Up Disk Space on a Linux Server (Quick Log Cleanup)
date: 2019-04-23 10:33:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/225px-Tux.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/225px-Tux.svg.png"
categories:
- IT
tags: [Linux]
---

There's a Linux server that hasn't been properly managed. I've put together, in the order I use them, the commands for deleting that server's log files.

--------------

# 1. Check disk usage

```bash
df
```

--------------

# 2. Check usage per subdirectory

```bash
du -h --max-depth=1 # Check directory usage only
du -sh *            # Check usage for both directories and files
```

--------------

# 3. Check files

Search for and sort log files older than 30 days

```bash
find ./ -name '*.log.*' -mtime +29 | sort
```

--------------

# 4. Delete files

Delete log files older than 30 days

```bash
find ./ -name '*.log.*' -mtime +29 -delete
```

--------------

# 5. Compress files, then delete

Compress log files older than 2 days, then delete the originals

```bash
find ./ -name '*.log.*' -mtime +1 -exec sh -c "tar cjvf {}.bz2 {}; rm -f {};" \;
```
