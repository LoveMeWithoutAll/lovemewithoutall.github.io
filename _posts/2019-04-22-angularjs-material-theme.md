---
layout: single
title: AngularJS Material dialog theme setting
date: 2019-04-22 11:11:30.000000000 +09:00
type: post
header:
    teaser: "https://material.angularjs.org/latest/img/logo.svg"
    image: "https://material.angularjs.org/latest/img/logo.svg"
categories:
- IT
tags: [Angular, JavaScript]
---

[AngularJS Material] provide useful functions, especailly <b>theme</b>. Configuring theme is not complicated, but searching on google is not easy. So I wrote a simple sample code below.


# AngularJS config

In AngularJS config, you need to configure <b>$mdThemingProvider</b>. 

On line #4, name of theme is being configured up by <code>$mdThemingProvider.theme('alert')</code>. <b>'alert'</b> is the name of new theme.

On line #5 ~ 7, colors of 'alert' theme are being configured.

```javascript
angular
  .module('myApp', ['ngMaterial']
  .config(function($mdThemingProvider) {
      $mdThemingProvider.theme('alert')
        .primaryPalette('blue')
        .accentPalette('yellow')
        .warnPalette('red')
  }
```

# Dialog

In this example, only javascript is needed to open dialog. 

On line line #8, theme of dialog is configured. 'alert' is described on above AngularJS config.

```javascript
function makeAlert(content) {
    $mdDialog.show(
        $mdDialog.alert()
        .parent(angular.element(document.querySelector('#popupContainer')))
        .clickOutsideToClose(true)
        .title('CAUTION!')
        .textContent(content)
        .theme('alert')
        .ok('back')
    );
}
```

# html: ng-click

When user clicked <code>Save</code> button, theme configured dialog will be open.

```html
<a class="bluebtn" ng-click="makeAlert('Please fill out the required form.')">Save</a>
```

## reference

* [AngularJS Material document: $mdDialog](https://material.angularjs.org/latest/api/service/$mdDialog#mddialog-api-documentation)
* [AngularJS Material document: Configuring a Theme]

[AngularJS Material]: https://material.angularjs.org/latest/
[AngularJS Material document: Configuring a Theme]: https://material.angularjs.org/latest/Theming/03_configuring_a_theme