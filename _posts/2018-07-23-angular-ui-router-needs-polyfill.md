---
layout: single
title: 
date: 2018-07-23 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://polyfill.io/v2/assets/images/logo.svg"
    image: "https://polyfill.io/v2/assets/images/logo.svg"
categories:
- IT
tags: [Angular, JavaScript]
---

In IE(Internet Explorer) 11, [Angular-UI-Router](https://ui-router.github.io/ng1/)(v1.0.19) with AngularJS and [ocLazyLoad](https://oclazyload.readme.io/) failed to instantiate.

```javascript
SCRIPT5022: [$injector:modulerr] Failed to instantiate module majorApp due to:
Error: [$injector:modulerr] Failed to instantiate module ui.router due to:
Error: [$injector:modulerr] Failed to instantiate module ui.router.init due to:
TypeError: Object doesn't support property or method 'find'
   at getParamDeclaration (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:1603:9)
   at Param (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:1667:13)
   at ParamFactory.prototype.fromConfig (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:5044:13)
   at makeConfigParam (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:2732:59)
   at Anonymous function (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:515:50)
   at forEach (https://ajax.googleapis.com/ajax/libs/angularjs/1.7.2/angular.js:401:11)
   at map (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:515:9)
   at paramsBuilder (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:2734:13)
   at Anonymous function (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:2929:105)
   at StateBuilder.prototype.build (http://unpkg.com/@uirouter/angularjs/release/angular-ui-router.js:2930:17)
```

To resolve this error, you need [polyfill](https://polyfill.io/v2/docs/).

Add the below code.

```html
<script src="//cdn.polyfill.io/v2/polyfill.min.js?features=default,MutationObserver"></script>
```

And it will work.

## P.S.
1. [This problem has been fixed](https://github.com/angular-ui/ui-router/issues/3725). It will work.
1. If polyfill did not work, use this [polyfil](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find#Polyfill) from mozilla.org(thanks [brennaveen](https://github.com/brennaveen)!)
1. Revert to version 1.0.18 is also resonable solution because v1.0.19 looks unstable(thanks [betty](https://disqus.com/by/bettysteger/)!).