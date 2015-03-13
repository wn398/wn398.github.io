title: android-蓝牙
date: 2015/3/13 18:52:31 
tags:
- 技术
- android
- 蓝牙

categories: android
---
##蓝牙
>有关蓝牙的类和接口位于android.bluetooth包中。
- BluetoothAdapter  蓝牙适配器（代表本地蓝牙适配器）
- BluetoothClass 蓝牙类（主要包括服务和设备）
- BluetoothClass.Device  蓝牙设备类
- BluetoothClass.Device.Major  蓝牙设备管理
- BluetoothClass.Service 有关蓝牙服务的类
- BluetoothDevice 蓝牙设备（主要指远程蓝牙设备）
- BluetoothServerSocket 监听蓝牙连接的类
- BluetoothSocket 蓝牙连接类

这些蓝牙api允许应用程序连接和断开蓝牙耳机，扫描仪和其他蓝牙设备，包括编写和修改本地服务SDP协议数据库和查询其他蓝牙设备上的SDP协议数据库，在Android上建立RFCOMM协议的连接并连接到其他指定设备上。