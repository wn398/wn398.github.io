title: android-NFC
date: 2015/3/13 18:44:09 
tags:
- 技术
- android
- NFC

categroies: android
---
##NFC
>NFC(Near Field Communication)  是一种非接触式的技术，用于在短距离内（通常小于4cm）内少量数据的传输。NFC传输可以在两个支持NFC的设备或者一个设备和一个NFC“标签”之间进行。NFC标签即包括在扫描时会传输URL的被动标签，也包括复杂的系统。在Android中，NFC消息是通过使用NFC Data Exchange Format(NDEF)处理的。 为读取，写入或广播NFC消息，应用程序需要具有NFC manifest权限。

	<uses-permission android:name="android.permission.NFC"/>

###读取NFC标签
>当一个android设备用于扫描一个NFC标签时，其系统将使用自己的标签分派系统解码传入的有效载荷。这个标签分派系统会分析标签，将数据归类，并使用Intent启动一个应用程序来接收数据。

为使应用程序能够接收NFC数据，需要添加一个Activity Intent Filter来监听以下的某个Intent 动作：

- NfcAdapter.ACTION_NDEF_DISCOVERED 这是优先级最高，也是最具体的NFC消息动作。使用这个动作的INtent包括MEME类型	和、或URI数据。最好的做法就是只要有可能就监听这个广播，因为其exra数据允许更加具体地定义要响应的标签。
- NfcAdapter.ACTION_TECH_DISCOVERED 当NFC技术已知，但是标签不包含数据（或者包含的数据不能被映射为MIME类型或	URI）时广播这个动作
- NfcAdapter.ACTION_TAG_DISCOVERED 如果从未知技术收到一个标签，则使用此Intent动作广播该标签

//如下示例，显示了如何注册一个Activity,使其只响应对应于作者博客的URI的NFC标签
	
	<activity android:name=".BlogViewer">
	    <intent-filter>
	        <action android:name="android.nfc.action.NDEF_DISCOVERED"/>
	        <category android:name="android.intent.category.DEFAULT"/>
	        <data android:scheme="http"
	                   android:host="blog.radioactiveyak.com"/>
	    </intent-filter>
	</activity>

>NFC Intent Filter尽可能具体是一种很好的做法，这样可以将能够响应指定NFC标签的应用程序数减至最少，从而提供最好，最快的用户体验。

>很多时候，应用程序使用Intent数据、URI和MIME类型就足以做出合适的响应。但是需要时，可以通过Intent(启动Activity的Intent)内的extra使用NFC消息提供的有效载荷。`NfcAdapter.EXTRA_TAG` extra包含一个代表扫描标签的原始tag对象。`NfcAdapter.EXTRA_TNDEF_MESSAGES` extra中包含了一个NDEF Message的数组，如下所示：

	//提取NFC标签的有效载荷 
	String action = getIntent().getAction();
	if(NfcAdapter.ACTION_NDEF_DISCOVERED.equals(action)){
	    Parcelable[] messages = intent.getPareclableArrayExtra(NfcAdapter.EXTRA_NDEF_MESSAGES);
	       for(int i=0;i<messages.length;i++){
	            NdefMessage message = (NdefMessage)message[i];
	            NdefRecord[]  records = message.getRecords();
	            
	            for(int j=0;j<records.length;j++){
	                NdefRecord record = records[j];
	                //TODO 处理单独的记录
	            }
	    }
	}

###使用前台分派系统
>默认情况下，标签分派系统会根据标准的Intent解析过程确定哪个应用程序应该收到特定的标签。在Intent解析过程中，位于前台的Activity并不比其他应用程序的优先级高。因此，如果几个应用程序都被注册为接收扫描类型的标签，用户就需要选择使用哪个应用程序，即使此时你的应用程序位于前台。

>通过使用前台分派系统，可以指定特定的一个具有优先级的Activity使得当它位于前台时，成为默认接收标签的应用程序。使用NFC Adapter的`enable`/`disableForegroundDispathc`方法可以切换前台分派系统。只有当一个Acitivy位于前台时才能使用前台分派系统，所以应该分别在`onResume()`和`onPause()`处理程序内启用和禁用该系统，如下示例：

	//使用前台分派系统
	public void onPause(){
	    super.onPause();
	    nfcAdapter.disableForegroundDispatch(this);
	}
	
	public void onResume(){
	    super.onResume();
	    nfcAdapter.enableForegroundDispathc(this,
	        //用于打包Tag Intent的Intent
	        nfcPendingIntent,
	        //用于声明想要拦截的Intent的Intent Filter数组
	        intentFilterArray,
	        //想要处理的标签技术的数组
	        techListArray);
	    String action = getIntent().getAction();
	    if(NfcAdapter.ACTION_NDEF_DISCOVERED.equals(action)){
	        processIntent(getIntent());
	        
	    }
	}

