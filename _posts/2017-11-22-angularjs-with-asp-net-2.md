---
layout: single
title: ASP.NET 2.0와 함께 하는 AngularJS 개발 이야기
date: 2017-11-22 17:07:30.000000000 +09:00
type: post
header:
  teaser: "/assets/images/angularjs-with-aspnet.png"
  image: "/assets/images/angularjs-with-aspnet.png"
categories:
- IT
tags: [angularjs, ASP.NET]
---
# 0. 기묘한 조합

요즘 나는 회사에서 웹개발을 주로 하게 되었는데, Front-end는 [AngularJS]를, Back-end는 ASP.NET 2.0(Core 2 아니다)을 사용 중이다. 기묘한 조합이다. 이런 조합을 써서 개발한다는 이야기를 들어본 적이 없다. 심지어 구글링을 해봐도 이런 케이스는 나오지 않는다. 물론 누가 등 떠밀어서 하게 된 것은 아니고, .NET 2를 오랫동안 사용하고 있는 사이트에서 transpiler가 필요없는 Front-end Framework를 억지로 붙이려다 보니 이렇게 되었다. 덕분에 별것도 아닌 개발 하나씩 할 때마다 오만 삽질을 다 하고 있다. 이게 다 조금이라도 개발을 편하게 해보려던 나의 욕심이 불러일으킨 화이니, 기왕 이렇게 된 거... AngularJS와 .NET 2.0을 연결하는 법에 대하여 간단히 적어보겠다.

# 1. [AngularJS]

위에서는 거창하게 써두었지만 사실 별건 아니다. Back-end와 AJAX 통신을 할 때, get 방식은 늘 하던대로 적당히 넘기면 된다. 문제는 post 방식이다. header에 **Content-Type**을 **application/x-www-form-urlencoded**로 명시해야 한다. 아래는 factory의 working code 한조각이다.

```javascript
function getStayoutDetail(applySeq) {
    return $http({
        method: "POST",
        url: "/activity/stayout_apply_detail.aspx",
        dataType: 'json',
        data: {
            "seq": applySeq
        },
        headers: { "Content-Type": "application/x-www-form-urlencoded; charset=utf-8" }
    });
}
```

# 2. ASP.NET

이제 Back-end에서 AJAX 요청을 받아 처리해야 한다. 먼저 다음과 같이 새 웹 폼 하나를 만든다.

```csharp
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="stayout_apply_detail.aspx.cs" Inherits="activity_stayout_apply_detail" %>
```

code behind page는 이렇게 만든다.

```csharp
using System;
using System.Collections.Generic;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Newtonsoft.Json;

public partial class activity_stayout_apply_detail : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        string rawRequest = Request.Form[0];

        requestJson json = JsonConvert.DeserializeObject<requestJson>(rawRequest);
        string seq = json.seq;

        StayoutBiz stayoutBiz = new StayoutBiz();
        string result = stayoutBiz.getStayoutDetail(seq);
        Response.Write(result);
    }

    public class requestJson
    {
        public string seq = "";
    }
}
```

AJAX 요청 날아오는 모양새로 class를 하나 만들고(여기서는 requestJson), [Newtonsoft]의 은총을 받아 방금 만든 class의 형태로 요청을 받는다. 그리고 biz단으로 데이터를 날린다. 받아오는 데이터는,

```csharp
string json = JsonConvert.SerializeObject(ds.Tables);
```

위와 같이 json 형태로 만들어서 **Response.Write(result);** 로 Front-end에 보낸다.

# 3. 끝

다 써놓고 나니 별 것도 아니네. 이제 잘 돌아간다. 신난다!

[AngularJS]: https://angularjs.org/
[Newtonsoft]: https://www.newtonsoft.com/json