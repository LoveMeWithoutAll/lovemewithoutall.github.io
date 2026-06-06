---
layout: single
title: What to do when npm audit finds security vulnerabilities
date: 2021-12-06 19:01:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/d/db/Npm-logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/d/db/Npm-logo.svg"
categories:
- IT
tags: [npm]
---

## The Situation

1. There are security vulnerabilities that npm audit fix cannot resolve.
2. The remaining vulnerabilities all originate from node modules referenced by storybook and react-scripts (create-react-app), which are both devDependencies.
3. Even after updating storybook and react-scripts (the culprits) to their latest versions, the vulnerabilities still appear.
	1. This is because somewhere among the dependencies of those node modules' dependencies, a module version that contains a vulnerability is being used.
4. Running npm audit fix --force increases the number of vulnerabilities.
	1. This is because it downgrades the node module to a version that npm audit considers safe, but somewhere among the dependencies of that downgraded node module's dependencies, a vulnerability exists.
	2. Even after the downgrade, running npm audit fix --force again does not solve this problem. It just repeats the process above.

## The Fix

1. Move every node module that is not included in the build output to devDependencies.
	1. This is so that node modules installed via devDependencies are not subject to the vulnerability scan.
2. Check with npm audit --production.
	1. This command does not scan node modules installed via devDependencies for vulnerabilities. This holds true even for a node module installed at the top level of the node_modules directory due to dependency hoisting.
		1. Dependency hoisting: when a specific node module version is referenced by multiple node modules, npm moves the referenced node module to the top-level path.
	2. Since devDependencies node modules are not included in the build output, they are irrelevant to security vulnerabilities.
		1. react-scripts (create-react-app) is a build tool. Its only role is to produce the build output. It does not introduce vulnerabilities into the deployed files.
		2. storybook likewise has no dependency on the deployed files, for the same reason.
		3. The same goes for other libraries such as craco.

## Summary

1. npm audit even checks for vulnerabilities in devDependencies, which is unnecessary.
2. To check the vulnerabilities of the actual build artifacts, add the option that excludes devDependencies and use npm audit --production.
	1. To do this, react-script must be moved to devDependencies.


## References

- A post stating that create-react-app will no longer accept security vulnerability issues: https://github.com/facebook/create-react-app/issues/11174
- Phantom dependencies: https://rushjs.io/pages/advanced/phantom_deps/
- toss's guide to using node modules: https://toss.tech/article/node-modules-and-yarn-berry

20211028
