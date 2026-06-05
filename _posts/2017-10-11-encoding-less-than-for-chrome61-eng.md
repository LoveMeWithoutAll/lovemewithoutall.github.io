---
layout: single
title: Fixing the issue where resource URLs containing the < character get blocked in Chrome 61
date: 2017-10-11 21:07:30.000000000 +09:00
header:
  teaser: "/assets/images/chrome-encoding.png"
type: post
categories:
- IT
tags: [encoding, URI, Chrome]
---

Among the services I operate, there's an old-fashioned site built with `ASP` and `Visual Basic`.
One day I opened the site in `google chrome`, and one corner of the screen was empty.

The console in the developer tools showed this message:
```javascript
[Deprecation] Resource requests whose URLs contained both removed whitespace (`\n`, `\r`, `\t`) characters and less-than characters (`<`) are blocked. Please remove newlines and encode less-than characters from places like element attribute values in order to load these resources. See https://www.chromestatus.com/feature/5735596811091968 for more details.
```

When I looked into it, the error was occurring precisely in the code that loads another site via an iframe and displays it on the screen.
The cause was one of the changes in `chrome61`: [Block resources whose URLs contain '\n' and '<' characters](https://developers.google.com/web/updates/2017/08/chrome-61-deprecations#block_resources_whose_urls_contain_n_and_wzxhzdk2_characters).
If a `<` is included in the `src attribute` of an `iframe`, it all gets blocked.
The simplest solution would have been to not put `<` in the url in the first place, but since that wasn't an option in this case, I had to take a somewhat messy measure. Encoding.

```javascript
<script type="text/javascript">
    (function(){
        var url = document.getElementById("iframeId").src;            
        var result = encodeURI(decodeURI(url));    
        document.getElementById("iframeId").src = result;
    }());
</script>
```

You just need to put the code above at the bottom of the body tag. You have to run `decodeURI` first, then `encodeURI`, for it to work. Not knowing this, I ended up wasting quite a bit of my life.
