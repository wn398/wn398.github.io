title: Intent使用
date: 2015/3/16 15:12:10 
tags:
- 技术
- android

categories: android
---
##Intent
>Intent是程序中各组件之间进行交互的一种重要方式，不仅可以指明当前组件想要执行的活动，还可以在不同组件之间传递数据，Intent一般用于**启动活动**，**启动服务**，以及**发送广播**等场景

###显式Intent:
>调用构造函数`Intent(Context packeageContext,Class<?> cls)`，Context提供一个启动活动的上下文，Class指定想要启动的目标活动。

###隐式Intent:
>它并不明确指出我们想要启动哪个活动，而是指定了一系列更为抽象的action和category等信息，然后交由系统去分析这个Intent,并帮我们找出合适的活动去启动。每个Intent中只能指定一个action,但却能指定多个category.
    
>intent-filter中的action指的是这个activity可以响应那个action,其中的名字可以是系统的，也可以是我们自定义的，但是如果是自定义的，我们要启动这个活动就要 new Intent("自定义字符串")。
    
>使用隐式的Intent,不仅可以启动我们自己程序内的活动，还可以启动其他程序的活动，这使得Andriod多个应用程序之间的功能共享成为了可能。例如：我们可以用系统浏览器打开一个网页：
>
	Intent intent = new Intent(Intent.Action_VIEW);
	intent.setData(Uri.parse("http://www.baidu.com"));
	startActivity(intent);
 
>`setData()`;方法，接收一个Uri对象，主要用于指定当前Intent正在操作的数据，而这些数据通常都是以字符串的形式传入到`Uri.parse()`方法中解析产生的。与此对应，我们可以在intent-filter标签中再配置一个data标签，用于更精确指定当前活动响应什么类型的数据。 

>data标签中主要配置以下内容：`android:schema 数据协议 android:host`，`android:port`,`android:path`,`android:mimeType`,只有data标签中指定的内容和Intent中携带的Data完全一致时，当前活动才能够响应该Intent.除了http协议外，我们还可以指定很多其他协议，比如`geo`表示显示地理位置，`tel`表示拨打电话。
如调用系统拨号界面：

		Intent intent = new Intent(Intent.ACTION_DIAL);
		intent.setData(Uri.parse("tel:10086"));
		startActivity(intent);

###向下一个活动传递数据：
>只需要调用Intent的一系列putExtra（）方法存入数据，在启动的活动中用：
>
	Intent intent = getIntent();
	String data = intent.getStringExtra("key");

###返回数据给上一个活动：
>返回上一个活动只需要按一下Back键就可以了。我们可以用`startActivityForResult()`方法来启动一个活动。
>它接收两个参数，第一个参数是Intent,第二个参数是请求码，用于在之后的回调中判断数据的来源。只要独一无二就可以。

###在被请求的活动中要使用setResult();来返回数据。
>这个方法是专门用于向上一个活动返回数据的，它接收两个参数

>第一个参数用于向上一个活动返回处理结果，一般只使用`RESULT_OK`或`RESULT_CANCELED`这两个值

>第二个参数则是把带有数据的Intent传递回去。

因为要处理活动返回来的结果，所以要重载`onActivityResult()`这个方法。它有三个参数

>第一个参数是requestCode，即我们在启动活动时传入的请求码

>第二个参数resultCode，即我们在返回数据时传入的处理结果

>第三个参数data，即带有数据的Intent.首先我们要通过requestCode判断数据来源。防止用户通过按下Back键回到上一个活动而没有把数据传给上一个活动，所以数据的处理就放在onBackPressed（）方法中去做处理。用户按下Back键就会去执行onBackPressed()方法中的代码。
