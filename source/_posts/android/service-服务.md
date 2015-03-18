title: service-服务
date: 2015/3/11 13:17:20 
tags:
- 技术
- android

categories: android
---

服务Service
>android中服务并不是运行在一个独立的进程当中的，而是依赖于创建服务时所在的应用程序进程。当某个应用程序的进程被杀掉时，所有依赖于该进程的服务也会停止运行。

##Service用法
###1 创建service
1. 写一个类继承抽象类service
2. 实现抽象方法onBind()
*  onCreate()方法会在服务创建的时候调用
*  onStartCommand()方法会在每次服务启动的时候调用，如果我们希望服务一旦启动就立刻去执行某个动作，就可以将逻辑写在onStartCommand()方法里
*  onDestroy()方法会在服务销毁的时候调用。而当服务销毁时，应该在onDestroy()方法中去回收那些不再使用的资源。
  
*onCreate()方法是在服务第一次创建的时候调用的，而onStartCommand()方法则在每次启动服务的时候都会调用.停止服务后并不需要重新创建服务,重新启动即可再使用服务了。另外需要注意是，每一个服务都需要在AndroidManifest.xml文件中进行注册才能生效，这是Android四大组件共有的特点。于是我们还应该修改AndroidManifest.xml文件。*（
###2 启动和停止service依靠Intent
	Intent startIntent = new Intent(this, MyService.class);
	startService(startIntent); // 启动服务
	
	Intent stopIntent = new Intent(this, MyService.class);
	stopService(stopIntent); // 停止服务
另外在Service里也可以调用stopSelf()方法来让service自己停下来。

###3 借助onBind()方法实现活动和服务进行通信
借助onBind()方法。在活动中要有一个专门的Binder对象来控制（继承Binder实现自己的Binder类）。当一个活动和服务绑定之后，就可以调用该服务里的Binder提供的方法进行操作。
	
	实现ServiceConnection接口，传入到onBind()方法中。（三个参数的方法）
	Intent bindIntent = new Intent(this, MyService.class);
	bindService(bindIntent, connection, BIND_AUTO_CREATE); // 绑定服务 onCreat()方法会执行，onStartCommand()方法不会执行
	unbindService(connection); // 解绑服务

>注意虽然每调用一次startService()方法，onStartCommand()就会执行一次，但实际上每个服务都只会存在一个实例。所以不管你调用了多少次startService()方法，只需调用一次stopService()或stopSelf()方法，服务就会停止下来了。
>
>另外，还可以调用Context的bindService()来获取一个服务的持久连接，这时就会回调服务中的onBind()方法。类似地，如果这个服务之前还没有创建过，onCreate()方法会先于onBind()方法执行。之后，调用方可以获取到onBind()方法里返回的IBinder对象的实例，这样就能自由地和服务进行通信了。只要调用方和服务之间的连接没有断开，服务就会一直保持运行状态。

>当调用了startService()方法后，又去调用stopService()方法，这时服务中的onDestroy()方法就会执行，表示服务已经销毁了。类似地，当调用了bindService()方法后，又去调用unbindService()方法，onDestroy()方法也会执行，这两种情况都很好理解。但是需要注意，我们是完全有可能对一个服务既调用了startService()方法，又调用了bindService()方法的，这种情况下该如何才能让服务销毁掉呢？根据Android系统的机制，一个服务只要被启动或者被绑定了之后，就会一直处于运行状态，必须要让以上两种条件同时不满足，服务才能被销毁。所以，这种情况下要同时调用stopService()和unbindService()方法，onDestroy()方法才会执行。

##使用前台服务：
>前台服务不会被回收，但是在系统通知栏一直会有个图标

用法如下：
	
	Notification notification = new Notification(R.drawable.ic_launcher,"Notification comes",System. currentTimeMillis());
	Intent notificationIntent = new Intent(this, MainActivity.class);
	PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, notificationIntent, 0);
	notification.setLatestEventInfo(this, "This is title", "This is content", pendingIntent);
	startForeground(1, notification);
  这段代码放在自己的service类的onCreate()方法中

##自动停止的Serivce
>为了可以简单地创建一个异步的、会自动停止的服务，Android专门提供了一个IntentService类。我们可以继承它来重写一些方法。首先是要提供一个无参的构造函数，并且必须在其内部调用父类的有参构造函数。然后要在子类中去实现onHandleIntent()这个抽象方法，在这个方法中可以去处理一些具体的逻辑，而且不用担心ANR（application not respond）的问题，因为这个方法已经是在子线程中运行的了。
