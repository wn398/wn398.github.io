title: android_UI的一些技巧
date: 2015/3/17 22:47:09  
tags:
- 技术
- android_UI
categories: android
---
##隐藏标题栏
调用`requestWindowFeature(Window.FEATURE_NO_TITLE)`要放在`setContextView()`之前
##将程序挂起
按Home键只是把程序暂时挂起，并没有退出
##浮动标题栏
`getWindow().requestFeature(Window.FEATURE_ACTION_BAR_OVERLAY);`这种模式，操作栏会浮动在Activity之上，有可能掩盖布局顶部内容
##拆分操作栏
在activity中添加：`android:uiOptions="splitActionBarWhenNarrow"`
##配置操作栏图标的导航行为
`actionBar.setHomeButtonEnabled(true);`
##启用应用程序图标的向上导航功能

	   actionBar.setDisplayUseLogoEnabled(false);
	   actionBar.setDisplayHomeAsUpEnabled(true);

##启用操作栏导航tab键
		actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_TABS);
		actionBar.setDisplayShowTitleEnabled(false)  
通过ActionBar的addTab方法添加tab
通过setTabListener添加对tab事件的监听以更新UI

##启用操作栏的下拉列表
`actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_LIST);`它的实现非常像spinner,可以通过创建一个新的实现`SpinnerAdapter`或`arrayAdapter`的接口适配器来填充下拉列表。调用操作栏的`setListNavigionCallbacks`,并传入适配器和一个`OnNavigationListener`
导航tab和下拉列表只能二者选其一，使用自定义view: setCustomView();

##把菜单项设置为操作栏操作：
    menuItem.setShowAsAction(MenuItem.SHOW_AS_ACTION_IF_ROOM | MenuItem.SHOW_AS_ACTION_WITH_TEXT)

##动态更新菜单项
可以重写Activity的`onPrepareOptionMenu()`	方法。每当菜单按键被单击，显示溢出菜单，或者创建操作栏的时候就会触发`onPrepareOnptionMenu`方法。要动态修改菜单，可以在此方法中记录创建菜单时产生的引用，也可以使用Menu对象的`findItem`方法

##主题
在activity中`android:theme`属性，用于给当前活动指定主题。`@android:style/Theme.Dialog`是使用对话框式的主题
##ListView布局
使用系统自带的adapter布局：
用`ArrayAdapter`可以实现简单的`ListView`的数据绑定。默认情况下，`ArrayAdapter`绑定每个对象的`toString`值到`layout`中预先定义的`TextView`控件上

1. `android.R.layout.simple_list_item_checked`这个资源，实现带选择框的`ListView`。需要用`setChoiceMode()`方法设定选择为多选还是单选，否则将不能实现选择效果
2. 通过指定`android.R.layout.simple_list_item_multiple_choice`这个资源实现带CheckBox的ListView。同样的，需要用`setChoiceMode()`方法来设置单选或者多选
3. 通过指定`android.R.layout.simple_list_item_single_choice`这个资源实现带`RadioButton`的ListView。这里要注意的是，这里并不是指定了单选。是多选还是单选要通过setChoiceMode()方法来指定

使用simpleAdapter的数据一般都是用`HashMap`构成的列表，列表的每一节对应ListView的每一行。通过SimpleAdapter的构造函数，将HashMap的每个键的数据映射到布局文件中对应控件上。这个布局文件一般根据自己的需要来自己定义。
ArrayAdapter和simpleAdatper的UI里的UI事件不能被响应，所以要用BaseAdapter
##屏幕设置 
	  /* 设置为全屏模式 */
	  getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);
 
	  /* 设置为横屏 */
	  setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE); 

##获得屏幕属性
Android中的DisplayMetrics定义了屏幕的一些属性，可以通过getMetrics方法得到当前屏幕的宽和高。

	/* 定义DisplayMetrics对象 */
	  DisplayMetrics dm = new DisplayMetrics();
	 
	  /* 取得窗口属性 */
	  getWindowManager().getDefaultDisplay().getMetrics(dm);
	 
	  /* 窗口的宽度 */
	  int screenWidth = dm.widthPixels;
	 
	  /* 窗口的高度 */
	  int screenHeight = dm.heightPixels;

