title: android桌面开发
date: 2015/3/17 13:40:12 
tags:
- 技术
- android_UI
categories: android
---
##快捷方式
>要在桌面上添加一个快捷方式，只需要长按桌面或或者点击“Menu”,就要可以弹出添加桌面组件的选项。

1. 首先需要在Activity注册时添加一个Action为`android.intent.CREATE_SHORTCUT`的IntentFilter，添加之后列表中就会出现该应用的图标和名字了
2. 接下来还要为快捷方式设置名字，图标，事件等属性。`Intent.EXTRA_SHORTCUT_NAEM`对应名字，`Intent.EXTRA.SHORTCUT_ICON_RESOURCE`对应快捷方式的图标，`Intent.EXTRA_SHORTCUT_INTENT`对应快捷方式执行的事件。需要说明的是，Android专门提供了`Intent.ShortcutIconResource.fromContext`来创建快捷方式的图标，最后通过setResult来返回，构建一个快捷方式。

##实时文件夹
>Live Folders是一个备受关注的新功能。由于Live Folder本身不存储任何信息，都是以映射方式查看其ContentProvider所指向的数据信息，并可以自定义显示格式，所以当源数据发生改变后，Live Folder可以实时更新显示内容。和创建快捷方式一样，需要在Activity注册时添加一个Action动作为`android.intent.action.CREATE_LIVE_FOLDER`的IntentFilter
需要在程序中设置该实时文件夹的*数据源，图标，名字的信息*，可能通过`intnet.setData`方法来设置要读取的数据源

##Widget开发
>Widget是一种很小的应用程序，主要作为Web 2.0服务或互联网内容的前端。Web设计人员与开发者可以使用Widget来创造最受欢迎的互联网体验。在android 1.5中加入了AppWidget framework框架，开发者可以使用该框架开发widget，这些widget可以拖到用户的桌面并且可以交互。当Widget被拖到桌面上时，指定一个保留的空间来显示应用提供的自定义内容。用户可以通过这个Widget来和应用交互。

Widget框架,AppWidget框架主要包括以下类：

1. AppWidgetProvider:继承自BroadcastReceiver，在AppWidget应用update,enable,disable和deleted时接收通知。其中，onUpdate,onReceiver是最常用方法，它们接收更新通知
2. AppWidgetProviderInfo:描述AppWidget的大小，更新频率和初始界面等信息，以XML文件形式存在于应用的res/xml/目录下。
3. AppWidgetManager:负责管理AppWidget，向AppWidgetProvider发送通知。
4. RemoteViews:一个可以在其他应用进程中运行的类，是构造AppWidget的核心。
>每个Widget就是一个`BroadcastReceiver`，它们用XML medadata来描述Widget的细节。AppWidget framework通过Broadcast intents和Widget通信，Widget的更新使用`RemoteViews`来发送。RemoteViews被包装成一个layout和特定内容来显示到桌面上。

步骤：

1. 制定widget的UI布局，即remoteView对应的布局文件
2. 首先需要在res/layout目录下创建桌面组件的布局文件`appwidget_provider.xml`(即能反映AppWidgetProviderInfo信息的布局文件)，用来显示桌面布局，然后需要创建一个描述这个桌面组件属性的文件，存放到res/xml文件夹下。AppWidget的像素大小取决于它所占的方块多少，其计算公式是(块数*74)-2. 其中android:initialLayout设置了AppWidget的布局文件

		<appwidget-provider 
		  android:minWidth="100dp"
		  android:minHeight="50dp"
		  android:updatePeriodMillis="86400000"
		  android:initialLayout="@layout/appwidget_provider"
		  android:configure="com....Activity"
		</appwidget-provider>

	android:updatePeriodMillis是自动更新的时间间隔
	
	android:initialLayout是Widget的界面描述文件
	
	Android:configure是可选的，如果你的Widget需要在启动前先启动一个Activity，则需要设定该项为你的Activity.

