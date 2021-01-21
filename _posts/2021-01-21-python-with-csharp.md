---
layout: single
title: Python requests 모듈로 c#에 post request 보내기
date: 2021-01-21 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://user-images.githubusercontent.com/62210/29321053-746e3d0c-8196-11e7-99d2-94d6dc8afdfe.png"
    image: "https://user-images.githubusercontent.com/62210/29321053-746e3d0c-8196-11e7-99d2-94d6dc8afdfe.png"
categories:
- IT
tags: [python, c#]
---

Python의 [Requests 모듈](https://requests.readthedocs.io/en/master/)로 C#에 json 데이터를 post request로 보낼 때 아래와 같은 500 에러를 맞이할 수 있다. 

```log
Newtonsoft.Json.JsonReaderException: Unexpected character encountered while parsing number
```

원인은 json 때문이다. Requests 모듈에서 json 데이터를 post로 보내려면 아래와 같이 해줘야 한다.

```python
data = {
  "id": id,
  "no": no
}

headers = {'Content-Type': 'application/x-www-form-urlencoded; charset=utf-8'}

response = requests.post(url="http://xxx.com/aaa/api/aaa.aspx", json=data, headers=headers)
```

`post`를 할 때 `json` 파라미터로 데이터를 보내야 C#의 json 라이브러리가 파싱 오류를 내지 않는다!
