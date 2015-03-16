title: android多媒体和闹钟
date: 2015/3/16 17:13:49 
tags:
- 技术
- android
- 多媒体

categroies: android
---
##播放音频
>在android中播放音频文件一般都是使用`MediaPlayer`类来实现的，它对多种格式的音频文件提供了支持

>1. 先调用setDataSource()方法和prepare()方法做好播放准备，

>2. 然后调用play()播放，pause()暂停，stop()停止，reset()设置为刚创建状态

>3. setDataSource()方法可接收一个文件路径，Content Provider URI,流式传输媒体URI路径或者文件描述符

##播放视频
>主要使用VideoView类来实现，它集显示与控制一身,使用它可以快速制作一个系统播放器。

>有两种技术可以用于播放视频内容。

>第一种是使用VideoView类，封装Surface的创建以及Media Player中视频内容的分配和准备。

>第二种技术允许指定自己的Surface并直接操作底层的Media Player实例。
   
>在将Video View添加到UI后，在代码中获得它的引用，然后调用它们的`setVideoPath`或`setVideoURI`来指定播放源。

###创建视频播放表面，使用Surface
>准备用于显示视频的`Surface`.  通常在`layout`中配置一个`surfaceView`对象。`SurfaceView`类是`SurfaceHolder`对象的包装器，后者是`surface`的包装器，而surface用于支持后台线程的可视化更新。

>`Media Player`使用一个`SurfaceHolder`对象来显示视频内容，该对象可以使用`setDisplay`方法进行分配。`SurfaceHolder`是异步创建的，必须等到`surfaceCreated`处理程序被触发，然后再通过实现`SurfaceHolder.Callback`接口将返回的`SurfaceHolder`对象分配给Media Player.在创建和分配`surfaceHolder`给`MediaPlayer`前，使用`setDataSource`方法来指定要播放的资源路径。

	//创建一个新 的mediaPlayer
	mediaPlayer = new MediaPlayer();
	//获得对Surfaceview的引用
	final SurfaceView surfaceView = (SurfaceView)findViewById(R.id.surfaceView);
	//配置SurfaceView
	surfaceView.setKeepScreenOn(true);
	//配置SurfaceHolder并注册回调
	SurfaceHolder holder = surfaceView.getHolder();
	holder.addCallback(this);
	holder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BRFFERS);
	holder.setFixedSize(400,300);

>除了自己使用MediaPlayer自己控制媒体的播放外，andriod还提供了一个`MediaController`.这是一个标准的控件，提供了常用的媒体控制热键。如果我们要使用它，比较好的方法是在代码中实例化它，并把它与视频播放视图相关联，而不是在布局中包含它。以这种方式创建MediaController时，只有将其设置为可见，触摸其所在的VideoView或者与其进行交互时，它才是可见的。如果使用Videoview来显示视频内容，通过调用VideoView的`setMediaController`方法就可以使用Media Controller.

	MediaController mediaController = new MediaController(this);
	videoView.setMediaController(mediaController);
>要直接控制Media Player或者其他音频或视频资源，需要实现一个新的`MediaController.MediaPlayerControl`.

	MediaController mediaController = new MediaController(this);
	mediaController.setMediaPlayer(new MediaPlayerControl(){....});
使用`setAnchorView`方法设置当Media Controller可见时包含在哪个视力中。调用`show`和`hide`分别可以显示和隐藏Media Controller:

	mediaController.setAnchorView(myView);mediaController.show();

###管理媒体播放输出
>Media Player可以使用`setVolume`控制播放过程中每个声道的音量。该方法分别为左右声道采用了一个0到1之间的标题浮点数值（0是静音，1是最大音量)作为参数。 
> 
	mediaPlayer.setVolume(0.5f,0.5f);

>要强制屏幕在视频播放期间不变暗，可以使用 `mediaPlayer.setScreenOnWhilePlaying(true);`

###响应音量控制：
>使用Activity的`setVolueControlStream`方法（通常是在onCreate()方法中调用），可以指定当该Activity处于活动状态时，音频按键控制哪个音频流。但当使用Media Player时，应该指定`STREAM_MUSIC`流，使其作为音频按键的焦点：

	setVolumeControlStream(AudioManager.STREAM_MUSIC);

