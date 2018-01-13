---
title: Android获取以及修改WebView的UserAgent
date: 2017-11-06 18:57:07
tags:
---
User Agent中文名为`用户代理`，是Http协议中的一部分，属于头域的组成部分，User Agent也简称UA。
>是什么：它是一个特殊字符串头，是一种向访问网站提供你所使用的浏览器类型及版本、操作系统及版本、浏览器内核、等信息的标识。
>什么用：通过这个标识，用户所访问的网站可以显示不同的排版从而为用户提供更好的体验或者进行信息统计；例如用手机访问谷歌和电脑访问是不一样的，这些是谷歌根据访问者的UA来判断的。

对于Android开发者，如何获取UserAgent:
1.首先在你的Activity对应的xml布局文件中加入WebView控件：
```xml
<WebView  
       android:id="@+id/id_wv_ua"  
       android:layout_width="match_parent"  
       android:layout_height="match_parent"></WebView>  
```
2.在Activity中加如下代码：

```java
public class Ano extends Activity {  
  
    private WebView webview;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_ano);  
  
        webview = (WebView) this.findViewById(R.id.id_wv_ua);  
        // 得到WebSettings对象  
        WebSettings settings = webview.getSettings();  
        // 如果访问的页面中有JavaScript，则WebView必须设置支持JavaScript，否则显示空白页面  
        webview.getSettings().setJavaScriptEnabled(true);  
        // 获取到UserAgentString  
        String userAgent = settings.getUserAgentString();  
        // 打印结果  
        Log.i("TAG", "User Agent:" + userAgent);  
    }  
}  
```

最后，如果你想让WebView能够加载来自网络的页面，别忘了在AndroidManifest.xml文件中加入INTERNET权限。
```xml
<uses-permission android:name="android.permission.INTERNET" />    
```
输出结果，以下就是userAgent的内容：
```
I/TAG: User Agent:Mozilla/5.0 (Linux; Android 4.4.4; SAMSUNG-SM-N900A Build/tt) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/33.0.0.0 Mobile Safari/537.36
```

修改：
为了便于WEB端统计分析，需要将APP的 user-agent 作特征标记，所以搜索了一下Android对webview的User-Agent设置方法，具体如下：
```java
// 修改ua使得web端正确判断    
String ua = webview.getSettings().getUserAgentString();    
webview.getSettings().setUserAgentString(ua+"; 自定义标记");  
```
```java
WebSettings settings = webview.getSettings();    
        settings.setUserAgentString("app/XXX");//添加UA,  “app/XXX”：是与h5商量好的标识，h5确认UA为app/XXX就认为该请求的终端为App    
        settings.setJavaScriptEnabled(true);    
    
        //设置参数    
        settings.setBuiltInZoomControls(true);    
        settings.setAppCacheEnabled(true);// 设置缓存    
    
        webview.setWebChromeClient(new WebChromeClient());    
    
        webview.loadUrl(loadurl);    
```

User Agent是Http协议中的一部分，属于头域的组成部分，User Agent也简称UA。是一种向访问网站提供你所使用的浏览器类型、操作系统、浏览器内核等信息的标识。通过这个标识，用户所访问的网站可以显示不同的排版从而为用户提供更好的体验或者进行信息统计；例如用手机访问谷歌和电脑访问是不一样的，这些是谷歌根据访问者的UA来判断的。
android 设置UA:
```java
WebSettings s = mSettings;  
s.setUserAgentString(ua);
```

ua 信息如下：
>1. windows

`Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Maxthon/%s Chrome/30.0.1551.0 Safari/537.36 `

>2.  iphone

```
Mozilla/5.0 (iPhone; CPU iPhone OS 5_1 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko) Version/5.1 Mobile/9B179 Safari/537.4 Maxthon/%s  
```

>3. iPad

```
Mozilla/5.0 (iPad; CPU OS 5_1 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko) Version/5.1 Mobile/9B176 Safari/537.4 Maxthon/%s  
```
>4.

`//如果ua为null或者为空字符串，系统会自动生成androidUA  `