##一些特定布局文件
	res/layout-long-land/ 为宽屏横屏模式提供布局
	res/layout-notlong-port/ 为非宽屏竖屏模式提供布局
	
	res/layout-w600dp
	res/layout-h720dp
	res/layout-sw320dp

##控件的可见性：
android中所有控件都有可见性，可通过android:visibility指定。

有三种模式`visible`,`invisible`和`gone`. 

visible是默认的。

invisible表示控件不可见，但它仍然占据着原来的位置和大小，可以理解成控件变成透明状态了。

gone则表示控件不仅不可见，而且不再占用任何屏幕空间。

##构建Dialog:

	AlerDialog.Builder dialog = new AlertDialog.Builder(context);
ProgressDialog会在对话框中显示一个进度条：

	PrograssDialog prd = new ProgressDialog(context);
注意如果在`setCancleable()`中传入false,表示`progressDialog`是不能通过Back键取消的，这时要在数据加载完成后调用 ProgressDialog的`dismiss()`方法来关闭对话框，否则它会一直存在。

##布局
LinearLayout中如果指定水平排列模式，则`android:layout_gravity`只能指定上下对齐方式才有效，同理如果LinearLayout如果指定为垂直排列模式，则`layout_gravity`只能指定水平对齐模式

layout_weight与wrap_content对应用界面对屏幕的适配性非常好

FrameLayout没有任何的定位方式，所有的控件都会摆放在布局的左上角。

在TableLayout中每加入一个TableRow就表示在表格中添加了一行，然后在TableRow中每加入一个控件，就表示在该行中加入了一列。TableRow中的控件是不能指定宽度的。
合并单元格可以通过`android:layout_span="xx"`来表示xx为要合并的个数。
在TableLayout中可以通过`android:stretchColumns=“x”`来对表格列进行拉伸，指定为0则拉伸第1列，指定为1则拉伸第2列

在安卓布局中，可以通过include layout="XXX"来引入其它布局

自定义控件：`LayoutInflater.from(context).inflate("布局文件"，this)`
在布局文件中添加自定义控件和添加普通控件的方式基本是一样的，只不过在添加自定义控件时候，我们需要指明控件的完整类名，包名在这里不可以省略。

ListView控件使用，实现自己的adapter时getView（）方法有一个convertView参数，这个参数用于将之前加载好的布局进行缓存，以便之后进行重用。

##单位和尺寸
pt是磅数，1磅=1/72英寸，一般pt会作为字体单位使用

dp是密度无关像素，也被称作dip和px相比，它在不同密度的屏幕中的显示比例将保持一致。
sp是可伸缩像素，它采用了和dp同样的设计理念，解决了文字大小甜酸问题
Android中的密度就是屏幕每英寸所包含的像素数，通常以dpi为单位。如160dpi
我们可以通过代码获得当前屏幕的密度值：

	float xdpi = getResources().getDisplayMetrics().xdpi;
	float ydpi = getResources().getDisplayMetrics().ydpi;

根据Android的规定，在160dpi的屏幕上，1dp=1px,而在320dpi的屏幕上，1dp = 2px,因此会用 dp来指定控件的宽和高，就可以保证控件在不同密度的屏幕中的显示比例保持一致。
sp的原理和dp是一样的，它主要是用于指定文字的大小。

in 英寸，屏幕的物理尺寸，每英寸==2.54厘米

dpi, Dots Per Inch 缩写，每英寸包含像素个数，如320 X 480分辨率的手机，宽2英寸，高3英寸，每英寸包含的像素点的数量为320/2=160dpi（横向）或480/3=160dpi（纵向），160就是这部手机的dpi，横向和纵向的这个值都是相同的，原因是大部分手机屏幕使用正方形的像素点。

density :屏幕密度，density和dpi的关系为 density = dpi/160

dp: 也即 dip,设置独立像素，device independent pixels缩写，Android特有的单位，在屏幕密度dpi = 160屏幕上，1dp = 1px