###响应Media播放控件：
>如果应用程序使用媒体播放器来播放音频和视频，就应该以可以预测的方式响应媒体播放器的按键控制。
一些设备（还有一些入耳式耳机或蓝牙耳机）带有播放，停止，暂停，下一首和前一首媒体播放按键，用户按下这些按键时，系统会广播一个带有`ACTION_MEDIA_BUTTON`动作的Intent.为接收此广播，必须在mainfest文件中声明一个监听此动作的Broadcast Receiver：

	<receiver android:name=".MediaConrolReceiver">
	    <intent-filter>
	        <action android:name="android.intent.action.MEDIA_BUTTON"/>
	    </intent-filter>
	</receiver>
>实现这个Broadcast Receiver在接收到媒体按键被按下的动作时，会创建一个包含相同extra的新Intent,并把该Intent广播给播放音频的Activity.
被按下的媒体按键的代码存储在接收到的Intent的`EXTRA_KEY_EVENT` extra中。

	KeyEvent event = (KeyEvent)intent.getParcelableExtra(Intent.EXTRA_KEY_EVENT);
	//有：
	KeyEvent.KEYCODE_MEDIA_PLAY_PAUSE
	KeyEvent.KEYCODE_MEDIA_PLAY
	KeyEvent.KEYCODE_MEDIA_PAUSE
	KeyEvent.KEYCODE_MEDIA_NEXT
	KeyEvent.KEYCODE_MEDIA_PREVIOUS
	KeyEvent.KEYCODE_MEDIA_STOP;

>如果应用程序希望在Activity不可见时仍在后台播放音频，一个好方法是上Media Player在Service中保持运行，并且使用Intent 来控制媒体播放。

>给定的设备上可能安装了多个应用程序，每个应用程序都被配置为接收媒体按键按下动作，因此还必须使用AudioManager的`registerMadiaButtonEventReceiver`方法将接收者注册为媒体按键按下动作的唯一处理程序。

	AudioManager am = (AudioManager)getSystemService(Context.AUDIO_SERVICE);
	am.registerMediaButtonEventReceiver(component);

###请求和管理音频焦点  
>有些情况下，应用程序即使不可见，或者处在非活动状态，也仍然应该断续响应媒体按键。用户的设备上可能有多个媒体播放器，因此另一个媒体应用程序获得焦点时，让你的应用程序暂停播放并交出媒体按键的控制权是很重要的。类似地，当应用程序进入活动状态时，应该通知其他音频播放应用程序暂停播放，以便它可以获得媒体按键的焦点。
为了在开始播放前请求音频焦点，需要使用Audio Manager的`requestAudioFocus`方法。当请求焦点时，可以指定需要的流（通常是STREAM_MUSIC）以及该流占有焦点的时间---通常是永久占用（如播放音乐）或临时占用（如提供导航提示）。对于临时占有情况，还可以指定临时中断是否可以由当前占有焦点的应用程序通过进入“ducking”状态（减小它的音量）来处理，直到中断完成。

	//永久占用焦点
	int result = am.requestAudioFocus(focusChangeListener,
	//使用音乐流
	AudioManager.STREAM_MUSIC,
	//请求永久焦点
	AudioManager.AUDIOFOCUS_GAIN);
	if(result == AudioManager.AUDIOFOCUS_REQUEST_GRANTED){mdiaPlayer.start()}
>音频焦点将依次分配给每个请求焦点的应用程序，意味着如果另一个应用程序请求音频焦点，你的应用程序就会失去焦点。你在请求音频焦点时注册的`Audio Focus Change Listener`的`AudioFocusChange`处理程序将会通知你焦点丢失情况。
focusChange的情况有：

	AudioManager.AUDIOFOCUS_LOSS_TRANSIENT_CAN_DUCK;//降低音量
	AudioManager.AUDIOFOCUS_TRANSIENT//暂停
	AudioManager.AUDIOFOCUS_LOSS//停止
	AudioManager.AUDIOFOCUS_GAIN//获得
>完成音频播放后，可以选择放弃音频焦点：`am.abandonAudioFocus(focusChangeListener);`

###当音频输出改变时暂停播放：
>如果当前的输出流在入耳式耳机上播放，那么拔出耳机时，系统会自动将输出切换到设备的扬声器。在这种情况下暂停音频或者减小音量是很好的做法。
  为此，需要创建一个监听`AudioManager.ACTION_AUDIO_BECOMING_NOISY`广播并暂停播放的`Broadcast Receiver`

##Remote Control Client简介
>android4中引入了Remote Control Client，使用它，应用程序可以向能够显示元数据，图片和媒体传输控制按键的远程控件提供数据，并响应这些远程控件。