3. 再次，需要创建一个类继承自`AppWidgetProvider`的类，当我们添加AppWidget应用或自动定时更新时，AppWidgetManager会广播动作名字是`android.appwidget.action.APPWIDGET_UPDATE`的Intent,当`onRecever()`方法没有被重载时，`onUpdate()`方法会接收到这些广播的Intent.类似于普通BroadcastReceiver类，我们可以重载AppWidgetProvider的onReceive方法，并在其中指定想要接收的Intent.

	public class TodayDate extends AppWidgetProvider
	{
	    public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds)
	 {
	  RemoteViews updateView = buildUpdate(context);
	  appWidgetManager.updateAppWidget(appWidgetIds, updateView);
	  super.onUpdate(context, appWidgetManager, appWidgetIds);
	 }
4. AndroidMainfest.xml权限设置
我们使用了`receiver`及其`android.appwidget.action.APPWIDGET_UPDATE`，所以需要声明

		<application android:icon="@drawable/icon" android:label="@string/app_name">
		        <receiver android:label="@string/app_name" android:name=".TodayDate">
		            <intent-filter>
		                <action android:name="android.appwidget.action.APPWIDGET_UPDATE"></action>
		            </intent-filter>
		            <meta-data android:name="android.appwidget.provider" android:resource="@xml/widget"></meta-data>
		        </receiver>

###widget优点

>Widget开发，其实就是只需要设计好UI,然后根据需要来完成更新。如果程序不需要使用定时更新，则建议不要使用定时更新，因为它会增加电量和CPU资源开销。我们可以自定义一个线程，当需要更新时才更新，这样让Widget达到最高效率。
Widget,LiveWallpaper和Quick Search Box(QSB)使用用户能够填充主屏幕，它即可以提供一个到应用程序的窗口，也可以提供直接在主屏幕上显示独立信息源。它们对用户和开发人员来说是令人耳目一新的新成果，主要提供了下面两种功能：
>
1. 用户能够即时访问感兴趣的信息而不需要打开应用程序
2. 开发人员能够直接从主屏幕中获取到自己的应用程序的入口点

一个实用的Widget或者动态墙纸可以增加用户的兴趣，同时可以增加其被使用的可能性。Widget会作为主屏幕进程的子进程而持续运行。当我们在创建Widget时需要格外小心，以确保它们能够及时响应并且不会耗尽系统资源。
Widget可以使我们的应用程序直接在用户主屏幕上拥有一块交互式的屏幕面板以及一个入口点。一个好的App Widget可以用最少的资源开销提供有用的，精确的和及时的信息

##创建App Widgets

App Widgets作为BroadcastReceiver实现。它们使用RemoteViews来创建和更新寄存在另一个应用程序进程中的视图层次结构，大多数情况下该进程是主屏幕。
为了创建一个应用程序的Widget，我们需要建立以下3个组件:

1. 一个定义了该widgetUI的XML布局资源
2. 一个描述了与该Widget相关联的元数据的XML文件
3. 一个定义并控制该widget的Intent接收器

>对一个单独的应用程序来说，即可以根据自己的需要创建尽可能多的widget,也可以拥有一个由单个Widget组成的应用程序。每个Widget即可以使用相同的尺寸，布局，刷新速率和更新逻辑，也可以全部使用不同的参数。在许多情况下为自己的Widget提供具有不同尺寸大小的多个版本是非常有用的。

widget布局尺寸：使用下面的公式可以近似地计算出为确保你的widget适合一定数量的单元格中，所需指定的最小高度和宽度限制：
Min height or width = 70dp * (cell count) -30dp

###受支持的widget视图和布局：

出于安全和性能考虑，当用创建自己的WidgetUI时，对可使用的布局和视图存在一些限制。下面的视图对App Widget布局来说不可用，如果使用它们将会导致一个空指针错误：

- 所有的自定义视图
- 由允许的视图所派生的视图
- EditText(编辑文本)

当前，可使用的布局仅限于：

- FrameLayout
- LinearLayout
- RelativeLayout
- GridLayout

其中包含的视图中只局限于：

- AnalogClock
- Button
- Chronometer
- ImaageButton
- ImageView
- ProgressBar
- TextView
- ViewFlipper

>Android3.0中引入了CollectionViewWidgets,这是一种新的Widget类，用于以列表，网格或堆栈的形式显示数据集合。
定义widget设置

>Widge定义资源作为xml存储在项目的res/xml文件夹中。appwidget-provider标签使我们能够描述widget元数据，该元数据使用下列属性定义了用户widget的尺寸，布局，以及更新速率：

