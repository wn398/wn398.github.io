title: BroadcastReceiver-广播接收器
date: 2015/3/12 12:44:22 
tags:
- 技术
- android

categories: android
---

##广播
1. 标准广播：所有广播接收器都会在同一时刻接收到这条广播信息，它们之前没有任何先后顺序
2. 有序广播：同步执行的广播，在广播发出后，同一时刻只会有一个广播接收器能够收到这条广播信息。在这个广播接收器执行完后，广播会继续传递。所有接收的广播接收器是有先后顺序的，优先级高的先收到，并且前面的广播接收器还可以拦截正在传递的广播，这样后面的广播接收器就无法收到广播消息了。

##广播注册   
>一般有2种，在代码中注册和在AndroidManifest.xml中注册，其中前者就是动态注册，后者为静态注册。

###动态注册：
创建广播接收器：只需要新建一个类，让它继承自BroadcastReciever,并重写父类的onReceive()方法就行了
	
	IntentFilter intentFilter = new IntentFilter();
	intentFilter.addAction("系统广信息")
创建自己的定义的广播接收类

	registerReceiver(自己定义的广播接收类，intentFilter);
这样就注册成功了，需要注意的是在onDestroy()方法中要
unregisterReceiver(自己定义的广播接收类对象)取消注册
###静态注册
>动态注册必须要在程序启动后才能收到广播。而静态注册可以让程序在未启动下就能收到广播。

	<receiver android:name=".自定义广播接收器类名">
	        <intent-filter>
	                <action android:name="广播信息"/>
	         </intent-filter>
	 </receiver>//在<application>标签包含此定义

##发送自定义广播

1. 发送标准广播

	    Intent intent = new Intent("广播信息");
	    sendBroadcast(intent);

2. 发送有序广播
 
		Intent  intent = new Intent("广播信息");
		sendOrderedBraodcast(intent,权限信息);

注册权限信息在`<intent-filter android:priority="100">`中权限大的最先收到广播，想阻止广播继承传下去调用方法abortBroadcast();

##使用本地广播
>全局广播可以被其它应用程序接收到，安全性不足。而使用本地广播，只能在应用程序内部进行传递，并且广播接收器也只能接收来自本应用程序发出的广播。本地广播主要是使用LocalBoradcastManager来对广播进行管理，并提供了发送广播和注册广播接收器的方法。

	localBroadcastManager = LocalBroadcastManager.getInstance(this);
	Intent intent = new Intent("广播信息");
	localBroadcastManager.sendBroadcast(intent)//发送广播
	
	localBroadcastManager.unregisterReceiver(localReceiver);

需要注意的是本地广播是无法通过静态注册的方式来接收的。