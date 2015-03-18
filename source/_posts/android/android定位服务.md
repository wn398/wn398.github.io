title: android定位服务
date: 2015/3/17 15:24:58 
tags:
- 技术
- android
- 定位
categories: android
---
##Android中主要借助LocationManager这个类实现定位。

	LocationManager locationManager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);

接着我们需要选择一个位置提供器来确定设备当前的位置。

>Android中一般有三种位置提供器可供选择，GPS_PROVIDER、NETWORK_PROVIDER和PASSIVE_PROVIDER。其中前两种使用的比较多，分别表示使用GPS定位和使用网络定位

将选择好的位置提供器传入到`getLastKnownLocation()`方法中，就可以得到一个Location对象，如下所示：

	String provider = LocationManager.NETWORK_PROVIDER;
	Location location = locationManager.getLastKnownLocation(provider);

这个Location对象中包含了经度、纬度、海拔等一系列的位置信息，然后从中取出我们所关心的那部分数据即可。

如果有些时候你想让定位的精度尽量高一些，但又不确定GPS定位的功能是否已经启用，这个时候就可以先判断一下有哪些位置提供器可用，如下所示：

	List<String> providerList = locationManager.getProviders(true);

可以看到，`getProviders()`方法接收一个布尔型参数，传入true就表示只有启用的位置提供器才会被返回。之后再从providerList中判断是否包含GPS定位的功能就行了。

另外，调用`getLastKnownLocation()`方法虽然可以获取到设备当前的位置信息，但是用户是完全有可能带着设备随时移动的，那么我们怎样才能在设备位置发生改变的时候获取到最新的位置信息呢？LocationManager还提供了一个`requestLocationUpdates()`方法，只要传入一个`LocationListener`的实例，并简单配置几个参数就可以实现上述功能了:

	locationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, 5000, 10,new LocationListener() {
	@Override
	public void onStatusChanged(String provider, int status, Bundle extras) {
	//如果提供器硬件状态改变，则更新应用程序
	}
	@Override
	public void onProviderEnabled(String provider) {
	//如果提供器被启用，则更新应用程序
	}
	@Override
	public void onProviderDisabled(String provider) {
	//如果提供器被禁用，则更新应用程序
	}
	@Override
	public void onLocationChanged(Location location) {
	//基于新位置更新应用程序
	}
	});

这里`requestLocationUpdates()`方法接收四个参数:

1. 第一个参数是位置提供器的类型
2. 第二个参数是监听位置变化的时间间隔，以毫秒为单位
3. 第三个参数是监听位置变化的距离间隔，以米为单位
4. 第四个参数则是LocationListener监听器。

这样的话，LocationManager每隔5秒钟会检测一下位置的变化情况，当移动距离超过10米的时候，就会调用`LocationListener`的`onLocationChanged()`方法，并把新的位置信息作为参数传入。

##另一种接收定位更新的方式
>在android 3.0引入了另外一种接收位置变化的技术。使用这种技术时不需要创建Location Listener,相反，你可指定一个Pending Intent,每当位置变化时，或者Location Provider的状态或可用性发生变化时，该Pending Intent就会被广播。新位置存储在一个extra中，其key为 `KEY_LOCATION_CHANGED`. 

如果有多个Activity或service需要使用位置更新，这种方法特别有用，因为它们可以监听相同的广播Intent.

	Intent intent = new Intent(this,MyLocationUpdateReceiver.class);
	PendingIntent pendingIntent = PendingIntent.getBroadcast(this,locationUpdateRC,intent,flags);
	lcoationManager.requestLocationUpdates(provider,t,distance,pendingIntent);

要想停止位置更新，可以调用`removeUpdates`,参数可以是Location Listener的实例，也可以是不想再触发的Pending Intent.

	locationManager.removeUpdates(myLocationListener):
	locationManager.removeUpdates(pendingInternt);

为了降低电源消耗，只要有可能，就应该在应用程序中禁用更新，特别是当应用程序不可见，而位置改变只是用于更新Activity的UI时候。通过将两次更新之间的最短时间和最短距离设置的尽可能大些，还可以进一步提升性能。

	android.permission.ACCESS_FINE_LOCATION
	android.permission.ACCESS_COARSE_LOCATION

##使用Criteria类可以更好地定义需要的精度类型

	String bestProvider = locationManager.getBestProvider(criteria,true);

如果有多个提供器满足了条件，那么返回精度最高的那个，如果没有任何一个位置提供器满足要求，那么将会按照下面的顺序放宽标准，直到找到一个提供器为止：

- 能耗
- 返回位置的精度
- 方向，速度和海拔的能力
- 返回方向，速度和海拔的能力

##反向地理编码：
Android本身提供了地理编码API,主要是使用GeoCoding API,它比Geocoder更稳定
Geocoding API中规定了很多接口，其中反向地理编码的接口如下： 

http://maps.googleapis.com/maps/api/geocode/json?latlng=40.714224,-73.961452&sensor=true_or_false 
我们来仔细看下这个接口的定义，其中http://maps.googleapis.com/maps/api/geocode/ 是固定的，表示接口的连接地址。json表示希望服务器能够返回JSON格式的数据，这里也可以指定成xml。latlng=40.714224,-73.96145表示传递给服务器去解码的经纬值是北纬40.714224度，西经73.96145度。sensor=true_or_false表示这条请求是否来自于某个设备的位置传感器，通常指定成false即可。