>Intent Filter数据应该声明想要拦截的URI或MIME类型,如果收到的任何标签的类型与这些条件不匹配，那么将会利用标准的标签分派系统处理它们。为了确保良好的用户体验，只指定你的应用程序处理的标签内容是很重要的。通过显式指定想要处理的技术（通常是添加NfcF类），可以进一步细化收到的标签。最后，NFC Adapter会填充Pending Intent，以便把收到的标签直接传输给你的应用程序。
>
>如下的程序清单显示了为了启用上一个程序清单中的前台分派系统所需要使用的Pending Intent,MIME类型数组和技术数组：

	//配置前台分派参数
	PendingIntent nfcPendingIntent;
	IntentFilter intentFilterArray;
	String[] techListsArray;
	
	public void onCreate(Bundle savedInstanceState){
	    super.onCreate(savedInstanceState);
	    setContentView(R.layout.main);
	    [...Existing onCreate logic...]
	    //创建Pending Intent
	    int requestCode = 0;
	    int flags = 0;
	    
	    Intent nfcIntent = new Intent(this,getClass());
	    nfcIntent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
	    nfcPendingIntent = PendingIntent.getActivity(this,requestCode,nfcIntent,flags);
	    //创建局限为URI或MIME类型的Intent Filter 从中拦截TAG扫描
	    IntentFilter tagIntentFilter = new IntentFilter(NfcAdapter.ACTION_NDEF_DISCOVERED);
	    tagIntentFilter.addDataSchema("http");
	    tagIntentFilter.addDataAuthority("blog.radioactivyak.com",null);
	    intentFilterArray = new IntentFilter[]{tagIntentFilter};
	    //创建要处理的技术数组
	    techListsArray = new String[][]{
	     new String[]{
	        NfcF.class.getName();
	        }
	    }
	}
###Android Beam
>android4中引入了Android Beam提供了一个简单的API。应用程序可以使用该API在使用NFC的两个设备之间传输数据，只要将这两个设备背靠背放在一起即可。（为使用Android Beam传输消息，你的应用程序必须位于前台，而且接收数据的设备不能牌锁住状态。）
>
>当设备被放到一起时，Android Beam会使用NFC在设备之间推送`NDEF`消息。通过在应用程序内启用Android Beam,可以定义所传输的消息的有效载荷。如果没有自定义消息，应用程序的默认动作会在目标设备上启动它。如果目标设备上没有安装你的应用程序，那么Googel Play就会启动，并显示你的应用程序的详细信息页面。

>为定义应用程序传输的消息，需要在mainfest文件中请求NFC权限：`android.permission.NFC`
>
>定义自己的有效载荷的过程如下：

1. 创建一个包含NdefRecord的NdefMessage对象，NdefRecord包含了消息的有效载荷 
2. 将你的NdefMessage 作为Android Beam的有效载荷分配给NFC Adapter
3. 配置应用程序来监听传入的Android Beam消息 

####创建Android Beam消息
>要创建一个新的Ndef Messae,需要创建一个NdefMessage对象，并在其中创建至少一个NdefRecord,用于包含你想要传递给目标设备上的应用程序的有效载荷。
>
>创建新的NdefRecord时，必须指定它表示的记录类型，一个MIME类型，一个ID和有效载荷。有几种公共的NdefRecord类型，可以用在Android Beam中来传递数据。要注意，它们总是应该作为第一条记录添加到要传输的消息中。
使用NdefRecord.TNF_MINME_MEDIA类型可以传输绝对URI:

    NdefRecord uriRecord = new NdefRecord(
        NdefRecord.TNF_ABSOLUTE_URI,
        "http://blog.radiativiak.com".getBytes(Charset.forName("US-ASCII")),
          new byte[0], new byte[0]);
