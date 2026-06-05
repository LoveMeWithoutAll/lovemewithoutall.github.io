---
layout: single
title: Extracting a Specific Parameter from a URL Query String with a Regular Expression
date: 2018-12-06 20:05:30.000000000 +09:00
type: post
header:
    teaser: "https://pbs.twimg.com/profile_images/851635058339336192/ZI5FHI72_400x400.jpg"
    image: "https://pbs.twimg.com/profile_images/851635058339336192/ZI5FHI72_400x400.jpg"
categories:
- IT
tags: [JavaScript]
---

Internet Explorer doesn't support `URLSearchParams`. That makes dealing with URL query strings a hassle.

Adding a `polyfill` or writing a separate function to pull out the query string are both good approaches, but depending on the situation there are times when you just want to grab the `value` of a specific `key` in a single line of code.

So I put together some code that extracts the value of a specific key from the query string using a simple regular expression and a `replace` statement.

```javascript
// Change 'key' in the code below to pull out the value you want
var wantedParam = /[a-zA-Z0-9_-]*/.exec(window.location.search)[0].replace("&key=", "").replace("key=", "");
```

It's a bit clunky, but it works fine.

If you want to keep it even simpler, this works too.

```javascript
// Change 'key' in the code below to pull out the value you want
window.location.search.match(/key=([^&]*)/)[1];
```

Since it's hardcoded you might get some flak for it, so use it with care.

* Reference: [URLSearchParams browser compatibility](https://developer.mozilla.org/ko/docs/Web/API/URLSearchParams#%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80_%ED%98%B8%ED%99%98%EC%84%B1)
