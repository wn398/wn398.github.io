title: android动画
date: 2015/3/17 9:32:54 
tags:
- 技术
- android_UI
categories: android
---
>Android平台提供了三类动画:

>一类是Tween动画即补间view动画，应用于view,可以定义一系列关于位置，大小，旋转，和 透明度的改变，从而让View的内容动起来。补间动画它以最小的资源消耗向用户提供深度，移动或者反馈。它使用动画来进行方向，大小，位置和透明度的改变比通过手动重新绘制Canvas来达到相似效果要消耗更少的资源，更不用提在实现方面的简单性。

>第二类是Frame动画即逐帧动画，即顺序播放事先做的图像，和电影类似。还有Gif动画的实现原理

>第三类是差值属性动画： 它几乎可以让应用程序的任何对象动起来。它是一个框架，在一定时间内，通过使用指定的内插技术来影响任意的对象属性。

##Tween动画（补间view动画）
>Tween动画通过对View的内容完成一系列的图形变换（平移，缩放，旋转，改变透明度）来实现动画效果。它主要包括4种动画效果：
>
1. Alpha:  渐变透明度动画效果
2. Scale:  渐变尺寸伸缩动画效果
3. Translate:画面转换位置移动动画效果
4. Rotate: 画面转移旋转动画效果

>经常用于：
>
1. Activity间的转换
2. Activity内的布局间的转换
3. 相同view中的不同内容间的转换
4. 为用户提供反馈，如提示进度，通过”晃动“输入框来说明错误或者无效的数据输入

具体来讲，Tween动画是通过预先定义一组指令，这些指令指定了图形变换的类型，触发时间，持续时间。程序沿着时间线执行这些指令就可以实现动画效果。补间动画是使用Animation来创建的,因此要首先需要定义Animation动画对象，然后设置该动画的一些属性，最后通过startAnimation方法来开始动画。
各个动画的属性如下：

1. AlphaAnimation(float from,float to);//可以改变View的透明度（不透明或alpha混合）

2. ScaleAnimation(float fromx,float tox,float foromy,float toy,in pivotXtype,float pivotXvalue,int pivotYtype,float pivotYValue);//允许缩放选中的View

3. TranslateAnimation(float fromXDelta,float toXDelta,float fromYDelta,float toYDelta);//可以在屏幕上移动选中的view(但只能在原始边界的范围内显示)

4. RotateAnimation(float fromDegrees,float toDegrees,int pivotXType, float pivotXValue,int pivotYType,float pivotYValue);//可以在XY平面上旋转中的view canvas.

		setDuration(long durationMillis)//动画时间
		startAnimation(Animation animation)//开始播放动画


##Frame动画（逐帧动画）
>传统的基于单元格的动画，每一帧显示一个不同的drawable.它可以在一个view中显示，并使用它的Canvas作为投影屏幕。它只需要创建一个`AnimationDrawable`对象来表示Frame动画，然后通过`addFrame`方法把每一帧要显示的内容添加进去，最后通过`start`方法就可以播放这个动画了。同时还可以通过`setOneShot`方法来设置动画是否重复播放。


##差值属性动画
>属性动画系统几乎可以让应用程序的任何对象动起来。它是一个框架，在一定时间内，通过使用指定的内插技术来影响任意的对象属性。虽然补间view动画改变了它所作用的View的外观，但并没有改变对象本身，而属性动画却直接改变了它所作用的对象的属性。

>通过使用一个属性对象生成器，在一个给定时间内使用你选择的差值算法将该属性从一个值转换为另一个值，还可以设置动画的重复行为。这个值可以是任意对象，从常规整型到复杂的类对象实例都可以。  它可以用在任意事物上，从简单的view效果，如移动，缩放，view的淡入淡出，到复杂的动画，如运行时的布局变化，曲线变换等

###创建属性动画：
>创建属性动画最简单的方法是使用`ObjectAnimator`类。这个类包含有`ofFloat`,`ofInt`和`ofObject`静态方法，可以很容易地将目标对象的特定属性在指定的值之间进行转换：

	String propertyName ="alpha";
	float from = 1f;
	float to = 0f;
	ObjectAnimator anim = ObjectAnimator.ofFloat(targetObject,propertyName,from,to);
>另外还可以只提供一个值，从而让属性从当前的值转换到最终的值：

	ObjectAnimator anim = ObjectAnimator.ofFloat(targetObject,proerptyName,to);
