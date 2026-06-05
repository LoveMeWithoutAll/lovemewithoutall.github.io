---
layout: single
title: Serving JSON on IIS6
date: 2017-12-04 19:07:30.000000000 +09:00
type: post
header:
  teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c9/JSON_vector_logo.svg/1200px-JSON_vector_logo.svg.png"
  image: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c9/JSON_vector_logo.svg/1200px-JSON_vector_logo.svg.png"
categories:
- IT
tags: [IIS6, JSON]
---

I ran into a situation where I needed to download JSON from IIS 6.0. But it wouldn't work. I kept getting this error message.

```html
HTTP Error 404 - File or directory not found.
```

The cause is that IIS6 hadn't been configured to allow JSON downloads. You can fix it using [this link](https://support.microsoft.com/ko-kr/help/326965/iis-6-0-does-not-serve-unknown-mime-types). Add the following to the MIME types.

```csharp
Extension: .json
MIME type: application/json
```

But sometimes even this doesn't work. That's because the changed setting hasn't been applied to IIS yet. Open CMD and type this.

```cmd
iisreset
```

Now it works!
