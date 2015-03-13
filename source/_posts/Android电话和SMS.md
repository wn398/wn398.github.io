title: Android电话和SMS
date: 2015/3/12 16:06:10 
tags:
- 技术
- android

catergories: android
---

##电话服务的硬件支持
  为了指定应用程序需要设备支持电话服务，需要在应用程序的mainfest文件中添加一个 uses-feature节点

	<uses-feature android:name="android.hardware.telephony" android:required="true"/>
   如果应用程序使用电话服务api，但它们并不是必需的，那么可以在试图使用相关api前，检查是否有电话硬件。
   为此，需要使用`PackageManager`的`hasSystemFeature`方法，并指定`FEATURE_TELEPHONY`功能。PackageManager还包括了用于查询特定CDMA和GSM的硬件是否存在的常量。

	PackageManager pm = getPackageManager();
	boolean telephonySupported = pm.hasSystemFeature(PackageManager.FEATURE_TELEPHONY);
	boolean gsmSupported = pm.hasSystemFeature(PackageManager.FEATURE_TELEPHONY_GSM);
	boolean cdmaSupported = pm.hasSystemFeature(PackageManager.FEATURE_TELEPHONY_CDMA);

##使用电话服务
>Android电话服务api使应用程序能够访问低层的电话硬件栈，从而允许创建自己的拨号程序----或者将呼叫处理程序和电话状态监视的功能集成到自己的应用程序中。

###启动电话呼叫
	Intent whoyougonnacall = new Intent(Intent.ACTION_DIAL,Uri.parse("tel:55-555"));
	startActivity(whoyougonnacall);

###替换本机拨号程序
替换本机拨号应用程序包括以下两个步骤：

1.  截获当前由本机拨号程序所服务的Intent

	本机拨号应用程序响应用户按下硬件呼叫按键对应的Intent动作，它要求利用`tel:模式`查看数据，或使用`tel:模式`发出一个ACTION_DIAL请求。

    为了截获这些请求，需要在你替换拨号程序Activity的mainfest文件中包含`intent-filter`标签来监听以下动作：

	* Intent.ACTION_CALL_BUTTON  当按下设备的硬件呼叫按键时该动作被广播。创建一个监听该动作的Intent Filter作为默认动作
	* Intent.ACTION_DIAL    它由想要启动电话呼叫的应用程序使用。
	* Intent.ACTION_VIEW   该查看动作由想要查看某条数据的应用程序使用。确保Intent Filter指定了tel:模式以允许新的Activity用于查看电话号码。

  	//替换拨号程序的Activity的manifest文件条目

	<actitivy android:name=“.MyDialerActivity">
	        <intent-filter>
	            <action android:name="android.intent.action.CALL_BUTTON"/>
	            <category android:name="android.intent.category.DEFAULT"/>
	        </intent-filter>
	        <intent-filter>
	            <action android:name="android.intent.action.VIEW"/>
	            <action android:name="android.intent.action.DIAL"/>
	            <category android:name="android.intent.category.DEFAULT"/>
	            <category android:name="android.intent.category.BROWSABLE"/>
	            <data android:schema="tel"/>
	        </intent-filter>
	   </activity>

2.  启动并管理拨打电话

 在你的Activity启动后，应该提供一个UI，供用户来输入或修改要拨打的号码，以及启动传出呼叫。最简单的办法就是通过`Intent.ACTION_CALL`动作使用现有的电话服务栈，这将使用系统的in-call Activity来启动呼叫，并让系统管理拨号，连接以及语音处理。要使用该动作，应用程序必须有以下权限：
`android.permission.CALL_PHONE`
或者还可以通过实现自己的拨号以及语音处理框架来完全替代传出的电话服务栈。

###访问电话服务的属性及状态
>电话服务API是由Telephony Manager进行管理的，使用getSystemService可以访问它

	String srvcName = Context.TELEPHONY_SERVICE;
	TelephonyManager telephonyManager = (TelephonyManager)getSystemService(srvcName);

