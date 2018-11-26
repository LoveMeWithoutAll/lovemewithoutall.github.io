---
layout: single
title: AngularJs에서 ng-repeat 렌더링 종료 후 콜백 함수 실행하기
date: 2018-11-26 09:20:30.000000000 +09:00
type: post
header:
    teaser: "https://ui-router.github.io/images/logos/angular1.png"
    image: "https://ui-router.github.io/images/logos/angular1.png"
categories:
- IT
tags: [JavaScript, Angular]
---

[AngularJS]는 DOM 렌더링 완료 후에 콜백 함수를 실행하게 하는 기능이 없다. 직접 만들어줘야 한다.

## 1. custom directive 만들기

DOM 렌더링 시, `ng-repeat`의 마지막 줄에 도달했음을 알린다.

```javascript
angular.module('MyApp').directive('emitLastRepeaterElement', function() {
    return function(scope) {
        if (scope.$last){
            scope.$emit('LastRepeaterElement');
        }
    };
});
```

## 2. custom directive 등록

`ng-repeat`이 달린 곳에 `emit-last-repeater-element`를 등록한다.

```html
<div ng-controller="ctrl">
    <div ng-repeat="data in datas" emit-last-repeater-element>
        <span>{{ data }}</span>
    </div>
</div>
```

## 3. 콜백 함수 등록

`custom directive`에서 `emit`이 들어왔을 때의 콜백 함수를 등록한다.

```javascript
// ctrl
$scope.$on('LastRepeaterElement', () => {
    $timeout(() => console.log('good to go');, 0);
});
```

## 4. 마무리

아래 두 글을 참고로 만들었는데, 무슨 이유에서인지 두 글에 나온 방법을 조합해야 정상 동작이 되었다. 이제는 [AngularJS]를 쓰는 것도 골치아픈 일이 되었다. 아무튼 잘 돌아간다.

### 끝!

## 참고

* https://blog.brunoscopelliti.com/run-a-directive-after-the-dom-has-finished-rendering/
* https://www.linkedin.com/pulse/20140822090331-106754325-execute-code-after-ng-repeat-renders-the-items/

[AngularJS]: https://angularjs.org/
