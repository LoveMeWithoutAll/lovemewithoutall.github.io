---
layout: single
title: The Structure of node_modules and Phantom Dependency
date: 2021-12-03 18:01:30.000000000 +09:00
type: post
header:
    teaser: "http://image.yes24.com/momo/TopCate526/MidCate006/52551019.jpg"
    image: "http://image.yes24.com/momo/TopCate526/MidCate006/52551019.jpg"
categories:
- IT
tags: [npm, yarn, node_modules]
---

## The Structure of node_modules

The node_modules directory stores the libraries specified in package.json. Each library also has its own package.json, and the dependencies specified in that package.json are stored under a node_modules directory created inside the directory where the corresponding library is stored. At this point, if multiple libraries use the same version of a library as a dependency at the same time, that library is hoisted to the top-level path of the node_modules directory.

![artifacts](/assets/images/2021-12-03-node-module-phantom-dependency.png)

Source: https://toss.tech/article/node-modules-and-yarn-berry

## Phantom Dependency

A phantom dependency is a phenomenon that occurs because of where duplicate libraries are located. A phantom dependency means you can use a library that is not specified in package.json. Because the version of this library is not clearly defined, it can cause version compatibility errors for other users.


### References

- https://rushjs.io/pages/advanced/phantom_deps/
- https://toss.tech/article/node-modules-and-yarn-berry

20211027
