title: android网络wifi
date: 2015/3/13 18:28:49 
tags:
-  技术
-  android
- wifi网络

categories: android
---
##管理网络和Internet连接
>Android可以广播用于监视网络连接变化的Intent，并提供了对网络设置和连接进行控制的API.

###Connectivity Manager
>Android网络主要是通过ConnectivityManager来处理的，该服务使你可以监视连接状态，设置自己的首选项网络连接以及管理连接失败转接。

####Connectivity Manager简介
>它用于代表网络连接服务，用于监视网络连接状态，配置故障转移设置以及控制网络无线电。 为使用connectivity Manager,应用程序需要读取和定入网络状态访问权限

	<user-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
	<user-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
   为了访问Connectivity Manager,使用getSystemService()，并传入`Context.CONNECTIVITY_SERVICE`作为服务名称。

####查找和监视网络连接

`GetActiveNetworkInfo()`方法会返回一人上NetworkInfo对象，其中包含了当前活动网络的详细信息。

	//获得活动网络的信息
	NetworkInfo activNetwork = connectivity.getActiveNetworkInfo();
也可以使用getNetworkInfo方法获得指定类型的非活动网络的详细信息。使用返回的NetworkInfo了解所返回的网络的连接状态，网络类型以及详细的状态信息。

在尝试传输数据之前，应该配置一个重复警报，或者调度一个执行数据传输的后台服务，并使用Connectivity Manager检查是否连接到了Internet,如果是，则确认连接的类型，如：

	NetworkInfo network = connectivity.getActiveNetworkInfo();
	boolean isConnected = ((network !=null)&&(network.isConnectedOrConnecting());
	boolean isWiFi = (network.getType() == ConnectivityManager.TYPE_WIFI);

为了监视网络连接，可以创建一个`BroadcastReceiver`来监听ConnectivityManager.CONNECTIVITY_ACTION Broadcast Intent.如下所示

	<receiver android:name=".ConnectivityChangedReceiver">
	    <intent-filter>
	        <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
	    </intent-filter>
	</receiver>
这些Intent包含了一些extra,它们提供了关于连接变化的额外详细信息。可以使用Connectivity-Manager类中可用的静态常量访问每个extra.EXTRA_NO_CONNECTIVITY是最常用的extra,它是一个布尔值，当设备未连接到任何网络时返回true.当它为false时，意味着有一个活动的连接，使用`getActiveNetworkInfo()`来获得新连接状态的更多详细信息并根据情况修改下载计划是一种很好的作法、




##Wifi

>Android也提供了android.net.wifi包供我们操作。

###wifi类里主要包括以下几个类和接口。

1. ScanResult
	主要用来描述已经检测出的接入点，包括接入点的地址，接入点的名称，身份认证，频率，信号强度等信息
2. WifiConfiguration
	Wifi网络的配置，包括安全配置等
3. WifiInfo
	Wifi无线连接的描述，包括接入点，网络连接状态，隐藏的接入点，IP地址，连接速度，MAC地址，网络ID，信号强度等信息
4. WifiManager
	提供了管理？Wifi连接的大部分API，它主要包括如下内容：
	- 已经配置好的网络的清单，这个清单可以查看和修改，而且可以修改个别记录的属性
	- 当连接中有活动的Wi-fi网络时，可以建立或者关闭这个连接，并且可以查询有关网络状态的动态信息
	- 对接入点的扫描结果包含足够的信息来决定需要与什么接入点建立连接
	- 还定义了许多常量来表示wi-fi状态的改变

	为使用WifiManager需要权限

		android.permission.ACCESS_WIFI_STATE
		android.permission.CHANG_WIFI_STATE;
		WifiManger wifiManger = (WifiManager)getSystemService(Context.WIFI_SERVICE)
	可以使用Wi-fi Manager的setWifiEnable方法启用或禁用Wi-fi硬件，或者使用getWifiState或者isWifiEnabled方法请求当前的Wi-Fi状态。

5. WifiManger.WifiLock
允许程序一直使用wifi无线网络，通常情况下当用户在一段时间内没有任何操作时，WiFi网络就会自动关闭。我们可以通过WifiLock来锁定Wifi网络，使其一直保持连接，直到这个锁定被释放。当有多个应用程序时可能会有多个wifilock,在无线电设备中必须保证任何一个应用中都没有使用wifilock时，才可以关闭它，在使用一个wifi锁之前，必须确定应用需要wifi的接入，或者能够在移动网络下工作。注意wifi锁不能超越客户端的wifi Enabled设置，也没有飞行模式，此时它们仍然认为wifi在运行，但是设备处于空闲状态。

###监视Wi-Fi 连接
>多数情况下，使用Connectivity Manager监视Wi-Fi连接变化是最佳实践，每当Wi-Fi网络连接状态网络发生改变时，Wi_fi Manager确实会广播Intent,它会使用在WifiManager类中定义的下列任意一种常量：

- *WIFI_STATE_CHANGED_ACTION wifi* 硬件状态发生变化，包括`enabling`,`enabled`,`disabling`,`disabled`和`unknown`几种状态。它包含了`EXTRA_STATE`和`EXTRA_PREVIOUS_STATE`这两个extra,分别提供了新的和前一次的Wi-Fi状态
- *SUPPLICANT_CONNECTION_CHANGE_ACTION* 每当与活动的请求方（接入点）之间的连接状态发生变化时广播该Intent.当新连接建立或现有连接丢失时就使用`EXTRA_NEW_STATE`触发它，并在建立新连接时该布尔值为true.
- *NETWORK_STATE_CHANGED_ACTION* 每当wifi连接状态发生变化时触发。该Intent包含两个extra.一个是 * `EXTRA_NETWORK_INFO`，其中包含详细描述了当前网络状态的`NetworkINfo`对象，第二个是`EXTRA_BSSID`,其中包含所连接的接入点的BSSID.
- *RSSI_CHANGED_ACTION*  可以通过监听RSSI_CHANGED_ACTION Intent来监视已连接wifi网络的当前信号强度。为了利用该信号强度，应当利用wifi manager的`calculateSignalLevel()`静态方法，以便按照你指定的范围将该信号强度转换成一个整形数值。

当建立了wifi连接后，可以使用Wi-Fi Manager的`getConnectionInfo`方法找出连接的状态信息。所返回的WifiInfo对象包含当前接入点的`SSID`,`BSSID`,`Mac地址`，`IP地址`，以及当前的链路速度和信号强度。

###扫描热点：
可使用WiFi Manager的`startScan()`方法进行接入点扫描。一个带有`SCAN_RESULTS_AVAILABLE_ACTION`动作的Intent将被广播以便异步宣布扫描完成并且结果可用。调用`getScanResult()`以获得这些结果，它们是ScanResult对象的列表。每个扫描结果包含了为每个检测到的接入点所检索到的详细信息，包括链路速度，信号强度，SSID以及所支持的身份验证技术。

###管理wifi配置
可使用wifi Manager管理已配置的网络设置并控制将要连接到哪个网络。一旦连接建立，就可以通过查询可用网络连接来获得其配置和设置的更多详细信息。

使用`getConfiguredNetworks` 可以获得当前网络配置的列表。所返回的`WifiConfiguration`对象列表包含每个配置的网络ID,SSID和其他详细信息。为了使用某个特定的网络配置，需要使用enableNetwork方法，并传入要使用的网络ID,同时将disableAllOthers参数指定为true

	//获得可用配置的一个列表
	List<WifiConfiguration> configurations = wifi.getConfiguredNetworks();
	//获得第一个配置 的网络ID
	if(configurations.size()>0){
	    int netId = configurations.get(0).networkId;
	//启用该网络
	  boolean disableAllOthers = true;
	wifi.enableNetwork(netId,disableAllOthers);	
	}

###创建Wifi网络配置
为了连接到一个wifi网络，需要创建并注册一个配置。正常情况下，用户将使用本机Wifi配置设置进行该操作，但是也可以在自己的应用程序中提供相同的功能，甚至可以完全替换本机wifi配置activity.
   
网络配置作为`WifiConfiguration`对象进行存储。下面是每个wifi配置可用的不公共域的不完全列表：

- BSSID       每个接入点的BSSID
- SSID        特定网络的SSID
- networkId   用于在当前设备上标识这个网络配置的唯一标识符
- priority    当对要连接到的潜在接入点的列表进行排序时用到的网络配置优先级
- status      该网络连接的当前状态，将会是下面结果之一：`WifiConfiguration.Status.ENABLED`,    `WifiConfiguration.Status.ENABLE`，或者是`WifiConfiguration.Status.CURRENT`.
 
wificonfiguration对象还包含了所支持的验证技术，以及先前用来验证该接入点所用的密钥。`addNetwork`方法可以指定一个新的配置并将其添加到当前列表中，与此类似`updateNetworks`方法可以通过传入只包含一个网络ID和想要更改的值的WifiConfiguration对象来更新网络配置。也可以使用`removeNetwork`方法传入一个网络ID以删除某个配置。为了保存对网络配置所做的任何更改，必须调用`saveConfiguration`方法。

###使用Wifi Direct传输数据
>Wifi Direct是一种通信协议，用于中等距离，高带宽的点对点通信。与蓝牙相比，Wifi Direct更加快速可靠，而且工作距离更远。
>
>使用Wifi Direct API 可以在Wifi Direct的工作范围内搜索并连接到其他Wifi Direct设备。通过使用套接字建立通信链接，可以在支持的设备（包括一些打印机，扫描仪，摄像头和电视）之间以及在运行在不同设备上的你的应用程序的实例之间传输和接收数据流。

####初始化 Wi-Fi Direct框架
>为使用Wi-Fi Direct ，应用程序需要有 `ACCESS_WIFI_STATE`,`CHANGE_WIFI_STATE`和`INTERNET`权限.wifi Direct连接是使用`WifiP2pManager`系统服务建立和管理的，通过使用`getSystemService`方法并传入`Context.WIFI_P2P_SERVICE`常量，可以访问WifiP2pManager系统服务。

>在使用Wifi P2P Manager之前，必须使用`WifiP2pManager`的`initializae`方法创建连接到wifi Direct框架的一个通道，需要向该方法传入当前的Context,Looper用于接收Wifi Direct事件和ChannelListener用于监听通道连接丢失情况。如下：

	private WifiP2pManager wifiP2pManger;
	private Channel wifiDirectChannel;
	private void initializeWiFiDirect(){
	    wifiP2pManager = (WifiP2pManager)getSystemService(Context.WIFI_P2P_SERVICE);
	    wifiDirectChannel = wifiP2pManager.initiallize(this,getMainLooper(),
	    new ChannelListener(){
	    public void onChannelDisconnected(){
	        initializeWiFiDirect();
	    }   
	});
	}
>使用这个通道与Wi-fi Direct框架进行交互。因此，Wifi P2P Manager的初始化操作通常是在Activity 的`onCreate`处理程序内完成的。使用wifi p2p manager执行的大多数动作（如找到和连接对等设备的尝试）会使用一个`ActionListener`立即指出它们是否成功。如下代码所示，动作成功时，通过接收Broadcast Intent可以得到与这些动作关联的返回值，

	//创建一个P2p manager动作监听器
	private ActionListener actionListener = new ActionListener(){
	    public void onFailure(int  reason){
	       String errorMessage = "WiFi Direct Failed";
	       switch(reason){
	        case WifiP2pmanager.BUSY:
	             errorMessage +="Framework busy."; break;
	       case WifiP2pManager.ERROR:
	            errorMessage +="Internal error."; break;
	       case WifiP2pManager.P2P_UNSUPPORTED:
	            errorMessage += "Unsupported."; break;
	       default:
	             errorMesage +="Unknown error."; break;
	        
	        }
	    public void onSuccess(){
	        //成功
	        //返回值将通过一个Broadcast Intent返回
	    }
	    }
	}

####启用 Wi-Fi Direct并监视其状态

>为使一个Android设备能够发现其他wifi Driect设备或被其他wifi direct设备发现，用户首先必须启用wifi direct。为此可以使用`android.provider.Settings.ACTION_WIRELESS_SETTINGS`类启动一个新的Activity,从而启动设置屏幕，供用户修改此设置，如下代码所示：

	Intent intent = new Intent(android.provider.Settings.ACTION_WIRESS_SETTINGS);
	startActivity(intent);

>只有建立连接并传输数据时，wifi Direct才会一直保持启用状态。如果短时间不用，它就会自动禁用。只有设备上启用了Wifi Direct时，才能够执行wifi Driect操作，因此，监听Wifi Direct的状态变化，并通过修改UI来禁用不可行操作是非常重要的。 

>通过注册一个接收`WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTION`动作的`BroadcastReceiver`,可以监视wifi Direct的状态。

	IntentFilter p2pEnalbeFilter = new IntentFilter(WifiP2pManager.WIFI_P2P_STATE_CHANGED_ACTION);
	registerReceiver(p2pStatusReceiver,p2pEnabledFilter);

  相关的Broadcast Receiver 收到的Intent(如下代码所示)包含一个被设置为`WIFI_P2P_STATE_ENABLED`或`WIFI_P2P_STATE_DISABLED`的`wifiP2pManager.EXTRA_WIFI_STATE extra`.

	//接收 wifi Direct的状态变化
	BroadcastReceiver p2pStatusReceiver = new BroadcastReceiver(){
	    public void onReceive(Context context,Intent intent){
	        int state = intent.getIntExtra(WifiP2pManager.EXTRA_WIFI_STATE,
	         WifiP2pManager.WIFI_P2P_STATE_DISABLED);
	       switch(state){
	        case(WifiP2pManager.WIFI_P2P_STATE_ENABLE):
	            buttonDiscover.setEnabled(true);
	           break;
	        default:
	            buttonDiscover.setEnalbed(false);
	
	}
	    }
	}
在onReceive处理程序内，可以根据Wifi Direct的状态变化相应地修改UI。

####发现对等设备
>为扫描对待设备，需要调用`WiFi P2P manager`的`discoverPeers()`方法，并传入一个处于活动状态的通道和一个Action Listener。对等设备列表的变化将作为一个Intent,通过使用`WifiP2pManager.WIFI_P2P_PEERS_CHANGED_ACTION`动作广播出去。在建立一个连接或创建一个组之前，对等设备的搜索过程会一直进行。当收到通知你对等设备列表发生变化的Intent后，可以使用`WifiP2pManager.requestPeers`方法请求当前发现的对等设备的列表。如下所示：

	//发现wifi Direct对等设备
	private void discoverPeers(){
	    wifiP2pManager.discoverPeers(wifiDirectChannel,actionListener);
	}
	BroadcastReceiver peerDiscoverReceiver = new BroadcastReceiver(){
	    public void onReceive(Context context,Intent intent){
	        wifiP2pManager.requestPeers(wifiDirectChannel,new PeerLislistener(){
	            public void onPeerAvailabe(WifiP2pDeviceList peers){
	                deviceList.clear();
	                deviceList.addAll(peers.getDeviceList()):
	                   aa.notifyDataSetChanged();
	            }
	        }
	    }
	}

   `requesetPeers()`方法接受一个`PeerListListener`,当检索对待设备列表时，就会执行`PeerListListener`的`onPeersAvailable`处理程序。对待设备列表可以通过`WifiP2pDeviceList`访问，你可以查询它来找出所有对等设备的名称和地址。

####连接对等设备
>为了与对等设备建立wifi Direct连接，需要使用`wifi P2p Manage`r的`connect`方法，并传入活动的通道，一个ActionListener以及一个指定了要连接的对等设备的地址的WifiP2pConfig对象，如下所示：

	  private void connectTo(WifiP2pDevice device){
	    WifiP2pConfig config = new WifiP2pConfig();
	      config.deviceAddress = device.deviceAddress;
	      wifiP2pManager.connect(wifiDirectChannel,config,actionListener);
	    
	}

>当尝试建立连接时，远程设备就会被提示接受连接请求。在Android设备上，这需要用户手动接受连接请求。如果远程设备接受了建立连接的请求，成功的连接将使用`WifiP2pManager.WIFI_P2P_CONNECTION_CHANGED_ACTION` Intent动作在两个设备上广播。

>Broadcast Intent将包含一个打包在`WifiP2pManager.EXTRA_NETWORK_INFO` extra中的NetworkInfo对象。可以查询Network Info来确认连接状态的变化表示建立了一个新的连接还是已有的连接断开了：
  
	boolean connected = networkInfo.isConnected();

>如果是建立了新连接，则可以使用`WifiP2pManager.requestConnectionInfo`方法查询连接的详细信息。需要向该方法传入活动的 通道以及一个ConnectionInfoListener,如下所示：

	//连接到Wifi Direct对等设备
	BroadcastReceiver connectionChanagedReceiver = new BroadcastReicever(){
	    public void onReceive(Context context,Intent intent){
	        //提取NetworkInfo
	        String extraKey = WifiP2pmanger.EXTRA_NETWORK_INFO;
	       NetworkInfo networkInfo = (NetworkInfo)intent.getParcelableExtra(extraKey);
	        //检查连接是否已经连接
	        if(networkInfo.isConnected()){
	            wifiP2pManager.requestConnectionInfo(wifiDirectChannel,
	                new ConnectionInfoListener(){
	                    public void onConnectionInfoAvailable(WifiP2pInfo info){   
	                        //如果建立了连接
	                            if(info.groupFormed){
	                                    //如果这个设备是服务器
	                                    if(info.isGroupOwner){//TODO:启动Server Socket}
	                                    //如果这个设备是客户机
	                                       else if(info.groupFormed)｛//TOD:启动Client Socket｝
	                            }
	                        
	                     }
	                }
	        }
	    }
	
	}

>当连接信息可用时，`ConnectionInfoListener`会触发其`onConnectionInfoAvailable`处理程序，并传入一个包含这些详细信息的WifiP2pInfo对象。建立连接后，所连接的对等设备会形成一个组。连接发起者将作为该组的所有者返回，并且通常（并非总是）承担服务器的角色进行进一步通信。建立连接后，可以使用Tcp/IP套接字在设备之间传输数据。

####在对等设备之间传输数据
>这是用java的套接字传输数据一样的