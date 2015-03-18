title: android绘图
date: 2015/3/17 9:21:33 
tags:
- 技术
- android_UI
categories: android
---
##Graphics包
Graphics包中包括了`Canvas`,`Paint`,`Color`,`Bitmap`,2D几何图形等常用类。Graphics具有*绘制点，线，颜色，图像处理，2D几何图形*等功能

绘制图像用`canvas.drawBitmap`,可以利用Matrix来实现图像的旋转和缩放功能

##图像特效
在玩游戏时经常看到一些图像的特效，比如半透明等效果。要实现这此效果并不难，只需要对图像本身的像素执行操作。Android中Bitmap同样提供了操作像素的方法，可以通过`getPixels`方法来获得该图像的像素并放到一个数组中，我们处理这个像素组就可以了，最后通过`setPixels`设置这个像素数组到`Bitmap`中。每一个图像像素通过一个4字节整数来展现：最高位字节用作`Alpha`通道，即用来实现透明与不透明控制，255代表完全不透明，0则代表完全透明，接下来一个字节是Red红色通道，255代表完全是红色。依次类推，接下来两个字节相应地实现绿色和蓝色通道。

##图像渲染
Android中提供了Shader类专门来渲染图像以及一些几何图形，Shader下面包括几个直接子类，分别是`BitmapShader`,`ComposeShader`,`LinearGradient`,`RadialGradient`,`SweepGradient`.

- BitmapShader主要用来渲染图像
- LinearGradient用来进行线性渲染
- RadialGradient用来进行环形渲染
- SweepGradient用来进行梯度渲染
- ComposeShader则是一个混合渲染

Shader类的使用，都需要先构建Shader对象，然后通过Paint的setShader方法来设置渲染对象，然后在绘制时使用这个Paint对象即可。

##双缓冲技术
双缓冲技术是游戏开发中的一个重要技术，主要原理：当一个动画争先显示时，程序又在改变它前面还没有显示完，程序又请求重新绘制，这样屏幕就会不停闪烁。为了避免闪烁，可以使用双缓冲技术，将要处理的图片都在内存中处理好之后，再将其显示到屏幕上。这样显示出来的总是完整的图像。双缓冲的核心技术就是先通过`setBitmap`方法将要绘制的所有图形绘制到一个Bitmap上，然后再来调用`drawBitmap`方法绘制出这个Bitmap,显示在屏幕上。

##强化View:高级canvas绘图
----
###Shader简介
>它的派生类可以创建允许使用多种纯色填充绘图对象的Paint,对Shader最常用的是定义渐变填充。Android包含了一个`BitmapShader`和一个`ComposeShader`,同时还包含3个渐变Shader:
>
>1. LinearGradient   线性渐变
>2. RadialGrandient  辐射渐变
>3. SweepGradient    扫描渐变

渐变Shader可以让你使用交替改变的颜色来填充绘图，可以通过两种方式定义渐变:

>第一种方法是将颜色渐变定义为两种颜色的简单交替：
>
		int colorFrom = Color.BLACK;
		int colorTo = Color.WHITE;
		LinearGradient myLinearGradient = new LinearGradient(x1,y1,x2,y2,colorFrom,colorTo,TileMode.CLAMP);
>第二种方法是指定一个更复杂的颜色序列，它是按照设定的比例对颜色进行分布的：
>
	int[] gradientColors = new int[3];
	gradientColors[0] = Color.GREEN;
	gradientColors[1] = Color.YELLOW;
	gradientColors[2] = Color.RED;
	float[] gradientPositions =new float[3];
	gradientPositions[0] = 0.0f;
	gradientPositions[1] = 0.5f;
	gradientPositions[2] = 1.0f;
	RadialGradient radialGradientShader = new RadialGradient(centerX,centerY,radius,gradientColors,gradientPositions,TileMode.CLAMP);

###使用Shader TileMode
>渐变shader的画刷大小即可以显式地使用包围矩形来定义，也可以使用中心点和半径长度来定义。位图shader可以可以通过它的位图大小来决定其画刷的大小。如果Shader画刷所定义的区域比要填充的区域小，那么TileMode将会决定如何处理剩余的区域，可以用以下静态常量定义使用哪种平铺模式：
>
- CLAMP    使用Shader的边界颜色来填充剩余空间
- MIRROR   在水平和垂直方向拉伸shader图像，这样每一幅图像就能与上一幅吻合
- REPEAT   在水平和垂直方向重复Shader图像，但不拉伸它

