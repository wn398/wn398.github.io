title: android通知、PendingIntent、定时机制
date: 2015/3/16 14:04:24 
tags:
- 技术
- android

categories: android
---
##使用通知的步骤
1. 获取通知管理器
	
		NotificationManager manager =(NotificationManager)context.getSystemService(Context.NOTIFICATION_SERVICE)
2. 创建Notification对象
	>这个对象用于存储通知所需的各种信息。可以使用有参构造函数来创建。
	
	>第一个参数指定通知图标
	
	>第二个参数指定通知的ticker内容，当通知刚被创建时，它会在系统的状态栏一闪而过，属于瞬时的提示信息。
	
	>第三个参数用于指定通知被创建的时间，以毫秒为单位
	
		Notification notification = new Notification(R.drawable.icon,"This is ticker text",System.currentTimeMillis());
3. 布局设定
	>创建好Notification对象后，还需要对通知的布局进行设定，只需要调用Notification的setLatestEventInfo()方法就可以给通知设置一个标准的布局。这个方法接收4个参数，
	
	>第一个参数context
	
	>第二个参数指定通知的标题内容，下拉系统状态栏就可以看到这部分内容
	
	>第三个参数用于指定正文的内容，同样下拉系统状态栏可以看到这部分内容
	
	>第四个参数pendingIntent
	
		notification.setLatestEventInfo(context,"This is content title","This is content text",null);
4.	显示通知
	>只需要调用NotificationManager的notify()方法就可以让通知显示出来了，它接收两个参数，第一个是id,要保证每个通知的id都是不同的，第二个参数是Notification对象
    	
		manager.notify(1,notification);

##pendingIntent
>为了让点开的通知能有点击效果。我们还需要在代码中进行相应的设置，这就涉及到PendingIntent.
PendingIntent和Intent有些类似，它们都指明一个“意图”，都可用于启动活动，启动服务以及发送广播等。不同的是，Intent更加倾向于去立即执行某个动作，而PendingIntent更加倾向于于在某个合适的时机去执行某个动作。也可以把它理解为延迟执行的Intent.

>PendingIntent用法很简单，它主要提供了几个静态方法用于获取PendingIntent的实例，可以根据需求来选择是使用getActivity()方法，getBroadcast()方法，还是getService()方法，这几个方法所接收的参数都是相同的：

>第一个context

>第二个参数一般用不到，通常传0，

>第三个参数是一个Intent对象，我们可以通过这个对象构建PendingIntent的“意图”

>第四个参数用于确定PendingIntent的行为，有`FLAG_ONE_SHOT,FLAG_NO_CREATE`,`FLAG_CANCEL_CURRENT`,`FLAG_UPDATE_CURRENT`这四种
现在我们可以加Notification的第四个参数了

##Notification的高级属性：

1. 可以使用声音

		Uri soundUri = Uri.fromFile(new File("/system/media/audio/ringtones/Basic_tone.ogg"));
		notification.sound = soundUri;
2. 还可以让手机震动：
	>vibrate，它是一个长整形的数组，用于设置手机静止和震动的时长，以毫秒为单位，下标0表示手机静止的时长，下标1表示手机振动的时长，下标2表示手机静止的时长，以此类推。如果想让手机在通知到来的时候立刻震动1秒，然后禁止1秒，再振动1秒，代码如下：
	>
		long[] vibrates = {0,1000,1000,1000}
		notification.vibrate=vibreates;
想要控制手机振动还需要声明权限的`android.permission.VIBRATE`
3. 使用LED灯
	>可以使用`ledARGB`,`lebOnMS`,`ledOffMS`以及`flags`这几个属性来实现这种效果。`ledARGB`用于控制LED灯的颜色，一般有红绿蓝三种，ledOnMS用于指定灯亮的时长，以毫秒为单位，ledOffMS指定灯暗的时长，flags用于指定通知的一些行为，其中包括LED灯这一项
	
		//当通知来时，如果想实现LED灯以绿色的灯光闪一闪的效果，可以如下：
		notification.ledARGB = Color.GREEN;
		notification.ledOnMS = 1000;
		notification.ledOffMS = 1000;
		notification.flags = Notification.FLAG_SHOW_LIGHTS;
	>如果不想进行那么多繁杂的设置，也可以直接使用通知的默认效果，它会根据当前手机的环境来决定播放什么铃声，以及如何振动：
	
		notification.default = Notification.DEFAULT_ALL

##Android的定时机制：
###两种定时机制
>Android中的定时任务一般有两种实现方式，一种是使用Java API里提供的Timer类，一种是使用Android的Alarm机制。这两种方式在多数情况下都能实现类似的效果，但Timer有一个明显的短板，它并不太适用于那些需要长期在后台运行的定时任务。我们都知道，为了能让电池更加耐用，每种手机都会有自己的休眠策略，Android手机就会在长时间不操作的情况下自动让CPU进入到睡眠状态，这就有可能导致Timer中的定时任务无法正常运行。而Alarm机制则不存在这种情况，它具有唤醒CPU的功能，即可以保证每次需要执行定时任务的时候CPU都能正常工作。需要注意，这里唤醒CPU和唤醒屏幕完全不是同一个概念，千万不要产生混淆。

###Alarm使用
>来看一下Alarm机制的用法吧，主要就是借助了`AlarmManager`类来实现的。这个类和`NotificationManager`有点类似，都是通过调用`Context`的`getSystemService()`方法来获取实例的，只是这里需要传入的参数是`Context.ALARM_SERVICE`。因此，获取一个AlarmManager的实例就可以写成：
	
	AlarmManager manager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);

>接下来调用AlarmManager的set()方法就可以设置一个定时任务了，比如说想要设定一个任务在10秒钟后执行，就可以写成：

	long triggerAtTime = SystemClock.elapsedRealtime() + 10 * 1000;
	manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP, triggerAtTime, pendingIntent);
>第一个参数是一个整型参数，用于指定AlarmManager的工作类型，有四种值可选，分别是`ELAPSED_REALTIME`、`ELAPSED_REALTIME_WAKEUP`、`RTC`和`RTC_WAKEUP`。其中`ELAPSED_REALTIME`表示让定时任务的触发时间从系统开机开始算起，但不会唤醒CPU。`ELAPSED_REALTIME_WAKEUP`同样表示让定时任务的触发时间从系统开机开始算起，但会唤醒CPU。`RTC`表示让定时任务的触发时间从1970年1月1日0点开始算起，但不会唤醒CPU。`RTC_WAKEUP`同样表示让定时任务的触发时间从1970年1月1日0点开始算起，但会唤醒CPU。使用`SystemClock.elapsedRealtime()`方法可以获取到系统开机至今所经历时间的毫秒数，使用`System.currentTimeMillis()`方法可以获取到1970年1月1日0点至今所经历时间的毫秒数。

>第二个参数，这个参数就好理解多了，就是定时任务触发的时间，以毫秒为单位。如果第一个参数使用的是`ELAPSED_REALTIME`或`ELAPSED_REALTIME_WAKEUP`，则这里传入开机至今的时间再加上延迟执行的时间。如果第一个参数使用的是`RTC`或`RTC_WAKEUP`，则这里传入1970年1月1日0点至今的时间再加上延迟执行的时间。

>第三个参数是一个`PendingIntent`,这里我们一般会调用`getBroadcast()`方法来获取一个能够执行广播的`PendingIntent`。这样当定时任务被触发的时候，广播接收器的`onReceive()`方法就可以得到执行。
