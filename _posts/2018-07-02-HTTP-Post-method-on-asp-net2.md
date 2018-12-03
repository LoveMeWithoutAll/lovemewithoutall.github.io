---
layout: single
title: ASP.NET2에서 HTTP Post method 사용하기
date: 2018-07-02 11:45:30.000000000 +09:00
type: post
header:
    teaser: "http://wakeupandcode.com/wp-content/uploads/2016/03/asp-net-logo.png"
    image: "http://wakeupandcode.com/wp-content/uploads/2016/03/asp-net-logo.png"
categories:
- IT
tags: [ASP.NET, HTTP]
---

# 0. 문제

프론트엔드에서 [HTTP request methods](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods)로 [ASP.Net 2.0] 백엔드 서버와 비동기 통신을 하려 하는데, 문제가 발생했다.

## **Post method**가 안된다!

잠시 좌절했지만 이를 해결할 방법을 찾아냈다. Post method를 사용할 수 있는 방법을!

# 1. Tech stack

1. Backend framework: [ASP.Net 2.0]
1. Frontend framework: [Vue.js]
1. HTTP client library for frontend: [axios]
1. JSON framework for .NET: [Json.NET]

# 2. Client side

반드시 **dataType**과 **headers**를 아래와 같이 명시해준다. 여기가 가장 중요하다.

[axios], `XMLHttpRequest` 둘 중 무엇을 써도 좋다.

```javascript
// axios 예제
return axios({
    method: 'POST',
    url: '/경로/class_name',
    dataType: 'json', // 필수
    headers: { 'Content-Type': 'application/x-www-form-urlencoded; charset=utf-8' }, // 필수
    data: { // 보낼 JSON에 들어갈 data
    'param1': param1,
    'param2': param2
    }
})
```

```javascript
// XMLHttpRequest 예제
var xhr = new XMLHttpRequest();
xhr.onload = function() {
    if (xhr.status === 200 || xhr.status === 201) {
        console.log(xhr.responseText);
    } else {
        console.error(xhr.responseText);
    }
};
xhr.open('POST', '/경로/class_name');
xhr.setRequestHeader('Content-Type', "application/x-www-form-urlencoded; charset=utf-8");
xhr.send(JSON.stringify(
                        data: { // 보낼 JSON에 들어갈 data
                                'param1': param1,
                                'param2': param2
                            }
                        )
        );
```

# 3. Server side

Client에서 전송받은 JSON은 Request.Form 배열의 첫번째 요소로 들어온다.

```csharp
using System;
using System.Collections.Generic;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public partial class 경로_class_name : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        string rawRequest = Request.Form[0]; // Post request를 문자열로 받는다
        JObject surveyJObj = JObject.Parse(rawRequest); // JSON object로 바꾼다.
        // 후속 작업...        
    }
}
```

# 4. 마무리

HTTP request를 보낼 때, dataType과 headers를 명시하지 않아도 Get method는 된다. 

```csharp
string data1 = Request["data1"]; // get parameter 받는 방법
```


하지만 로그인이라든가, 계층화된 데이터를 전달하기 위해서는 Post method가 필요하다. 그러나 구글링을 해보아도 신통한 답변이 없어 좌절하고 있었는데, 놀랍게도 내 블로그에 내가 예전에 써놓은 글에서 답을 찾을 수 있었다. 그만큼 이제 [ASP.Net 2.0]을 쓰는 사람이 없다는거다. 아무튼 나중에 또 써먹을지 모르니 정리해보았다. [ASP.Net 2.0]는 그만 쓰고 싶다.

## 끝!

[Vue.js]: https://vuejs.org/
[ASP.Net 2.0]: https://msdn.microsoft.com/ko-kr/library/aa479401.aspx
[axios]: https://github.com/axios/axios
[Json.NET]: https://www.newtonsoft.com/json
