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

# Symptom

There's a service that uses [pdf.js], and the PDF viewer intermittently(!) throws this error. It only happens on [Google Chrome].

```
net::ERR_CACHE_OPERATION_NOT_SUPPORTED
```

![ERR_CACHE_OPERATION_NOT_SUPPORTED](https://user-images.githubusercontent.com/510750/31525532-e37b3e40-af8e-11e7-9108-0ae568f6ecef.png)

# Environment

* Web Browser: Chrome 67
* pdf.js: v1.9.426(stable)

# Cause

When [pdf.js] downloads a PDF file, the worker sends a [Range request]. This is presumably because [Google Chrome] hasn't handled this properly since version 61.


# Fix

You just need to avoid sending the [Range request]. Add the following script to **pdfjs/web/view.html**.

```html
<script>
  PDFJS.disableWorker = true;
  PDFJS.disableRange = true;
</script>
```

I trust it'll work fine now.
Done!

## References
https://github.com/mozilla/pdf.js/issues/9022

[pdf.js]: https://mozilla.github.io/pdf.js/
[Google Chrome]: https://www.google.com/chrome/
[Range request]: https://developer.mozilla.org/ko/docs/Web/HTTP/Range_requests