drawable-ldpi (dpi=120, density=0.75)
drawable-mdpi (dpi=160, density=1)
drawable-hdpi (dpi=240, density=1.5)
drawable-xhdpi (dpi=320, density=2)
drawable-xxhdpi (dpi=480, density=3)
当你需要在不同大小的控件中使用的图片时，最好的方法就是用nine-patch图片。它是一和种被特殊处理过的图片，你可以指定哪些区域可以拉伸而哪些区域不可以。为了将图片转换成nine-patch图片，你可以从一张普通的图片开始，然后通过SDK中带有的draw9patch工具打开这张图片(工具位置在SDK的tools目录下)，你可以在图片的左边框和上边框绘制来标记哪些区域可以被拉伸。你也可以在图片的右边框和下边框绘制来标记内容需要放置在哪个区域。

总结：在编写Android程序的时候，尽量将控件或布局的大小指定成match_parent或wrap_content，如果必须要指定一个固定值，则使用dp来作为单位，指定文字大小时候，使用sp作为单位。

##碎片：
动态添加fragment:

1. 创建待添加的碎片实例
2. 获取到FragmentManger ,在活动中可以直接getFragmentManager()
3. 开启一个事务，通过调用beginTransaction()方法开启
4. 向容器内加入碎片，一般使用replace()方法实现，需要传入容器的id和待添加碎片实例
5. 提交事务，调用commit()方法来完成

	FragmentManager manager = getFragmentManager();
	FragmentTransaction transaction = manager.beginTransaction();
	transaction.replace(要替换的fragment id,fragment);
	transaction.commit();

用碎片可以模拟返回栈，在FragmentTransaction中提供了一个`addToBackStack()`方法，可以用于将一个事务添加到返回栈中。

为了方便碎片和活动之间通信，FragmentManager提供了一个类似于`findViewById()`的方法，`findFragmentById(id)`这样就可以调用碎片中的方法了。
 在碎片中调用活动里的方法用`getActivity()`,然后再去调用方法。
碎片与碎片通信，则是通过活动的中间桥梁来实现。

###碎片的状态和回调：
1. 运行状态：当一个碎片是可见的，并且它所关联的活动正处于运行状态，该碎片也处于运行状态
2. 暂停状态：当一个活动进入暂停状态时（由于另一个未占满屏幕的活动被添加到了栈顶），与它相关联的可见碎片就会进入到暂停状态。
3. 停止状态:当一个活动进入停止状态，与它相关联的碎片就会进入到停止状态。或者通过调用FragmentTransaction的remvoe(),replace()方法将碎片从活动中移除，但有在事务提交之前调用addToBackStack()方法，这时的碎片也会进入到停止状态。进入停止状态的碎片对用户是完全不可见的，有可能被系统回收
4. 销毁状态:碎片总是依附于活动而存在的，当活动被销毁时，与它相关的碎片就会进入到销毁状态。或者通过调用FragmentTransaction的remove()，replace()方法将碎片从活动中移除，但在事务提交之前并没有调用addToBackStack()方法，这时的碎片也会进入到销毁状态。

碎片除了有活动中的回调方法外，还有一些附加的方法：

1. onAttach()  当碎片和活动建立关联时调用
2. onCreateView() 为碎片创建视图（加载布局）时调用
3. onActivityCreated() 确保与碎片相关联的活动一定已经创建完毕时调用
4. onDestroyView() 当与碎片关联的视图被移除时调用
5  onDetach() 当碎片和活动解除关联的时候调用。

##动态加载布局的技巧：
使用限定符（Qualifiers）layout文件夹与layout-large文件夹

最小宽度限定符允许我们对屏幕的宽度指定一个最小值（以dp为单位），然后以这个最小值为临界点，屏幕宽度大于这个值的设备加载这个布局，屏幕小于这个值的设备就加载另一个默认布局。如在res目录下建layout-sw600dp文件夹

##控件引用
1. android:id="@+id/btn"  
表示在R.java文件里面新增一个id为btn的控件索引，最常用的一种声明控件id的方式。
2. android:id="@android:id/tabhost"
表示引用的是系统已有的ID，在对应的sdk目录下的ids.xml里面。一般外部不去调用，是组件内部调用的时候使用。
3. android:id="@id/btn"  
表示引用一个已经存在的ID
