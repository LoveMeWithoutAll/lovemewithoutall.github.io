---
layout: single
title: Angular-UI-Router와 함께 쓰는 ocLazyLoad 예제
date: 2018-07-24 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://ui-router.github.io/images/logos/angular1.png"
    image: "https://ui-router.github.io/images/logos/angular1.png"
categories:
- IT
tags: [Angular, JavaScript]
---

이제는 애증의 물건이 되어버린 [AngularJS](https://angularjs.org/).
[Angular-UI-Router](https://ui-router.github.io/ng1/)를 사용하여 지연 로드(lazy load)를 구현해보았다. 
지연 로딩을 위해 [ocLazyLoad](https://oclazyload.readme.io/)를 사용했다.

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

이걸 만든지가 너무 오래된 나머지 내가 이런걸 만들었다는 사실조차 잊고 있었다. 아무튼 구성은 잘 돌아간다. 물론 이런 식으로 SPA를 구성하면 상당히 느리다. 페이지를 이동할 때마다 컨트롤러 역할을 하는 JavaScript 파일을 불러와야 하기 때문이다. 하지만 [이런](https://lovemewithoutall.github.io/it/angularjs-with-asp-net-2/) 사정이 있었어서 부득불 만들었던 기억이 난다. 옛 기술과 함께 고통받는 누군가에게 도움이 되었으면 좋겠다.

끝!