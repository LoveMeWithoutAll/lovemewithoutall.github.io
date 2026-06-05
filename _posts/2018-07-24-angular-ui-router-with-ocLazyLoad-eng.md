---
layout: single
title: An ocLazyLoad Example Using Angular-UI-Router
date: 2018-07-24 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://ui-router.github.io/images/logos/angular1.png"
    image: "https://ui-router.github.io/images/logos/angular1.png"
categories:
- IT
tags: [Angular, JavaScript]
---

[AngularJS](https://angularjs.org/), which has by now become a love-hate kind of thing.
I tried implementing lazy loading using [Angular-UI-Router](https://ui-router.github.io/ng1/).
For lazy loading, I used [ocLazyLoad](https://oclazyload.readme.io/).

```javascript
// app.js
var app = angular.module('App', ['ui.router', 'oc.lazyLoad']);

app.config(['$stateProvider', '$httpProvider', '$urlRouterProvider', '$controllerProvider', '$compileProvider', '$filterProvider', '$provide', '$ocLazyLoadProvider', function($stateProvider, $httpProvider, $urlRouterProvider, $controllerProvider, $compileProvider, $filterProvider, $provide, $ocLazyLoadProvider) {

  app.controller = $controllerProvider.register;
  app.directive = $compileProvider.directive;
  app.filter = $filterProvider.register;
  app.factory = $provide.factory;
  app.service = $provide.service;
  app.constant = $provide.constant;
  app.value = $provide.value;

  $urlRouterProvider.otherwise("/moduleName/param");

  //Config For ocLazyLoading
  $ocLazyLoadProvider.config({
    'debug': true, // For debugging 'true/false'
    'events': true, // For Event 'true/false'
    'modules': [
      { // Set modules initially
        name: 'moduleName1', // list module
        files: ['/moduleDir/controllers/Ctrl1.js', '/moduleDir/services/Service.js']
      },
      {
        name: 'moduleName2', // detail module
        files: ['/moduleDir/controllers/Ctrl2.js', '/moduleDir/services/Service.js']
      }
    ]
  });

  //Config/states of UI Router
  $stateProvider
    .state('stateName1', {
      url: "/moduleName1/:param",
      templateUrl: "/moduleDir/templates/template1.tpl.html",
      resolve: {
        loadMyCtrl: ['$ocLazyLoad', function($ocLazyLoad) {
          return $ocLazyLoad.load('moduleName1'); // Resolve promise and load before view
        }]
      }
    })
    .state('stateName2', {
      url: "/moduleName2/:param",
      templateUrl: "/moduleDir/templates/template2.tpl.html",
      resolve: {
        loadMyCtrl: ['$ocLazyLoad', function($ocLazyLoad) {
          return $ocLazyLoad.load('moduleName2'); // Resolve promise and load before view
        }]
      }
    })
}]);
```

It's been so long since I built this that I'd even forgotten I made such a thing. In any case, the setup works just fine. Of course, structuring an SPA this way is quite slow. That's because every time you navigate to a different page, you have to load the JavaScript file that acts as the controller. But I remember that, given [these](https://lovemewithoutall.github.io/it/angularjs-with-asp-net-2/) circumstances, I had no choice but to build it this way. I hope it helps someone out there who is also struggling with old technology.

The end!
