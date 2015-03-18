title: android WebView组件
date: 2015/3/17 13:17:10 
tags:
- 技术
- android_UI
categories: android
---
##WebView使用

    webView = (WebView) findViewById(R.id.webView); 
    webView.getSettings().setJavaScriptEnabled(true); 
    webView.setWebViewClient(new WebViewClient(){ 
    @Override 
    public boolean shouldOverrideUrlLoading(WebView view, String url) { 
    view.loadUrl(url);//根据传入的参数再去加载新的网页 
    return true;//当前webview可以处理新的请求不用打开系统浏览器 
    } 
    }); 
    webView.loadUrl("http://www.1616.net");

##WebView与Javascript
WebView不但可以运行一段HTML代码，而且还有一个最重要的特点，就是WebView可以同Javascript互相调用。因此我们可以用HTML和Javascript来编写Android应用。这里需要通过`addJavascriptInterface(Object obj,String interfaceName)`方法将一个Java对象绑定到一个Javascript对象中，Javascript对象名就是interfaceName,作用域是Global,这样便可以扩展Javascript的API，获取Android的数据