- initialLayout 创建Widget UI时用到的布局资源
- minWidth/minHeight 分别表示Widget的最小宽度和最小高度
- resizeMode 通过使用horiontal和vertical的组合来设置resizeMode允许你指定Widget在哪个方向上进行调整。将它设为none则会禁止调整widget的大小
- label  在Widget选取器中用户Widget所用到的标题
- updatePeriodMillis 以毫秒为单位表示的Widget更新的最小周期。Android将会以这个速率唤醒设备以便更新用户Widget,因此应当指定为至少一个小时。App Widget Manager最快不能以每30分钟一次的速率进行更新。理想情况下，用户的Widget每天使用该更新技术不应当超过一次或两次。
- configure  当将用户Widget添加到主屏幕时，可以有选择地指定启动一个完全限定的activity.该activity能够用于指定widget设置和用户首选项。
- icon 默认情况下，android在widget选取器中呈现widget时，会使用应用程序的图标，通过指定一个Drawable资源，可以使用一个不同的图标
- previewImage  Andriod3引入一个新 的App Widget选取器，用于显示Widget的预览，而不是显示其图标。在这里指定的drawAble资源应该能够精确地表现出Widget在添加到主屏幕时的样子。

>创建Widget Broadcast Receiver并将其添加到应用程序的mainfest文件 中：
>widget是作为BroadcastReceiver实现的。每个Widget的Broadcast Receiver都指定Intent Filter,用于监听使用`AppWidget.ACTION_APPWIDGET_UPDATE`, `DELETED`,`ENABLED以及DISABLED`动作请求更新的`Braodcast Intent`. 
通过重写`onReceive`方法来扩展`BroadcastReceiver`类并实现对每个Broadcast Intent的响应，可以创建自己的Widget
AppWidgetProvider类封装了Intent处理，并提供了更新，删除，启用和禁用事件的事件处理程序。

一个扩展了AppWidgetProvider的Widget实现的基本代码：

	public class myAppWidget extends AppWidgetProvider{
	    public void on Update(Context,AppWidgetMaqnager,int[] appWidgetIds){
	    //更新widget UI
	    }
	    public void onDeleted(context,int[] appWidgetIds){
	     //处理删除widget操作 
	    }
	    ...
	}

>Widget必须被添加到应用程序的mainfest文件中,为了将一个Broadcast Receiver指定为一个App Widget，需要下面两个标签添加到它的mainfest文件结点中:

- 一个用于android.appwidget.action.APPWIDGET_UPDATE动作的Intent Filter
- 一个对appwidget-provider元数据XML资源的引用，该xml描述了用户的Widget设置
如：

		<receiver android:name=".MyAppWidget">
		    <intent-filter>
		        <action android:name="android.appwidget.action.APPWIDGET_UPDATE"/>
		    </intent-filter>
		   <mete-data
		        android:name="android.appwidget.provider”
		        android:resource="@xml/widget_provicer_info'
		    />
		</receiver>

###AppWidgetManager和RemoteView简介
>AppWidgetManager类用于更新App Widget和提供App Widget的相关信息。RemoteView类用作在另一个应用程序的进程中托管的View的层次的代理。从而允许修改运行在另一个应用程序中的View的属性或运行该view的方法。

1. 创建remoteView
	>为创建一个新的RemoteView对象，需要将应用程序的包名和想要操纵的布局资源传入RemoteViews构造函数中：
	
		RemoteViews views = new RemoteViews(context.getPackageName(),R.layout.my_widget_layout)
	
	RemoteView包含一系列方法，可用于访问本机View的许多属性和方法。在RemoteView包含的这些方法中，最灵活的是一组set方法，它们可以指定要在远程托管的View上执行的目标方法的名称。这些方法支持单值参数（基本类型，整数，字节，字符，和浮点型），还支持字符串，位图，Bundle和URI参数。
	如：
	//设置ImageView的image level
	
		views.setInt(R.id.widget_image_view,"setImageLevel",2);
	
	其它的类似。还有许多专门的用于某些view类的方法，包含修改TextView,ImageView,ProgressBar和Chronometer的方法。如`views.setProgressBar(R.id.widget_progressbar,100,50,false);`
	通过调用`setViewVisibility`可以设置任何托管在一个远程View布局内的view的可见性。如
	
		views.setViewVisibility(R.id.widget_text,View.INVISIBLE);

