---
layout: single
title: Android Webview custom error page
date: 2018-05-30 12:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Android]
---

Android 웹앱을 개발 중이다. 네트워크가 끊겼을 경우, 간단한 에러 페이지를 띄워보았다.

첫째, 에러가 발생할 경우, 앱에 내장된 html 페이지로 이동하는 코드를 작성한다.
```java
// WebViewActivity
WebChromeClient wcclient = new WebChromeClient() {
    @Override
        public void onReceivedError(WebView view, int errorCode, String description, String failingUrl){
            Log.e("error","ReceivedError on WebView. ERROR CODE is " + errorCode);
            Log.e("error","description is " + description);
            Log.e("error","failingUrl is " + failingUrl);
            try{
                view.loadUrl("file:///android_asset/www/error.html?errorCode=" + errorCode + "&errorDescription=" + description);
            }catch  (Exception e) {
                Log.e("error", e.toString());
            }
        }
}
```

둘째, 에러 페이지를 작성한다. 파일의 위치에 주의해야 한다.
```html
<!-- app/src/main/assets/www/error.html  -->
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
</head>

<body>
<div style="width:90%; margin:0 auto;">
    <h1 style="text-align:center; font-size:30px; color:#292929; margin-bottom:45px;">Error</h1>
    <p style="font-size:14px; color:#292929; font-weight:bold;text-align:center; margin-bottom:25px;">
        <font id="error-code" color="#e80000"></font> 오류가 발생했습니다.
    </p>
    <ul style="margin:25px auto; width:75%; color:#47597f; font-size:14px; background-color:#e5eaf5; padding:15px 25px; line-height:150%; list-style:none; border:1px solid #cfd8ec;">
        <li ><span id="error-guide"></span>다시 시도해보시고 그래도 오류가 발생할 경우에는 아래 주소로 메일을 보내주세요.</li>
    </ul>
    <p style="background-color:#2357ac; padding:10px; margin:0 auto; width:200px; text-align:center;">
        <a style="font-size:13px; color:#FFF; text-decoration:none;" href="mailto:메일@주소">담당자에게 메일 보내기</a>
    </p>
</div>
</body>

<script>
    var urlParams = new URLSearchParams(window.location.search);

    var errorCode = urlParams.get('errorCode');
    var errorDescription = urlParams.get('errorDescription');

    document.getElementById("error-code").innerHTML = errorDescription;
    
    // 오류 코드에 맞춰 적절히 안내 문구를 작성해준다.
    switch(errorCode) {
        case '-2': // net::ERR_INTERNET_DISCONNECTED 오류인 경우
            document.getElementById('error-guide').innerHTML = 'Wi-Fi 및 데이터 통신을 확인해주세요. ';
            break;
    }
</script>

</html>
```
에러 코드에 맞춰 적절히 안내 문구를 작성해준다.