*想要让一个给定的属性动起来，拥有该属性的对象必须包含`getter/setter`方法* 。

>要想作用于一个非整形和非浮点型的类型的属性，可以使用`ofObject`方法，这个方法要求提供一个`TypeEvaluator`类的实现，实现`evaluate`方法以返回一个对象，该对象是当动画为开始对象和结束对象之间动画动作指定部分时应该返回的对象。

	TypeEvaluator<MyClass> evaluator = new TypeEvaluator<MyClass>{
	    public MyClass evaluator(float fraction,MyClass startValue,MyClass endValue){
	        MyClass result = new MyClass();
	        //修改新的对象，使之代表开始值和结束值之间的给定部分
	        return result;
	    }
	}

	//两个实例之间的动画
	ValueAnimator oa = ObjectAnimator.ofObject(evaluator,myClassFromInstance,myClassToInstance);
	oa.setTarget(myClassInstance);
	os.start();
默认情况下，每个动画只运行300ms,并且只运行一次使用`setDuration(500)`和`setRepeatMode()`和`setRepeatCount()`可以改变

>要想作用于XML动画生成器资源中特定的对象，可以使用`AnimatorInflator.loadAnimator`方法，传入当前上下文和动画资源ID,从而获得一个ObjectAnimator的副本，然后使用setTarget将它应用到一个对象上。

	Animator anim = AnimatorInflatore.laodAnimator(context,resID);
	anim.setTarget(targetObject);
###定制插值器
>默认情况下，在每个动画开始和结束间转换所使用的插值器是一个非线性的插值器`AccelerateDecelerateInterpolator`,它提供了在转换开始时加速且在快到结束时减速的效果。

可以使用`setInterpolator`方法应用以下SDK提供的插值器中的一种。

- AccelerateDecelerateInterpolator  开始和结束时速度变化较慢，在中间时速度另速
- AcceleratInterpolator  开始时速度变化较慢，在中间时候加速
- AnticipateInterpolartor  开始的时候向后，然后再向前急冲
- AnticipateOversshootInterpolator 开的时候向后，然后再向前冲一定的值，最后回到最终的值
- BouceInterpolator  动画结束时弹起
- DecelerateInterpoloator 开始时速度变化很快，然后减速
- LinearInterpolator  速度变化是一个常量
- OvershootInterpolator 开始向前急冲，超过最终的值，然后再回来
	
		anim.setInterpolator(new AnticipateOvershootInterpolator());

###创建属性动画集
可以使用AnimatorSet类

	AnimatorSet set = new AnimatorSet();

想要向一个动画集中添加一个新的动画，可以使用`play`方法，这个方法返回一个`AnimatorSet.Builder`对象，通过它可以指定相对于其他动画何时播放指定的动画

	set.play(firstAnimation).before(concurrentAnim1);
	set.play(concurrentAnim1).with(concurrentAnim2);
	set.play(lastAnim).after(concurrentAnim2);
使用start()执行动画  set.start();


Android提供了几种动画类型：View Animation 、Drawable Animation 、Property Animation 。View Animation相当简单，不过只能支持简单的缩放、平移、旋转、透明度基本的动画，且有一定的局限性。比如：你希望View有一个颜色的切换动画；你希望可以使用3D旋转动画；你希望当动画停止时，View的位置就是当前的位置；这些View Animation都无法做到。这就是Property Animation产生的原因

###布局动画（Layout Animations）
>主要使用LayoutTransition为布局的容器设置动画，当容器中的视图层次发生变化时存在过渡的动画效果。
>过渡的类型一共有四种：
>
1. LayoutTransition.APPEARING 当一个View在ViewGroup中出现时，对此View设置的动画
2. LayoutTransition.CHANGE_APPEARING 当一个View在ViewGroup中出现时，对此View对其他View位置造成影响，对其他View设置的动画
3. LayoutTransition.DISAPPEARING  当一个View在ViewGroup中消失时，对此View设置的动画
4. LayoutTransition.CHANGE_DISAPPEARING 当一个View在ViewGroup中消失时，对此View对其他View位置造成影响，对其他View设置的动画
5. LayoutTransition.CHANGE 不是由于View出现或消失造成对其他View位置造成影响，然后对其他View设置的动画。
注意动画到底设置在谁身上，此View还是其他View。

