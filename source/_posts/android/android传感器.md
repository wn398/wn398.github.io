title: android传感器
date: 2015/3/17 15:38:34 
tags:
- 技术
- android
- 传感器
categories: android
---
>Android手机通常会支持多种传感器，如光照传感器，加速度传感器，地磁传感器，压力传感器，温度传感器，陀螺传感器等
>
- Sensor.TYPE_ACCELEROMETER 加速度传感器
- Sensor.TYPE_GYROSCOPE: 陀螺仪传感器
- Sensor.TYPE_LIGHT 亮度传感器
- Sensor.TYPE_MAGNETI_FIELD 地磁传感器
- Sensor.TYPE_ORIENTATION 方向传感器
- Sensor.TYPE_PRESSURE 压力传感器
- Sensor.TYPE_PROXIMITY近程传感器
- Sensor.TYPE_TEMPERATURE 温度传感器

##光照传感器

1. 第一步：获取SensorManager实例：

		SensorManager senserManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
2. 第二步：获取传感器实例
	
	SensorManager是系统所有传感器的管理器，有了它的实例之后就可以调用getDefaultSensor()方法来得到任意的传感器类型了，如下所示：
			
		Sensor sensor = senserManager.getDefaultSensor(Sensor.TYPE_LIGHT);
3. 第三步：监听传感器信号

	这就要借助SensorEventListener来实现了。SensorEventListener是一个接口，其中定义了`onSensorChanged()`和`onAccuracyChanged()`这两个方法

		SensorEventListener listener = new SensorEventListener() {
		@Override
		public void onAccuracyChanged(Sensor sensor, int accuracy) {
		}
		@Override
		public void onSensorChanged(SensorEvent event) {
		}
		};

	当传感器的精度发生变化时就会调用`onAccuracyChanged()`方法，当传感器监测到的数值发生变化时就会调用`onSensorChanged()`方法。可以看到`onSensorChanged()`方法中传入了一个SensorEvent参数，这个参数里又包含了一个values数组，所有传感器输出的信息都是存放在这里的。

4. 第四步：注册监听
	
	下面我们还需要调用SensorManager的`registerListener()`方法来注册SensorEventListener才能使其生效，
	
	registerListener()方法接收三个参数
	
	1. 第一个参数就是SensorEventListener的实例
	2. 第二个参数是Sensor的实例，这两个参数我们在前面都已经成功得到了。
	3. 第三个参数是用于表示传感器输出信息的更新速率，共有`SENSOR_DELAY_UI`、`SENSOR_DELAY_NORMAL`、`SENSOR_DELAY_GAME`和`SENSOR_DELAY_FASTEST`这四种值可选，它们的更新速率是依次递增的。因此，注册一个SensorEventListener就可以写成：
	
			senserManager.registerListener(listener, senser, SensorManager.SENSOR_ DELAY_NORMAL);
	另外始终要记得，当程序退出或传感器使用完毕时，一定要调用`unregisterListener ()`方法将使用的资源释放掉，如下所示：
			
			sensorManager.unregisterListener(listener);

##加速度传感器
>如前面所说的一样，每种传感器的用法都是大同小异的
在使用加速度传感器的时候只需要注意两点。

1. 第一，获取Sensor实例的时候要指定一个加速度传感器的常量，如下所示：
	
		Sensor sensor = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
2. 第二，加速度传感器输出的信息同样也是存放在SensorEvent的values数组中的，只不过此时的values数组中会有三个值，分别代表手机在X轴、Y轴和Z轴方向上的加速度信息。

	![加速度传感器方向](/images/加速度传感器方向.png)

需要注意的是，由于地心引力的存在，你的手机无论在世界上任何角落都会有一个重力加速度，这个加速度的值大约是9.8m/s2。当手机平放的时候，这个加速度是作用在Z轴上的，当手机竖立起来的时候，这个加速度是作用在Y轴上的，当手机横立起来的时候，这个加速度是作用在X轴上的

##方向传感器
>方向传感器会记录手机在所有方向上的旋转角度，其中:
>
>values[0]记录着手机围绕Z轴的旋转角度
>
>values[1] 记录着手机围绕X轴的旋转角度
>
>values[2] 记录着手机围绕Y轴的旋转角度。

遗憾的是，Android早就废弃了`Sensor.TYPE_ORIENTATION`这种传感器类型，虽然代码还是有效的，但已经不再推荐这么写了。事实上，Android获取手机旋转的方向和角度是通过加速度传感器和地磁传感器共同计算得出的，这也是Android目前推荐使用的方式。