>为了在应用程序中添加对Remote Control Client的支持，必须注册一个`Media Button Event Receiver`.创建一个`Pending Intent`，使其包含一个针对你的Receiver的`ACTION_MEDIA_BUTTON`动作，然后使用该Pending Intent创建一个新的Remote Control Client.你需要使用AudioManager的`registerRemoteControlClient`方法来注册它。如：

	//创建一个将会广播媒体按键按下动作的Pending Intent，将目标组件设为你的Broadcast Receiver
	Intent mediaButtonIntent = new Intent(Intent.ACTION_MEDIA_BUTTON);
	ComponentName component = new ComponentName(this,MediaControlReceiver.class);
	mediaButtonIntent.setComponent(component);
	PendingIntent mediaPendingIntent = PendingIntent.getBroadcast(getApplicationContext(),0,mediaButtonIntent,0);
	//使用Pending Intent 创建一个新的Remote Control Client，并把它注册到Audio Manager中
	myRemoteControlClient = new RemoteControlClient(mediaPendingIntent);
	am.registerRemoteControClient(myRemoteControlClient);
>本例中，Media Control Receiver 将会接收到Remote Control Client按键的按下动作，并把这些动作广播给Activity中注册的Receiver.

>在注册了Remote Control Client后，可以使用它来修改与该Remote Control Client相关的显示屏上显示的元数据。使用`setTransportControlFlags`方法来来定义应用程序支持哪些播放控件。可以使用`setPlaybackState`方法设置某个`RemoteControlClient.PLAYBACK_*`常量来更新音频播放当前状态。

>可以提供与当前正在播放的音频关联的位图，文本字符串和数值---通常它们分别是专辑的艺术图，音轨的名称和已播放的时间。为此需要在Remote Control Client的editMetadata方法中使用MetadataEditor。

	MedadataEditor editor = myRemoteContorlClient.editMetadat(false);
	editor.putBitmap(MetadataEditor.BITMAP_KEY_ARTWORK,artwork);
	editor.apply();

##创建一个Sound Pool
>当应用程序需要低音频延迟并且（或者 ）将同时播放多个音频流时（如播放多种声效和背景音乐的游戏），可以使用SoundPool 类来管理音频。

##使用音效
>AudioEffect有以下5个子类可用：