###使用MaskFilter
>MaskFilter可以为Paint分配边缘效果，如果Canvas是硬件加速的，则不支持Mask Filter. 对Mask Filter的扩展可以对一个Paint边缘的alpha通道应用转换

>Android包含了下面几种MaskFitler:
>
- BlurMaskFilter   指定了一个模糊的样式和半径来羽化Paint的边缘
- EmbossMaskFilter 指定了光源的方向和环境光强度来添加浮雕效果
 要应用一个MaskFilter 可以使用`setMaskFilter`方法，并传递给它一个MaskFilter对象：

	//设置光源的方向
	float[] direction = new fload[]{1,1,1};
	//设置要应用的环境光亮度
	float light = 0.4f;
	//选择要应用的反射等级
	float specular = 6;
	//向蒙版应用一定级别的模糊
	float blur = 3.5f;
	EmbossMaskFilte emboss = new EmbossMaskFilter(direction,light,specular,blur);
	//应用蒙版
	if(canvas.isHardwareAccelerated()){
	    myPaint.setMaskFilter(emboss);
	}
   
###使用ColorFilter
>MaskFilter是对一个Paint的alpha通道应用转换，而colorFilter则是对每一个RGB通道应用转换，所有由ColorFilter派生的类在执行它们的转换时都会忽略alpha通道。
>
>Android包含3个ColorFilter:
>
- ColorMatrixColorFilter 可以指定一个4*5的ColorMatrix并将其应用到一个Paint中，颜色矩阵通常用于在应用程序中对图像进行处理，而且由于支持使用矩阵相乘的方法来执行链式转换，因此它们很有用。
- LightingColorFilter  将RGB通道乘以第一个颜色，然后加上第二个颜色。每一次转换的结果都限制在0~255之间
- PorterDuffColorFilter 可以使用数字图像合成的16条Porter-Duff规则中的任意一条来向Paint应用一个指定的颜色。

使用`setColorFilter`方法应用`ColorFilter`:
	
	myPaint.setColorFilter(new LightingColorFilter(Color.BLUE,Color.RED));

###使用PathEffect
>前面所有效果都是影响Paint填充图像的方式，PathEffect是用来控制绘制轮廓线（笔划）的方式.使用PathEffect可以改变一个形状的边角的外观，并且控制轮廓的外观。

>Android包含了多个PathEffect,包括：
>
- CornerPathEffect  可以使用圆角来代替尖锐的角，从而对基本图形的形状尖锐的边角进行平滑处理
- DashPahtEffect 可以使用DashPathEffect来创建一个虚线的轮廓（短横线/小圆点），而不是使用实线。你还可以指定任意的虚/实线段的重复方式
- DiscretePathEffect 与DashPathEffect相似，但是添加了随机性。当绘制它的时候，需要指定每一段的长度和与原始路径的偏离程序。
- PathDashPathEffect  这种效果可以定义一个新的形状（路径）并将其用作原始路径的轮廓标记。
下面的效果可以在一个Paint中组合使用多个PathEffect。
- SumPathEffect  顺序地在一条路径中添加两种效果，这样每种效果都可以应用到原始路径中，而且两种结果可以结合起来
- ComposePathEffect  将两种效果组合起来应用，先使用每一种效果，然后在这种效果的基础上再应用每二种效果
修改所绘制对象的形状的PathEffect会改变受影响的形状区域。这就能够保证应用到相同形状中的填充效果将会绘制到新的边界中。

###改变转换模式
>可以通过Paint的`Xfermode`来影响在画布中已有的图像上面绘制新的颜色的方式。正常情况下，在已有的图像上绘图将会在其上方添加一层新的形状。如果新的Paint是完全不透明的。那么它将完全遮挡下面的Paint.如果它是部分透明的，那么它将会被染上下方的颜色。
>
下面的Xfermode子类可以改变这种行为：
>
- AvoidXfermode  指定了一个颜色和容差，强制Paint避免在它上方绘图（或者只在它上方绘图）
- PixelXorXfemode 当覆盖已有的颜色时，应用一个简单的像素XOR操作
- PorterDuffXfermode 这是一个非常强大的转换模式，通过它可以使用图像合成的16条PorterDuff规则的任意一条来控制Paint如何与已有的画布图像进行交互。

要应用转换模式，可以使用`setXferMode`方法：
 	
	AvoidXfermode avoid = new AvoidXfermode(Color.BLUE,10,AvoidXfermode.Mode.AVOID);
	borderPen.setXfermode(avoid);