2. 将RemoteView应用到运行中的App widget
	>要将对RemoteView所作的修改应用到处于活动状态的Widget,需要使用`AppWidgetManager的updateAppWidget`方法，并传入一个或更多个要更新的Widget的标识符和要应用的RemoteView作为参数。
	
		appWidgetManager.updateAppWidget(appWidgetIds,remoteViews);
3. 使用RemoteViews为Widget添加交互性
>App Widget运行在进程中，并继承这些进程的权限。因为大多数主屏幕应用程序都在完整权限下运行，所以潜在的安全风险变得严峻。因而，Ｗidget的交互性是被严格控制的.主要被限制为以下几个方面：
>
	1. 添加监听一个或更多个View的ClickListener
	2. 根据所选项变化改变UI
	3. 在Collection View Widget中的View之间过渡。

###使用Click Listener:
>要向Widget添加交互性，最简单，最强大的方法是添加其view的click Listener.这是能过使用RemoteView对象的`setOnClickPendingIntent`方法实现的。使用此方法指定一个当用户单击指定view时触发的Pending Intent:

	Intent intent = new Intent(context,Myactiviy.class);
	PendingIntent pendingIntent = PendingIntent.getActivity(context,0,intent,0);
	views.setOnclickPendingIntent(R.id.widget_text,pendingIntent);

###创建并使用Widget配置Activity
>如果为用户提供在把Widget添加到主屏幕之前配置Widget的机会，对他们会更有帮助。如果进行了合适的配置，用户能够在主屏幕上添加相同Widget的多个实例。  每当我们将widget添加到主屏幕中时，一个App Widget配置Activity就会立即启动。它可以是用户应用程序中的任何Activity,配置中必须全部包名+类名

	android:configure=".."
启动配置Activity的Intent将包含一个`EXTRA_APPWIDGET_ID` extra,该extra提供了它所配置的App Widget ID. 在该Activiy内，提供一个UI以便让用户完成配置并确认。在这个阶段，Activity的结果应该是RESULT_OK，并返回一个Intent,返回的Intent必须包含一个extra,该extra使用EXTRA_APPWIDGET_ID常量描述了所配置的Widget的ID。
基本模式为：

	Intent intent = getIntent();
	Bundle extras = intent.getExtras();
	if(extras !=null){
	    appWidgetId = extras.getInt(AppWidgetManager.EXTRA_APPWIDGET_ID,AppWidgetManager.INVALID_APPWIDGET_ID);
	//如果用户不接受配置更改
	setResult(RESULT_CANCELED,null);
	//conifgure the UI
	//通知Widget Manager配置已经完成
	Intent intent = new Intent();
	result.putExtra(AppWidgetManager.EXTRA_APPWIDGET_ID,appWidgetId);
	setResult(RESULT_OK,result);
	finish();
	}

###Collection View Widget使用
>Android3.0引入了Collection View Widget，这是一种新型的Widget,用于将数据集合显示为列表，网格或层叠卡片样式。
它用于添加对于集合View的支持，主要包括：

- StackView  一个卡片view，以层叠方式显示其子view
- ListView  传统的ListView
- GridView 一个二维的可滚动的网格，每个项目都显示在网格的一个单元中。

这些控件都扩展了AdatperView类
>Collection View Widget可用于显示任何数据集合，但是它们特别适合创建那些用于呈现应用程序Content Provider中保存的数据的动态Widget.  它的实现方式与常规App widget相似。另外基于集合的widget还需要以下组件：

- 一个额外的布局资源，用于为Widget中显示的每个项目定义UI
- 一个RemoteViewsFactory,用作Widget的事实上的适配器，提供了将集合View中显示的view。这个RemoteViewFactory使用项目布局定义创建一个RemoteViews,然后使用你想要显示的底层数据填充该RemoteView的元素。
- 一个RemoteViewsService  用于实例化和管理RemoteViewsFactory

>有了这些组件后，就可以使用RemoteViewsFactory创建和更新代表集合项目的每个view，通过创建一个RemoteView,并使用`setRemoteAdapter`方法将`RemoteViewsService`分配给该RemoteView,可以让这个过程自动进行。当把Remote Views 应用到集合Widget时，RemoteViewsService会根据需要创建并更新每个项目。

