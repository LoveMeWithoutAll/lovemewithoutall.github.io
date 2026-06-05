---
layout: single
title: Sending a POST request to C# with Python's requests module
date: 2021-01-21 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://user-images.githubusercontent.com/62210/29321053-746e3d0c-8196-11e7-99d2-94d6dc8afdfe.png"
    image: "https://user-images.githubusercontent.com/62210/29321053-746e3d0c-8196-11e7-99d2-94d6dc8afdfe.png"
categories:
- IT
tags: [python, c#]
---

When you send JSON data to C# as a POST request using Python's [Requests module](https://requests.readthedocs.io/en/master/), you may run into a 500 error like the one below.

```log
Newtonsoft.Json.JsonReaderException: Unexpected character encountered while parsing number
```

The cause is the JSON. To send JSON data via POST with the Requests module, you need to do it like this:

```python
data = {
  "id": id,
  "no": no
}

headers = {'Content-Type': 'application/x-www-form-urlencoded; charset=utf-8'}

response = requests.post(url="http://xxx.com/aaa/api/aaa.aspx", json=data, headers=headers)
```

When calling `post`, you have to send the data through the `json` parameter so that C#'s JSON library doesn't throw a parsing error!
