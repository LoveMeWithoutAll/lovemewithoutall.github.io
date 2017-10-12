---
layout: single
title: 크롬61에서 리소스 url에 < 기호 포함시 blocking 당하는 문제 해결
date: 2017-10-11 21:07:30.000000000 +09:00
header:
  teaser: "/assets/images/chrome-encoding.png"
type: post
categories:
- IT
tags: [encoding, URI, Chrome]
---

운영하는 서비스 중 `ASP`와 `Visual Basic`으로 만들어진 구닥다리 사이트가 하나 있다. 
어느 날 `google chrome`에서 사이트를 열어보니 화면 한 구석이 휑하다.

개발자 도구의 콘솔창에는 이런 메시지가 뜬다.
```javascript
[Deprecation] Resource requests whose URLs contained both removed whitespace (`\n`, `\r`, `\t`) characters and less-than characters (`<`) are blocked. Please remove newlines and encode less-than characters from places like element attribute values in order to load these resources. See https://www.chromestatus.com/feature/5735596811091968 for more details.
```

확인해보니 딱 iframe으로 다른 사이트를 불러와 화면에 띄워주는 코드에서 오류가 발생하고 있었다.
원인은 `chrome61`의 변경사항 중 [Block resources whose URLs contain '\n' and '<' characters](https://developers.google.com/web/updates/2017/08/chrome-61-deprecations#block_resources_whose_urls_contain_n_and_wzxhzdk2_characters)이었다.
`iframe`의 `src attribute`에 `<`가 들어가면 모두 block 당한다.
애시당초 url에 `<`를 안 넣으면 될 일이지만, 부득이한 경우라 다소 지저분한 조치를 취해야했다. 인코딩이다.

```javascript
<script type="text/javascript">
    (function(){
        var url = document.getElementById("iframeId").src;            
        var result = encodeURI(decodeURI(url));    
        document.getElementById("iframeId").src = result;
    }());
</script>
```

위 코드를 body 태그 하단에 넣어주면 된다. `decodeURI`를 먼저 한 후, `encodeURI`를 해야 동작한다. 이걸 몰라서 인생을 제법 많이 낭비해야 했다.