###创建RemoteViewService
>RemoteViewsService用作一个实例化和管理`RemoteViewsFactory`的包装器，而RemoteViewsFacotry则用来提供在Collection view Widget中显示的每个View.  为创建RemoteviewsService，需要扩展RemoteViewsService类，并通过重写onGetViewFacotry处理程序返回来的RemoteViewsFactory的一个新实例。和任何Service一样，需要使用一个service标签把RemoteViewsService添加到应用程序的mainfest文件中。为防止其他应用程序访问你的Widget,必须指定`android.permission.BIND_REMOTEVIEWS`的权限。

	<service android:name=".MyRemoteViewsService"
	               android:permission="android.permission.BIND_REMOTEVIEWS">
	</service>
###创建一个RemoteViewsFactory:
>RemoteViewsFacotry是Adapter类的一个包装器。

	class MyRemoteViewsFactory implements RemoteViewsFacotry{
	}

###使用RemoteViewsService填充CollectionviewWidget
>完成RemoetViewFactory后，剩下的就是将App Widget Layout中的ListView,GridView或StackView绑定到RemoteViewsService。这通常是在App Widget实现的ouUpdate处理程序中使用一个Remote View完成的。

	Intent intent = new Intent(context,MyRemoteViewsService.class);
	intent.putExtra(AppWidgetManager.EXTRA_APPWIDGET_ID,appWidgetId);
	views.setRmeoteAdapter(appWidgetId,R.id.wiget_stack_view,intent);
	views.setEmptyView(R.id.widget_statck_view,R.id.widget_empty_text);

##Live Wallpaper

>Live Wallpaper用于创建动态的，交互式的主屏幕背景，它使用Surface view以呈现一种动态的显示，并允许实时交互。它可以监听和捕获屏幕触摸事件，以使用户能够直接与主屏幕的背景进行交互。
>
为了创建一个新的Live Wallpaper需要以下三个组件：

1. 一个描述了与Live Wallpaper关联的元数据的xml资源。这些元素包括，作者，描述和和来在Live wallpaper选取器中代表该Live wallpaper的缩略图
2. 一个Wallpapaer service实现，它将封装，实例化和管理Wallpaper引擎
3. 一个Wallpaper service引擎实现（通过wallpaper Service返回），它定义了Live wallpaper的UI和交互行为。Live Wallpaper实现的大部分内容都包含在Wallpaper Service引擎中。

###创建Live Wallpaper步骤

1. 创建LiveWallpaper定义资源

	Live wallpaper资源定义是一个存储在res/xml文件夹中的xml文件。如
	
			<wallpaper
			    android:author="@string/author"
			/>
	
	android:settingActivity="..."配置Live Wallpaper的activity

2. 创建Wallpaper service

	扩展wallpaperService类，创建一个包装器service.重写`onCreateEngine`处理程序以返回自定义的Wallpaper service Engine的一个新的实例。
	添加到mainfest文件。注意粗黑
	权限 android:permisstion="android.permission.BIND_WALLPAPER"
		
			<service android:name=".MYWallpaperService">
			    <intent-filter>
			        <action android:name="android.service.wallpaper.WallpaperService"/>
			    </intent-filter>
			   <mete-data
			        android:name="android.service.wallpaper"
			        android:resource="@xml/mylivewallPaper"
			 />
			</service>

3. 创建wallpaper service引擎

	我们将在WallpaperService.Engine类中定义Live Wallpaper的行为。WallPaper Service Engine包含一个用于显示墙纸的Surface View，还包含一些处理程序，用于通知触摸时间和主屏幕偏移变化。应该在Wallpaper Service Engine中实现重绘循环。为实现自己的Wallpaper Service引擎，需要在一个Wallpaper Service类内扩展WallpaperService.Engine类。
	
			public class MyWallpaperServiceEngin extends WallpaperService.Engine{
			  onCreate()//处理初始化
			  onOffsetsChanges()//每当用户手指划过主屏幕面板时触发
			  onTouchEvent()//收到触摸事件时触发
			   onSurfaceCreated()//已经创建表面，运行用于更新Live Wallpaper的更新循环
			  drawFrame()//在画布上绘制
			    
			}
	    