>这是使用Android Beam传输的最常见的NdefRecord,因为收到的Intent和任何启动Activity的Intent具有一样的形式。用来确定特定的Activity应该接收哪些NFC消息的Intent Filter可以使用schema,host和path Prefix属性。如果需要传输的消息所包含的信息不容易被解释为`URI`,`NdefRecord.TNF_MIME_MEDA`类型支持创建一个应用程序特定MIME类型，并包含相关的有效载荷：

	    byte[] mimeType = "application/com.paad.nfcbeam".getBytes(Charset.forName("US-ASCII"));
	    byte[] tagId = nwe byte[0];
	    byte[] payload = "Not a URI".getBytes(Charset.forName("US-ASCII"));
	    NdefRecord mimeRecord = new NdefRecord(NdefRecord.TNF_MIME_MEDIA,mimeType,tagId,payload);

>包含Android Application Record(AAR)形式的NdefRecord是一种很好的做法。这可以保证你的应用程序会在目标设备上启动;如果目标设备上没有安装你的应用程序，则会启动google Play Store,让用户可以安装它。
>
>要创建一个ARR Ndef Record,需要使用Ndef Record类的createApplicationRecord静态方法，并指定应用程序的包名，如下所示：

	//创建一条Android Beam NDEF消息 
	String payload = "Two ot beam across";
	String mimeType = "application/com.paad.nfcbeam";
	byte[] mimeBytes = mimeType.getByte(Charset.forName("US-ASCII));
	
	NdefMessage nfcMessage = new NdefMessage(new NdefRecord[]{
	    //创建NFC有效载荷
	    new NdefRecord(
	        NdefRecord.TNF_MIME_MEDIA,
	        mimeBytes,
	        new byte[0],
	        payload.getBytes()),
	        //添加ARR(Android Application Record)),
	        NdefRecord.createApplicationRecord("com.paad,nfcbeam")
	    )
	};
####分配Android Beam有效载荷
>使用NFC Adapter可以指定Android Beam的有效载荷，通过使用NfcAdapter类的`getDefaultAdapter`静态方法，可以访问默认的NFC Adapter:

	NfcAdapter nfcAdapter = NfcAdapter.getDefaultAdapter(this);

>有两种方法可以把以上程序中创建的NDEF Message指定为应用程序的Android Beam有效载荷。最简单的方法是使用`setNdefPushMessage`方法来分配当Android Beam启动时总是应该从当前的Activity发送的消息。通常，这种分配只需要在Activity的onResume方法中完成一次：
	
	nfcAdapter.setNdefPushMessage(nfcMessage,this);

>更好的方法是使用`setNdefPushMessageCallback`方法。该处理程序在消息被传输之前立即触发，允许你根据应用程序当前的上下文---如，在看哪个视频，浏览哪个网页，或者哪个地图坐标居中---动态设置有效载荷的内容，如下程序所示：

	//动态设置android beam消息
	nfcAdapter.setNdefPushMessageCallback(new CreateNdefMessageCallback(){
	    public NdefMessage createNdefMessage(NfcEvent event){
	        String payload = "Beam me up,Android !\n\n"+
	        "Beam Time:" + System.currentTimeMillis();
	        NdefMessage message = createMessage(payload);
	        return message;
	        }
	    },this);
>如果使用回调处理程序同时设置了静态和动态消息，那么只有动态消息会被传输。

####接收Android Beam消息
>Android Beam消息的接收方式与前面介绍的NFC标签十分类似。为了接收在前面打包的有效载荷，首先要在Activity中添加一个新的Intent Filter如下

	<intent-filter>
	    <action android:name="android.nfc.action.NDEF_DISCOVERED"/>
	    <category android:name="android.intent.category,DEFAULT"/>
	    <data android:mimeType="application/com.paad.nfcbeam"/>
	</intent-filter>

>Android Beam启动后，接收设备上的Activity就会被启动，如果接收设备上没有安装你的应用程序，那么Google Play Store将会启动，以允许用户下载你的应用程序。

>传输的数据会使用一个具有`NfcAdapter.ACTION_NDEF_DISCOVERED`动作的Intent传输给你的Activity,其有效载荷可作为一个NdfMessage数组用于存储对应的`NfcAdapter.EXTRA_NDEF_MESSAGES` extra,如下面程序所示：

	//提取Android beam有效载荷
	Parcelable[] messages = intent.getParcelableArrayExtra(NfcAdapter.EXTRA_NDEF_MESSAGES);
	NdefMessage message = (NdefMessage)messages[0];
	NdefRecord record = message.getRecords()[0];
	String payload = new String(record.getPayload());
通常，有效载荷字符串是一个URI的形式，可以像对待Intent内封装的数据一样提取和处理它，以显示合适的视频，网页或地图坐标。