Telephony Manager提供了对许多电话服务的属性的直接访问，包括设备，网络，客户识别模块（SIM）以及数据状态的详细信息。

1. 读取电话设备详细信息
	>可以获得电话类型（GSM,CDMA或SIP）,唯一ID（IMEI或者MEID）,软件版本和手机号码

		int phoneType = telephonyManager.getPhoneType();
		switch(phoneType)..
		//需要READ_PHONE_STATE使用权限
		//读取GSM手机的IMEI或CDMA手机的MEID
		String deviceId = telephonyManager.getDeviceId();
		//读取手机上的软件版本（注意：不是SDK版本）
		String softwareVersion = telephonyManager.getDeviceSoftwareVersion();
		//获得手机号码
		String phoneNumber = telephonyManager.getLine1Number();

	你还可以确定手机连接到的网络的类型，以及SIM或所连接的运营商网络的名称和所在的国家

2. 读取网络详细信息
	
	当连接到网络时，可以分别使用Telephony Manager的`getNetworkOperator`,`getNetworkCountryIso`,`getNetworkOperatorName`和`getNetworkType`方法读取移动国家代码和移动网络代码（MCC+MNC）,国家ISO代码，网络运营商名称和你连接的网络类型：

3. 读取SIM详细信息
	
	如果应用程序运行在GSM设备上，那通常有一个SIM.可以从Telephony Manager中查询SIM的详细信息以获得安装在当前设备中的SIM的ISO国家代码，运营商名称和运营商MCc以及MNC.
	
		int simState = telephonyManager.getSimState();

4. 读取数据连接和传输状态的详细信息
	
	使用`getDataState()`和`getDataActivity()`方法分别可以查找当前的数据连接状态和数据传输Activity。
Telephony Manager只指示基于电话服务的数据连接（移动数据，而不是wifi）.因此大多数情况下，Connectivity Manager是确定当前连接状态的更好选择。

###  使用PhoneStateListener监视电话状态的变化
>为了监视管理电话状态，需要权限`android.permission.READ_PHONE_STATE`。创建一个实现了PhoneStateListener的新类以监听并响应电话状态的变化事件，包括**呼叫状态（响铃，摘机等），蜂窝位置变化，语音邮件和呼叫转移状态，电话服务变化以及移动信号强度的变化**。
>
>在PhoneStateListener的实现中，重写想要响应的事件的处理程序。每个处理程序会接收指示新电话状态的参数，如当前蜂窝位置，呼叫状态或信号强度
   
创建了自己的Phone State Listener以后，将它注册到Telephony Manager中，使用位掩码指示想要监听的事件。

	    telephonyManager.listen(phoneStateListenern,
	                PhoneStateListener.LISITEN_CALL_FORWARDING_INDICATOR|
	                PhoneStateListener.LISITEN_CALL_STATE|
	                PhoneStateListener.LISITEN_CELL_LOCATION|
	                .....);
	                )
为了注销一个监听器，需要调用listen方法并将PhoneStateListener.LISTEN_NONE作为位掩码参数传入：

	telephonyManager.listen(phoneStateListener,
	                                    PhoneStateListener.LISITEN_NONE);
1. 监视传入的电话呼叫

	重写`onCallStateChanged()`方法并进行注册，以便在呼叫状态改变时接收通知。
	onCallStateChanged处理程序接收与传入呼叫相关的电话号码，而state参数则使用以下三个之一代表当前呼叫状态：
	
	*  TelephonyManager.CALL_STATE_IDLE       当电话即不响铃也不在通话中时
	*  TelephonyManager.CALL_STATE_RINGING    当电话响铃时
	*  TelephonyManager.CALL_STATE_OFFHOOK    当电话当前正在通话时
	
	注意一旦状态变为CALL_STATE_RINGING，系统会显示来电屏幕，询问用户是否要接听电话。
	应用程序必须处于运行状态才能接收这个回调，如果每当电话状态变化时，就应该启动应用程序，那么可以注册一个Intent Receiver，用于监听表示电话状态发生变化的Braodcast Intent.
