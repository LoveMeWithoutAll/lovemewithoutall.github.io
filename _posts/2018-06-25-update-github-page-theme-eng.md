---
layout: single
title: Updating the GitHub Page Theme
date: 2018-06-25 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://jekyllrb.com/img/logo-2x.png"
    image: "https://jekyllrb.com/img/logo-2x.png"
categories:
- IT
tags: [jekyll]
---

This blog runs on [jekyll]. For the theme, it uses [minimal mistake]. This theme is updated quite actively, and since I'm just riding along to get a nice-looking blog, I need to keep up with the theme updates whenever I find time between blogging sessions. Here's how to update the theme. Since I host my blog on GitHub Pages, I used the approach of updating with git.

## 1. Register the upstream branch

After registering the master branch of the [minimal mistake] repository as a remote,

```bash
git remote add upstream https://github.com/mmistakes/minimal-mistakes.git
```

## 2. pull

Pull the changes.

```bash
git pull upstream master
```

## 3. Resolve merge conflicts

## 4. Commit and push

Done!

## References

https://mmistakes.github.io/minimal-mistakes/docs/upgrading/

[jekyll]: <https://jekyllrb.com>
[minimal mistake]: <https://github.com/mmistakes/minimal-mistakes>
