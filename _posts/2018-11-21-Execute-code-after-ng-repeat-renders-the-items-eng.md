---
layout: single
title: Running a Callback Function After ng-repeat Finishes Rendering in AngularJS
date: 2018-11-26 09:20:30.000000000 +09:00
type: post
header:
    teaser: "https://ui-router.github.io/images/logos/angular1.png"
    image: "https://ui-router.github.io/images/logos/angular1.png"
categories:
- IT
tags: [JavaScript, Angular]
---

[AngularJS] has no built-in feature for running a callback function after the DOM has finished rendering. You have to build it yourself.

## 1. Create a custom directive

When the DOM is rendered, it signals that the last element of the `ng-repeat` has been reached.

```javascript
angular.module('MyApp').directive('emitLastRepeaterElement', function() {
    return function(scope) {
        if (scope.$last){
            scope.$emit('LastRepeaterElement');
        }
    };
});
```

## 2. Register the custom directive

Register `emit-last-repeater-element` wherever the `ng-repeat` is attached.

```html
<div ng-controller="ctrl">
    <div ng-repeat="data in datas" emit-last-repeater-element>
        <span>{{ data }}</span>
    </div>
</div>
```

## 3. Register the callback function

Register the callback function to run when the `custom directive` emits the event.

```javascript
// ctrl
$scope.$on('LastRepeaterElement', () => {
    $timeout(() => console.log('good to go');, 0);
});
```

## 4. Wrapping up

I built this by referring to the two articles below, but for some reason it only worked correctly when I combined the methods from both of them. Using [AngularJS] has become a headache by now. Anyway, it works fine.

### Done!

## References

* https://blog.brunoscopelliti.com/run-a-directive-after-the-dom-has-finished-rendering/
* https://www.linkedin.com/pulse/20140822090331-106754325-execute-code-after-ng-repeat-renders-the-items/

[AngularJS]: https://angularjs.org/