###使用抗锯齿效果提高Paint质量
>在创建一个新的Paint对象时，可以通过传递给它一些标志来影响渲染它的方式。`ANTI_ALIAS_FLAG`是其中一种，它可保证在绘制斜线的时候使用抗锯齿效果来平滑该斜线的外观（代价是会降低性能）。

>在绘制文本时，抗锯齿效果最重要，因为经过搞锯齿效果处理之后的文本非常容易阅读。要创建更加平滑的文本效果，可以应用`SUBPIXEL_TEXT_FLAG`,它将会应用子像素抗锯齿效果。
>
	Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG|Paint.SUBPIXEL_TEXT_FLAG);
也可以使用setSubpixelTest和setAntiAlias方法来设置这些标志
>
	myPaint.setSubpixelText(true);
	myPaint.setAntiAlias(true);

可以为application或activity指定是否硬件加速  `android:hardwareAccelerated="true"`

##Surface View简介
>一般情况下应用程序View都是在相同的GUI线程中绘制的，这个应用程序线程同时也用来处理所有的用户交互。当需要快速更新View 的UI，或者当渲染代码阻塞GUI线程的时间过长的时候，SurfaceView就能解决这些。SurfaceView封装了一个Surface对象而不是画布。因为可以使用后台线程绘制surface.但独立于GUI线程进行绘图的代价是增加内存消耗。
###View事件
----
>View的事件：
>
- onTouchEvent 触摸屏事件处理程序，当触摸屏被触摸，释放或者拖动时触发
- onKeyDown  当按下任何硬件按键时调用
- onKeyUp 当释放任何硬件按键时调用
- onTrackballEvent 当轨迹球移动时触发

要创建一个使用触摸屏进行交互的View或者Activity，需要重写onTouchEvent处理程序,当用户触摸屏幕的时候，会触发onTouchEvent事件处理程序，每当位置发生改变时就触发一次，当触摸结束的时候还会触发一次。


在Android 2.0引入了对处理任意数量的并发触摸事件的平台支持。每个触摸事件将被分配一个独立的指针标识符，该标识符将在`MotionEvent`参数中得到引用。对MotionEvent参数调用`getAction`以找出触发该处理程序的事件类型。对于单点设备或多点触摸设备上的第一个触摸事件，可以使用`ACTION_UP/DOWN/MOVE/CANCEL/OUTSIDE`常量来确定事件类型。

	public boolean onTouchEvent(MotionEvent event){
	    int action = event.getAction();
	    switch(action){
	    case(MotionEvent.ACTION_DOWN)://按下触摸屏
	    case(MotionEvent.ACTION_MOVE);//触摸点在屏幕上移动
	    case(MotionEvent.ACTION_UP)://触摸结束
	    case(MotionEvent.ACTION_CANCEL): //触摸事件取消
	    case(MotionEvent.ACTION_OUTSIDE)://移动操作发生在被监控的屏幕元素的边界之外
	
	    }
	}
要使用多个指针跟踪触摸事件，可以分别应用`MotionEvent.ACTION_MASK`和`MotionEvent.ACTION_POINTER_ID_MASK`来确定触摸事件和触发该事件的指针id。调用`getPointCount`可以找出触摸事件是否是一个多点触摸事件。

###跟踪触摸移动
MotionEvent参数还包含了应用屏幕上的压力。
MotionEvent参数还可以包含历史值。可以调用`getHistorySize`来获得历史的大小值

###状态列表Drawable
状态列表Drawable是一种复合资源，允许根据包含该状态列表Drawable的view的状态指定一个不同的Drawable
大多数android原生View都使用状态列表Drawable.包括button上使用的图像和标准ListView项使用的背景

要想定义一个状态列表Drawable，创建一个包含了<selector>标记的xml文件。添加一些item子节点，每个子节点使用`android:state_*`属性和`android:drawable`属性指定特殊状态下对应的Drawable:

	<selector >
	<item android:state_pressed="true"   android:drawable="..">
	<item android:state_focused="true"   android:drawable="..">
	</selector>
state列表：

* android:state_pressed      按下或者没有按下
* android:state_focused      有焦点或者没有焦点
* android:state_hovered      光标在view上悬停或者不悬停
* android:state_selected     选中或没有选中
* android:state_checkable    能或者不能选中
* android:state_checked      是选中状态或者不是选中状态
* android:state_enable       启用或者禁用
* android:state_activated    激活或者未激活
* android:state_window_focused    父窗口有焦点或者没有焦点