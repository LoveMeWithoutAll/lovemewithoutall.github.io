---
layout: single
title: ERR_CACHE_OPERATION_NOT_SUPPORTED on pdf.js
date: 2018-07-13 13:45:30.000000000 +09:00
type: post
header:
    teaser: "https://mozilla.github.io/pdf.js/images/logo.svg"
    image: "https://mozilla.github.io/pdf.js/images/logo.svg"
categories:
- IT
tags: [javascript, pdf.js, chrome]
---

# 현상

[pdf.js]를 사용하는 서비스가 있는데, pdf viewer에서 간헐적(!)으로 이런 오류가 뜬다. [Google Chrome]에서만 그렇다.

```
net::ERR_CACHE_OPERATION_NOT_SUPPORTED
```

![ERR_CACHE_OPERATION_NOT_SUPPORTED](https://user-images.githubusercontent.com/510750/31525532-e37b3e40-af8e-11e7-9108-0ae568f6ecef.png)

# 환경

* Web Browser: Chrome 67
* pdf.js: v1.9.426(stable)

# 원인

[pdf.js]가 PDF파일을 다운받을 때, worker에서 [Range request]를 보내는데, 이를 [Google Chrome] 61버전부터 제대로 처리하지 못하기 때문이라 추정된다.


# 조치

[Range request]를 안보내면 된다. **pdfjs/web/view.html**에 하기 스크립트를 추가해준다.

```html
<script>
  PDFJS.disableWorker = true;
  PDFJS.disableRange = true;
</script>
```

이제 잘 돌아가리라 믿는다.
끝!

## 참고
https://github.com/mozilla/pdf.js/issues/9022

[pdf.js]: https://mozilla.github.io/pdf.js/
[Google Chrome]: https://www.google.com/chrome/
[Range request]: https://developer.mozilla.org/ko/docs/Web/HTTP/Range_requests