2. 跟踪蜂窝位置变化
   
	实现`onCellLocationChanged`方法，当当前的蜂窝位置发生变化时，可以得到通知。它需要权限：

		android.permission.ACCESS_COARSE_LOCATION
  
	onCellLocationChanged处理程序接收一个CellLocation对象，其中包含了基于电话网络类型提取不同位置信息的方法。对于GSM网络，可以获得蜂窝ID(getCid)和当前位置区域代码（getLac）.对于CDMS网络，可以获得当前基站的ID(getBaseStationId)以及该基站的纬度（getBaseStationLatitude）和经度（getBaseStationLongitude)。
3. 跟踪服务变化
 
	`onServiceStateChanged()` 处理程序跟踪设备的蜂窝服务的详细信息。使用ServiceState参数可查找当前服务状态的详细信息。
   Service State对象的getState方法将当前的服务状态作为下列任意一项ServiceState参数返回：
	- STATE_IN_SERVICE  正常电话服务是可用的
	- STATE_EMERGENCY_ONLY 电话服务仅能用于紧急呼叫
	- STATE_OUT_OF_SERVICE 当前没有电话服务可用
	- STATE_POWER_OFF 电话无线传输功能被关闭（通常是启用了飞行模式时）

4. 监视数据连接和数据传输的变化
  可以使用一个`PhoneStateListener`监视数据连接以及移动数据传输的变化。注意，这不包含使用wifi传输的数据。若要更全面地监视数据连接和数据传输，应该使用`Connectivity Manager`,重写`onDataActivity()`以跟踪数据传输Activity,重写`onDataConnectionStateChange()`以请求通知数据连接状态变化。

5. 使用Intent Receiver 监视传入的电话呼叫
  当电话状态由于来电，接听和挂断而发生变化时，Telephony Manager会广播一个`ACTION_PHONE_STATE_CHANGED` Intent. 通过在mainfest文件中注册一个监听此Braodcast Intent的Intent Receiver，可以监听来电，即使你的应用程序没有运行。需要权限：`READ_PHONE_STATE`

		<receiver android:name="PhoneStateChangeReceiver">
		    <intent-filter>
		        <action android:name="android.intent.action.PHONE_STATE"></action>
		    </intent-filter>
		</receiver>
电话状态变化的Braodcast Intent最多可以包含两个extra。所有这些广播都会包含EXTRA_STATE extra,其值为某个TelephonyManager.CALL_STATE_*,用于指示新的电话状态。如果电话是响铃，则Broadcast Intent还会包含EXTRA_INCOMING_NUMBER extra.其值是来电号码。

		String phoneState = intent.getStringExtra(TelephonyManager.EXTRA_STATE);
		    if(phoneState.equals(TelephonyManager.EXTRA_STATE_RINGING)){
		     String phoneNumber = intent.getStringExtra(TelephonyManager.EXTRA_INCOMING_NUMBER);
		}


##SMS和MMS

###使用Intent从应用程序中发送SMS和MMS
####发送SMS消息
大多数情况下，与实现完整SMS客户端相比，最佳实践是使用Intent让另一个应用程序（通常是本机SMS应用程序）发送SMS和MMS消息。为此，需要使用`Intent.ACTION_SENDTO` 动作Intent来调用startActivity.使用sms:模式指定一个目标号码作为Intent数据。使用一个sms_body extra在Intent有效载荷中包含想要发送的消息。

        Intent smsIntent = new Intent(Intent.ACTION_SENDTO,Uri.parse("sms:54545454"));
        smsIntent.putExtra("sms_body","message is me");
       startActivity(smsIntent);