首先我们需要分别获取到加速度传感器和地磁传感器的实例，并给它们注册监听器，如下所示：

	Sensor accelerometerSensor = sensorManager.getDefaultSensor(Sensor. TYPE_ACCELEROMETER);
	Sensor magneticSensor = sensorManager.getDefaultSensor(Sensor. TYPE_MAGNETIC_FIELD);
	sensorManager.registerListener(listener, accelerometerSensor, SensorManager.SENSOR_DELAY_GAME);
	sensorManager.registerListener(listener, magneticSensor, SensorManager.SENSOR_DELAY_GAME);

由于方向传感器的精确度要求通常都比较高，这里我们把传感器输出信息的更新速率提高了一些，使用的是SENSOR_DELAY_GAME。
接下来在`onSensorChanged()`方法中可以获取到SensorEvent的values数组，分别记录着加速度传感器和地磁传感器输出的值。然后将这两个值传入到SensorManager的`getRotationMatrix()`方法中就可以得到一个包含旋转矩阵的R数组，如下所示：

	SensorManager.getRotationMatrix(R, null, accelerometerValues, magneticValues);

其中第一个参数R是一个长度为9的float数组，getRotationMatrix()方法计算出的旋转数据就会赋值到这个数组当中。

第二个参数是一个用于将地磁向量转换成重力坐标的旋转矩阵，通常指定为null即可。

第三和第四个参数则分别就是加速度传感器和地磁传感器输出的values值。

得到了R数组之后，接着就可以调用SensorManager的`getOrientation()`方法来计算手机的旋转数据了，如下所示：

	SensorManager.getOrientation(R, values);

values是一个长度为3的float数组，手机在各个方向上的旋转数据都会被存放到这个数组当中。其中values[0]记录着手机围绕着Z轴的旋转弧度，values[1]记录着手机围绕X轴的旋转弧度，values[2]记录着手机围绕Y轴的旋转弧度。
注意这里计算出的数据都是以弧度为单位的，因此如果你想将它们转换成角度还需要调用如下方法：

	Math.toDegrees(values[0]);

SensorManager中还有很多常量和一些常用方法：

- getDefaultSensor  :得到默认的传感器对象
- getInclination: 得到地磁传感器的弧度值
- getOrientation: 得到设备旋转的方向
- getSensorList:得到指定传感器的列表。


##安卓中受支持的传感器：

- Sensor.TYPE_AMBIENT_TEMPERATURE   
	 >返回以摄氏度表示的温度。返回的温度表示环境的室温
- Sensor.TYPE_ACCELEROMETER          
	>三轴加速度传感器，返回三个坐标轴当前加速度，单位m/s2
- Sensor.TYPE_GRAVITY                
	>三轴重力感应器，返回当前的方向和三个坐标轴上的重力分量 m/s2.重力传感器是通过对加速度传感器的结果应用一个低通过滤器，作为一个虚拟传感器实现的
- Sensor.TYPE_LINEAR_ACCELERATION    
	>一个三轴的线性加速度传感器，返回三个坐标轴上不包括重力的加速度，单位m/s2.与重力传感器一样，线性加速度通常是加速记输出，作为一个虚拟传感器实现的
- Sensor.TYPE_GYROSCOPE              
	>一个陀螺仪传感器，以弧度/秒 返回了三个坐标轴上的设备旋转速度。可以对一段时间内的旋转率求积分，以确定设备的当前方向。
- Sensor.TYPE_ROTATION_VECTOR        
	>返回设备的方向，表示为三个轴的角度的组合。通常作为用作传感器管理器的getRotationMatrixFromVector方法的输入，以便将返回的旋转向量转换为旋转矩阵。旋转向量传感器一般被实现为一个虚拟传感器，它可以组合并校正多个传感器（如加速度计和陀螺仪）得到的结果，以提供更加平滑的矩阵
- Sensor.TYPE_MAGNETIC_FIELE         
	>磁力传感器，返回三个坐标轴上的当前磁场，单位为microteslas
- Sensor.TYPE_PRESSURE               
	>一个气压传感器，返回当前的大气压力，单位为millibars毫巴。通过使用传感器管理的getAltitude方法来比较两个位置的气压值，可以把气压传感器用于确定海拔高度。
- Sensor.TYPE_RELATIVE_HUMIDITY      
	>相对温度传感器，以百分比的形式返回当前相对温度。
- Sensor.TYPE_PROXIMITY             
	>近距离传感器，以厘米为单位返回设备和目标对象之间的距离。
- Sensor.TYPE_LIGHT                 
	>光传感器，返回一个lux(勒克斯)为单位的值，用于描述环境的亮度。