* Equalizer 可以修改音频输出的频率响应。使用setBandLevel方法可以为特定的频带指定一个增益值
* Virtualizer 使音频的立体声效果更强。它的实现会随着输出设备的配置而发生变化。使用setStrength方法可以将音效的强度设置为0-1000
* BassBoost 增强音频输出的低音频。使用setStrength方法可以将音效的强度设置为0-1000
* PressRever 允许指定多个混 声预设值之一，使音听起来好像在指定房间中播放一样，使用setPreset方法和一个`PresetReverb.PRESET_*`常量可以设置效果与大家厅，小房间，中等房间或大型房间相同的混响。
* EnvironmentalReverb` 与`PresetRever`类似,EnviromentReverb允许通过控制音频输出来模拟不同环境的效果。与PresetReverb不同的是，这个子类允许自己指定每个混响参数，从而可以创建出自定义的效果。

>要将其中某个效果应用到`AudioTrack`或`MediaPlayer`，可以使用AudioTrack或MediaPlayer的`getAudioSessionId`方法找出其唯一音频会话ID.利用这个值，构造想要使用的AudioEffect子类的一个新实例，按照需要修改该实例，最后启用它：

	int sessionId = mediaPlayer.getAudioSessionId();
	short boostStrength =500;
	int priority =0;
	BassBoost bassBoost = new BassBoost(priority,sessionId);
	bassBoost.setStrength(boostStrength);
	bassBoost.setEnabled(true);


##录制歌曲
>使用MediaRecorder类来实现

>当点击“开始”按钮就构建MediaRcorder对象，并且设置声音的来源（`setAudioSource`），输出文件的格式（`setOutputFormat`）,音频文件的编码（`setAudioEncoder`）输出文件的路径（`setOutputFile`）等，然后准备开始录音（prepare）,开始录音（start）

##录制视频：
>Android提供了两种方法来录制视频。

>最简单的是使用Intent启动视频摄像头应用程序。这种方法允许指定输出位置和视频录制质量，同时让本机视频录制应用程序负责用户体验和错误处理。如果想要替换原生的应用程序，或者需要对视频捕获UI或录制设置进行更细致的控制，那么可以使用Media Recoder类。

###使用Intent录制视频
>启动录制最简单的方法就是使用`MediaStore.ACTION_VIDEO_CAPTURE`动作Intent.
使用此Intent启动新Activity将会启动本机视频录制器，允许用户开始，停止，浏览并重新拍摄视频，当他们感到满意后，已录制视频的URI将作为返回Intent的数据参数提供给Activity.

>视频捕获动作Intent可以包含以三个可选的extra:

- MediaStore.EXTRA_OUTPUT  默认情况下，由视频捕获操作录制的视频将存储在默认媒体库中，如果想要在其他地方录制，则可以使用此extra指定一个替代URI.
- MediaStore.EXTRA_VIDEO_QUALITY 当前有两种可能 的值，0表示低质量视频（MMS）,1表示质量视频（全分辨率）。默认情况下选择高分辨率模式
- MediaStore.EXTRA_DURATION_LIMIT 所录制视频的最大长度，单位为秒。

		//示例
		private void startRecording(){
		    Intent intent = new Intent(MediaStore.ACTION_VIDEO_CAPTURE);
		    //启动摄像头应用程序
		   startActivityForResult(intent, RECORD_VIDEO);
		@Override  
		protect void onActivityResult(int requestCode,int resultCode,Intent data){
		    if(requestCode == RECORD_VIDEO){
		        VideoView videoView = (VideoView)findViewById(R.id.videoView);
		        videoView.setVideoURI(data.getData());
		        videoView.start(); 
		    }
		}
		}



##相机设置
>在Android中专门提供了Camera来处理相机相关的事件，Camera是一个专门用来连接和断开相机服务的类。Camera下面包括如下几个事件：
>
1. Camera.AutoFocusCallback:自动调焦功能
2. Camera.ErrorCallback:错误的信息捕捉
3. Camera.Paramerters 相机的属性参数
4. Camera.PictureCallback：拍照，产生图片时触发
5. Camera.PreviewCallback 相机预览设置
6. Camera.ShutterCallback 快门设置
7. Camera.Size：图片的尺寸
8. 
>要在Android中使用相机服务很简单，Camera没有构造方法，只要使用它直接通过open()方法来打开相机设备。

###面部检测和面部特征：
>为了确认设备支持面部检测功能，需要使用Camera对象的`getMaxNumDetectedFaces`方法：

	int facesDetectable = camera.getParameters().getMaxNumDetectedFaces();
>为0不支持。

>在开始使用摄像检测人脸之前，需要分配一个新的`FaceDetectionListener`,使其重写`onFaceDetection`方法，你将得到一个Face对象数组，每个Face对象对应在场景中检测到的一个人脸。每个Face对象都包含一个唯一标识符，只要该Face对象表示的人脸还在场景中，就可以使用该唯一标识符跟踪这个脸。另外，Face对象还包含0-100之间的一个信心分数，表示所检测的确实是一个人脸的可能性。一个包含人脸的矩形，以及每个眼睛和嘴部的坐标。
为了检测和跟踪人脸，需要调用Camera的`startFaceDetection`方法，每次启动时都必须调用，因为预览结束时，它会自动停止

##读取并写入JPEG EXIF图像的详细信息
`ExifInterface`类为读取并修改存储在JPEG文件中的EXIF(可交换图像文件格式)数据提供了一种机制。通过将目标JPEG图像的完整文件名传入ExifInterface构造函数来创建一个新的ExifInterface实例。
 
	ExifInterface exif = new ExifInterface(jpegfilename);
EXIF数据用于为照片存储各种不同的元数据，包括拍摄日期和时间，摄像头设置（如制造商和型号），图像设置（如光圈和快门速度）以及图像描述和位置。为读取EXIF属性，需要调用ExifInterface对象的getAttribute方法，并传入将要读取的属性名。ExifInterface类包含了大量静态TAG_*常量，用于访问通用的EXIF元数据。为了修改EXIF属性，需要使用setAttribute方法，并传入要读取的属性名以及要设置的值。
##闹钟设置
>在Android中可以通过AlarmManager来实现闹钟，AlarmManager类是专门来设定在某个指定的时间去完成指定的事件。AlarmManger提供了访问系统警报的服务，只要在程序中设置了警报服务，AlarmManager就会通过`onReceiver()`方法去执行这些事件，就算系统处于待机状态，同样不会影响运行。可以通过`Context.getSystemService()`方法来获得该服务。
要实现闹钟，首先要创建一个继承自`BroadcastReceiver`的类，实现`onReceive`方法来接收这个Alarm服务，然后通过建立Intent和PendingIntent连接来调用Alarm组件。

##铃声设置
安卓提供了RingtoneManger类专门来操作各种铃声。Android自带的系统铃声都放置在“/system/medio/audio/”文件夹中。