####发送MMS消息
   为了附加文件（基本上就是创建一条MMS消息）到消息中，需要添加一个带有要附加的资源URI的Intent.EXTRA_STREAM.并将Intent type设置为所附加资源的MIME类型。需要注意的是，本机sms应用程序并不包含ACTION_SENDTO的一个已经设置了type的Intent Receiver。相反，你需要使用ACTION_SEND并包含目标电话号码作为address extra:

	//获得要附加的一个媒体的URI
	Uri attached_Uri = Uri.parse("content://media/external/images/media/1");
	//创建一个新的MMS Intent
	Intent mmsIntent = new Intent(Intent.ACTION_SEND, attached_Uri);
	mmsIntent.putExtra("sms_body","message is me");
	mmsIntent.putExtra("address","03526677149);
	mmsIntent.putExtra(Intent.EXTRA_STREAM, aatached_Uri);
	mmsIntent.setType("image/jpeg");
	startActivity(mmsIntent);
   当运行以上程序时，用户很可能被提示选择一种能够完成发送的应用程序，包括Gmail,E-mail,和SMS应用程序。

###使用SMS Manager 发送SMS消息 

为了发送SMS消息 ，需要权限 `android.permission.SEND_SMS`

1. 发送文本消息 
	>为了发送文本消息，可以使用SMS Manager的sendTextMessage方法，并传入接收人的地址（电话号码）和想要发送的文本消息。
	
	    SmsManager smsManager = SmsManager.getDefault();
	    String sendTo = "564";
	    String myMessage = "I am message";
	    smsManager.sentTextMessae(sendTo,null,myMessage,null,null);
       第二个参数可以用于指定所要使用的SMS服务中心。如果输入了null，那么将使用设备运营商默认的服务中心。
        最后两个参数指定了用于跟踪传输和消息成功递送的Intent.为了响应这些Intent,创建并注册Broadcast Receiver，如下节所示“跟踪并确认SMS消息递送”所讲。

2. 跟踪并确认SMS消息递送
	>为了跟踪发出的SMS消息的传输过程并确认递送成功，需要实现并注册一些Broadcast Receiver，用于监听我们在创建传入sendTextMessage方法中的Pending Intent时所指定的动作。第一个PendingIntent参数会在消息发送成功或失败时被触发。接收该Intent的BroadcastReceiver的结果会是结果代码下列中的一个：
	
    - Activity.RESULT_OK  表示一次成功的传输
    - SmsManager.RESULT_ERROR_GENERIC_FAILURE 普通的传输失败
    - SmsManager.RESULT_ERROR_RADIO_OFF 电话无线信号被关闭
    - SmsManager.RESULT_ERROR_NULL_PDU 表示一次PDU(协议数据单元)错误
    - SmsManager.RESULT_ERROR_NO_SERVICE 表示没有可用的手机服务
       
	>第二个Pending Intent参数仅在接收人接收到你的SMS消息 之后被触发

3. 遵守最大sms消息尺寸

>SMS Manager包含`divideMessage()`方法，它可以接收一个字符串作为输入，并将其分成一个消息数据列表，其中的每条消息都不超过最大允许的尺寸。 之后可以使用sms Manger的`sendMutipartTextMessage()`方法传输消息数组：

	ArrayList<String> meeageArray = smsManager.divideMessage(myMessage);
		ArrayList<PendingIntent> sentIntents = new ArrayList<PendingIntent>();
		for(int i=0;i<messageArray.size();i++){
	        sentIntents.add(sentPI);
	    }
	   smsManger.sendMultipartTextMessage(sendTo,null,messageArray,sendIntents,null));

4  发送数据消息
   >可以使用SMS Manager的`sendDataMessage()`方法通过SMS发送二进制数据。sendDataMessage方法的使用方式与sendTextMessage类似，但是需要包含额外的参数，用于指定目标端口和构成了你想要发送的数据的字节数组。

	String sendTo = "54464";
	short destinationPort = 80;
	byte[] data= {---your data---}
	smsManager.sendDataMessage(sendTo,null,destinationPort,data,null,null);

##监听传入的SMS消息
当设备接收到一条新的SMS消息时，就会使用`android.provider.Telephony.SMS_RECEIVED`动作触发一个新的Broadcast Intent.需要注意的是，这是一个字符串变量，当前的SDK并不包含对该字符串的引用，因此当在应用程序中使用它时必须显式地指定它（这是不平台支持的API）
    
一个监听SMS Broadcast Intent的应用程序来说，需要权限 `android.permission.RECEIVE_SMS`
