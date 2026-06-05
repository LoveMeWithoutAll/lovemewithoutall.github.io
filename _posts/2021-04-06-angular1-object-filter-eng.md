---
layout: single
title: The filter in AngularJS
date: 2021-04-06 13:46:30.000000000 +09:00
type: post
header:
    teaser: "https://acaroom.net/sites/default/files/images/blogs/angular_js.jpg"
    image: "https://acaroom.net/sites/default/files/images/blogs/angular_js.jpg"
categories:
- IT
tags: [Angular, JavaScript]
---

After a long while, I had a reason to do some development with AngularJS. Even though it was code I wrote myself, almost 4 years had passed since I made it, so I couldn't remember how AngularJS behaves. So I'm putting together a quick summary.

## The Problem
I'm using `ng-repeat` to iterate over an array and display it. The elements of this array are of Object type. In this case, how does `filter` behave?

## The Answer
If you don't specifically designate which property of the Object to reference, it filters against all properties of that Object.

## Example

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
<!-- When there is no setting, it filters by all properties -->
<label>Any: <input ng-model="search.$"></label> <br>
<!-- Filters by the name property -->
<label>Name only <input ng-model="search.name"></label><br>
<!-- Filters by the phone property -->
<label>Phone only <input ng-model="search.phone"></label><br>
<!-- Filters by the phone property -->
<table id="searchObjResults">
  <tr><th>Name</th><th>Phone</th></tr>
  <tr ng-repeat="friendObj in friends | filter:search">
    <td>{{friendObj.name}}</td>
    <td>{{friendObj.phone}}</td>
  </tr>
</table>

```

Example source: https://code.angularjs.org/1.6.10/docs/api/ng/filter/filter
