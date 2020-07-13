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

Let's launch a custom error page when the network is down in the Android WebView App.

First, When an error occurs, write code to move to the html page embedded in the app.

```java
// WebViewActivity
public class WebViewActivity extends AppCompatActivity {
    // ...
    public static WebView wv;

    @Override
	protected void onCreate(Bundle savedInstanceState) {
        // ...
		wv.setWebViewClient(wvclient);
        // ...
	}

    WebViewClient wvclient = new WebViewClient() {
        // ...

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
}
```

Second, create an error page. Pay attention to the location of the files.

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
        <font id="error-code" color="#e80000"></font> Error!
    </p>
    <ul style="margin:25px auto; width:75%; color:#47597f; font-size:14px; background-color:#e5eaf5; padding:15px 25px; line-height:150%; list-style:none; border:1px solid #cfd8ec;">
        <li ><span id="error-guide"></span>Please retry!</li>
    </ul>
    <p style="background-color:#2357ac; padding:10px; margin:0 auto; width:200px; text-align:center;">
        <a style="font-size:13px; color:#FFF; text-decoration:none;" href="mailto:name@mail.com">Send mail to Admin</a>
    </p>
</div>
</body>

<script>
    var urlParams = new URLSearchParams(window.location.search);

    var errorCode = urlParams.get('errorCode');
    var errorDescription = urlParams.get('errorDescription');

    document.getElementById("error-code").innerHTML = errorDescription;
    
    // Write the appropriate guidance text according to the error code
    switch(errorCode) {
        case '-2': // Case: net::ERR_INTERNET_DISCONNECTED
            document.getElementById('error-guide').innerHTML = 'Check Wi-Fi and Network connection';
            break;
    }
</script>

</html>
```

Write the appropriate guidance text according to the error code.

End!

# last update: 2020-07-13