---
layout: single
title: Using the HTTP Post method in ASP.NET 2.0
date: 2018-07-02 11:45:30.000000000 +09:00
type: post
header:
    teaser: "http://wakeupandcode.com/wp-content/uploads/2016/03/asp-net-logo.png"
    image: "http://wakeupandcode.com/wp-content/uploads/2016/03/asp-net-logo.png"
categories:
- IT
tags: [ASP.NET, HTTP]
---

# 0. The problem

I was trying to communicate asynchronously with an [ASP.Net 2.0] backend server from the frontend using [HTTP request methods](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods), and I ran into a problem.

## The **Post method** doesn't work!

I was frustrated for a moment, but I found a way to solve it. A way to use the Post method!

# 1. Tech stack

1. Backend framework: [ASP.Net 2.0]
1. Frontend framework: [Vue.js]
1. HTTP client library for frontend: [axios]
1. JSON framework for .NET: [Json.NET]

# 2. Client side

Be sure to specify **dataType** and **headers** as shown below. This is the most important part.

You can use either [axios] or `XMLHttpRequest`.

```javascript
// axios example
return axios({
    method: 'POST',
    url: '/path/class_name',
    dataType: 'json', // required
    headers: { 'Content-Type': 'application/x-www-form-urlencoded; charset=utf-8' }, // required
    data: { // data to be included in the JSON to send
    'param1': param1,
    'param2': param2
    }
})
```

```javascript
// XMLHttpRequest example
var xhr = new XMLHttpRequest();
xhr.onload = function() {
    if (xhr.status === 200 || xhr.status === 201) {
        console.log(xhr.responseText);
    } else {
        console.error(xhr.responseText);
    }
};
xhr.open('POST', '/path/class_name');
xhr.setRequestHeader('Content-Type', "application/x-www-form-urlencoded; charset=utf-8");
xhr.send(JSON.stringify(
                        data: { // data to be included in the JSON to send
                                'param1': param1,
                                'param2': param2
                            }
                        )
        );
```

# 3. Server side

The JSON sent from the client comes in as the first element of the Request.Form array.

```csharp
using System;
using System.Collections.Generic;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public partial class path_class_name : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        string rawRequest = Request.Form[0]; // receive the Post request as a string
        JObject surveyJObj = JObject.Parse(rawRequest); // convert it to a JSON object.
        // subsequent work...        
    }
}
```

# 4. Wrapping up

When sending an HTTP request, the Get method works even if you don't specify dataType and headers.

```csharp
string data1 = Request["data1"]; // how to receive a get parameter
```


But for things like logins or for passing hierarchical data, you need the Post method. Even after googling, I couldn't find a decent answer and was getting frustrated, but surprisingly I found the answer in a post I had written on my own blog a while back. That just goes to show how few people use [ASP.Net 2.0] anymore. Anyway, I might need this again later, so I wrote it up. I really want to stop using [ASP.Net 2.0].

## The end!

[Vue.js]: https://vuejs.org/
[ASP.Net 2.0]: https://msdn.microsoft.com/ko-kr/library/aa479401.aspx
[axios]: https://github.com/axios/axios
[Json.NET]: https://www.newtonsoft.com/json
