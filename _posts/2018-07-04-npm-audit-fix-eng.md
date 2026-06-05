---
layout: single
title: npm audit fix
date: 2018-07-04 11:45:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/d/db/Npm-logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/d/db/Npm-logo.svg"
categories:
- IT
tags: [IT, NPM]
---

## tl;dr: Let's fix vulnerabilities with npm audit fix

At some point, running **npm install** started showing a message like this. It says vulnerabilities have been found, so you should run **npm audit fix**.

```bash
ys@YS-PC MINGW64 /c/workspace/summer/foreign_survey/client (develop)
$ npm install --only=prod
audited 30197 packages in 12.466s
found 657 vulnerabilities (574 low, 64 moderate, 16 high, 3 critical)
  run `npm audit fix` to fix them, or `npm audit` for details
```

So what is this? When you run **npm install**, it checks your packages for vulnerabilities. And with **npm audit fix**, it automatically updates the packages where vulnerabilities were found.

```bash
ys@YS-PC MINGW64 /c/workspace/summer/foreign_survey/client (develop)
$ npm audit fix
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.1.3 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.3: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ babel-preset-env@1.7.0
added 43 packages from 39 contributors, removed 10 packages, updated 8 packages and moved 1 package in 28.66s
fixed 477 of 657 vulnerabilities in 30197 scanned packages
  7 package updates for 180 vulns involved breaking changes
  (use `npm audit fix --force` to install breaking changes; or do it by hand)
```

In many cases, even running **npm audit fix** doesn't resolve all the vulnerabilities. When that happens, keep running **npm audit fix** until the number of vulnerabilities no longer goes down. If the vulnerabilities still don't reach 0, use **npm audit** to check what the problem is, and then fix it by hand.

```bash
ys@YS-PC MINGW64 /c/workspace/summer/foreign_survey/client (develop)
$ npm audit

                       === npm audit security report ===

# Run  npm install --save-dev nightwatch@1.0.6  to resolve 6 vulnerabilities
SEMVER WARNING: Recommended action is a potentially breaking change

  Low             Regular Expression Denial of Service

  Package         debug

  Dependency of   nightwatch [dev]

  Path            nightwatch > mocha-nightwatch > debug

  More info       https://nodesecurity.io/advisories/534




  Critical        Command Injection

  Package         growl

  Dependency of   nightwatch [dev]

  Path            nightwatch > mocha-nightwatch > growl

  More info       https://nodesecurity.io/advisories/146




  High            Denial of Service

  Package         http-proxy-agent

  Dependency of   nightwatch [dev]

  Path            nightwatch > proxy-agent > http-proxy-agent

  More info       https://nodesecurity.io/advisories/607




  High            Denial of Service

  Package         http-proxy-agent

  Dependency of   nightwatch [dev]

  Path            nightwatch > proxy-agent > pac-proxy-agent >
                  http-proxy-agent

  More info       https://nodesecurity.io/advisories/607




  High            Denial of Service

  Package         https-proxy-agent

  Dependency of   nightwatch [dev]

  Path            nightwatch > proxy-agent > https-proxy-agent

  More info       https://nodesecurity.io/advisories/593




  High            Denial of Service

  Package         https-proxy-agent

  Dependency of   nightwatch [dev]

  Path            nightwatch > proxy-agent > pac-proxy-agent >
                  https-proxy-agent

  More info       https://nodesecurity.io/advisories/593



# Run  npm install --save-dev url-loader@1.0.1  to resolve 1 vulnerability
SEMVER WARNING: Recommended action is a potentially breaking change

  Moderate        Regular Expression Denial of Service

  Package         mime

  Dependency of   url-loader [dev]

  Path            url-loader > mime

  More info       https://nodesecurity.io/advisories/535



found 7 vulnerabilities (1 low, 1 moderate, 4 high, 1 critical) in 33345 scanned packages
  7 vulnerabilities require semver-major dependency updates.
```

After checking it as shown above, update the problematic *nightwatch*.

```bash
ys@YS-PC MINGW64 /c/workspace/summer/foreign_survey/client (develop)
$ npm install --save-dev nightwatch@1.0.6
npm WARN deprecated socks@1.1.10: If using 2.x branch, please upgrade to at least 2.1.6 to avoid a serious bug with socket data flow and an import issue introduced in 2.1.0
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.4 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.4: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ nightwatch@1.0.6
added 13 packages from 438 contributors, removed 21 packages, updated 16 packages and audited 33329 packages in 21.056s
found 1 moderate severity vulnerability
  run `npm audit fix` to fix them, or `npm audit` for details
```

When you see the output below, all the vulnerabilities have been resolved.

```bash
ys@YS-PC MINGW64 /c/workspace/summer/foreign_survey/client (develop)
$ npm install --only=prod
audited 33337 packages in 12.174s
found 0 vulnerabilities
```

Done!

### References
1. https://docs.npmjs.com/cli/audit
1. https://blog.outsider.ne.kr/1375
