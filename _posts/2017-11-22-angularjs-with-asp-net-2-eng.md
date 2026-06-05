---
layout: single
title: A Tale of AngularJS Development with ASP.NET 2.0
date: 2017-11-22 17:07:30.000000000 +09:00
type: post
header:
  teaser: "/assets/images/angularjs-with-aspnet.png"
  image: "/assets/images/angularjs-with-aspnet.png"
categories:
- IT
tags: [angularjs, ASP.NET]
---
## 0. A Strange Combination

These days I've mainly been doing web development at work, using [AngularJS] for the front-end and ASP.NET 2.0 (not Core 2) for the back-end. It's a strange combination. I've never heard of anyone developing with a pairing like this. A bizarre hybrid of outdated tech... I even tried Googling, and this kind of case just doesn't come up. Of course, nobody twisted my arm into doing it...

## 1. My Own Circumstances

Due to the policy of the site I work at, there was a constraint that the back-end absolutely had to use .NET 2, and I also had to be able to collaborate with colleagues who were encountering a front-end framework for the very first time. In a situation like this, you can't use a framework with its own file extension like [Vue.js] or [React], nor can you choose a framework where a transpiler is practically mandatory, like [Angular]. [Backbone.js] and [ember] don't seem to be used much anymore... In the end, I chose [AngularJS], which doesn't run into any of the problems above. ~~Honestly, it's because I'm familiar with [AngularJS]~~

## 2. The Agony

As a result, every time I do even the most trivial bit of development, I end up doing all kinds of pointless troubleshooting. This is the consequence of my own desire to make development just a little easier, so since it's come to this... let me write briefly about how to connect [AngularJS] and .NET 2.0.

## 3. [AngularJS]

I made it sound grand above, but it's really nothing much. When doing AJAX communication with the back-end, for the GET method you can just pass things along the usual way. The problem is the POST method. You have to explicitly set the **Content-Type** in the header to **application/x-www-form-urlencoded**. Below is a snippet of working code from a factory.

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

## 4. ASP.NET

Now we need to receive and handle the AJAX request on the back-end. First, create a new web form like this.

```csharp
<%@ Page Language="C#" AutoEventWireup="true" CodeFile="stayout_apply_detail.aspx.cs" Inherits="activity_stayout_apply_detail" %>
```

The code-behind page is created like this.

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

You create a class in the shape of the incoming AJAX request (here it's requestJson), and with the blessing of [Newtonsoft] you receive the request in the form of the class you just made. Then you send the data off to the biz layer. As for the data that comes back,

```csharp
string json = JsonConvert.SerializeObject(ds.Tables);
```

you turn it into JSON form as shown above, and send it to the front-end with **Response.Write(result);**.

## 5. The End

Now that I've written it all out, it's really nothing much. It works fine now. Exciting!

[AngularJS]: https://angularjs.org/
[Newtonsoft]: https://www.newtonsoft.com/json
[React]: https://reactjs.org/
[Vue.js]: https://vuejs.org/
[Angular]: https://angular.io/
[Backbone.js]: http://backbonejs.org/
[ember]: https://www.emberjs.com/
