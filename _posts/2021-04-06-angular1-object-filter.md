---
layout: single
title: AngularJS의 filter
date: 2021-04-06 13:46:30.000000000 +09:00
type: post
header:
    teaser: "https://acaroom.net/sites/default/files/images/blogs/angular_js.jpg"
    image: "https://acaroom.net/sites/default/files/images/blogs/angular_js.jpg"
categories:
- IT
tags: [Angular, JavaScript]
---

간만에 AngularJS로 개발을 할 일이 생겼다. 내가 만든 코드인데도 만든지 4년 가까운 시간이 지나니 AngularJS의 동작에 대하여 기억이 나지 않았다. 그래서 간단히 정리해본다.

## 문제
`ng-repeat`로 array를 반복하여 보여주고 있다. 이 array의 element는 Object type이다. 이 때 `filter`는 어떻게 동작하는가?

## 답
특별히 Object의 어떤 property를 참고할지 지정하지 않았다면 해당 Object의 모든 property에 대하여 필터링을 한다.

## 예시

```html
<div ng-init="friends = [{name:'John', phone:'555-1276'},
                         {name:'Mary', phone:'800-BIG-MARY'},
                         {name:'Mike', phone:'555-4321'},
                         {name:'Adam', phone:'555-5678'},
                         {name:'Julie', phone:'555-8765'},
                         {name:'Juliette', phone:'555-5678'}]"></div>

<label>Search: <input ng-model="searchText"></label>
<table id="searchTextResults">
  <tr><th>Name</th><th>Phone</th></tr>
  <tr ng-repeat="friend in friends | filter:searchText">
    <td>{{friend.name}}</td>
    <td>{{friend.phone}}</td>
  </tr>
</table>
<hr>
<!-- 아무 설정이 없을 때는 모든 property로 필터링 한다 -->
<label>Any: <input ng-model="search.$"></label> <br>
<!-- name property로 필터링 한다 -->
<label>Name only <input ng-model="search.name"></label><br>
<!-- phone property로 필터링 한다 -->
<label>Phone only <input ng-model="search.phone"></label><br>
<!-- phone property로 필터링 한다 -->
<table id="searchObjResults">
  <tr><th>Name</th><th>Phone</th></tr>
  <tr ng-repeat="friendObj in friends | filter:search">
    <td>{{friendObj.name}}</td>
    <td>{{friendObj.phone}}</td>
  </tr>
</table>

```

예시 출처: https://code.angularjs.org/1.6.10/docs/api/ng/filter/filter
