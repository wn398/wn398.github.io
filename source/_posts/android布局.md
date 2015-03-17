title: android布局
date: 2015/3/17 13:00:19 
tags:
- 技术
- android_UI
categories: android
---
##LayoutInflater
>获取LayoutInflater的两种方法：
>
1. LayoutInflater layoutInflater = LayoutInflater.from(context);
2. LayoutInflater layoutInflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);  

>layout-width和layout-height都是相对于父布局而言的，如果没有父布局，他们将失去作用。
>
>在setContentView()方法中，android会自动在布局文件的最外层再嵌套一个FrameLayout,而对于动态在代码中加载的要特别注意这一点。

每一个视图的绘制过程都必须经历三个最主要的阶段：`onMeasure()`,`onLayout()`和`onDraw()`

##安卓布局优化原则：
>1. 尽量多使用`RelativeLayout`和`LinearLayout`, 不要使用绝对布局`AbsoluteLayout`，在布局层次一样的情况下， 建议使用`LinearLayout`代替`RelativeLayout`,`LinearLayout`性能要稍高一点，但往往`RelativeLayout`可以简单实现`LinearLayout`嵌套才能实现的布局。
>
>2. 将可复用的组件抽取出来并通过include标签使用；
3. 使用ViewStub标签来加载一些不常用的布局；viewstub标签同include标签一样可以用来引入一个外部布局，不同的是，viewstub引入的布局默认不会扩张，即既不会占用显示也不会占用位置，从而在解析layout时节省cpu和内存。 viewstub常用来引入那些默认不会显示，只在特殊情况下显示的布局，如进度布局、网络失败显示的刷新布局、信息出错出现的提示布局等。
4. 使用merge标签减少布局的嵌套层次；
>>merge标签的作用是合并UI布局，使用该标签能降低UI布局的嵌套层次。
>>
>merge标签可用于两种典型情况：
>>
* 布局根结点是FrameLayout且不需要设置background或padding等属性，可以用merge代替，因为Activity内容布局的parent view就是个FrameLayout，所以可以用merge消除只剩一个。
* 某布局作为子布局被其他布局include时，使用merge当作该布局的顶节点，这样在被引入时顶结点会自动被忽略，而将其子节点全部合并到主布局中。如：


	<mergexmlns:android="http://schemas.android.com/apk/res/android"
		android:layout_width="match_parent"
		android:layout_height="match_parent">
		<FrameLayoutandroid:layout_width="match_parent"android:layout_height="match_parent">
	   			<TextViewandroid:layout_width="match_parent"android:layout_height="wrap_content"android:text="hello world"/>
		<RelativeLayoutandroid:layout_width="match_parent"android:layout_height="match_parent"android:layout_gravity="center">
		<includelayout="@layout/header"/>
	</RelativeLayout>
	</FrameLayout>
	</merge>

