title: android编程一些技巧
date: 2015/3/12 14:55:44 
tags:
- 技术
- android

categories: android
---

##全局获取Context
>在Activity里获取Context容易，一旦脱离了activity你会发现很难获取到context。而许多方法的参数里需要context.

###1. 使用自己的Application类   
>Android有一个Application类，每当应用程序启动的时候，系统就会自动将这个类进行初始化。需要实现一个自己的Application类，用于管理程序内一些全局的状态信息，如全局Context。
首先创建一个MyApplication类继承自Application，代码如下所示：

		public class MyApplication extends Application {
			private static Context context;
			@Override
			public void onCreate() {
				context = getApplicationContext();
			}
			public static Context getContext() {
				return context;
			}
		}
此代码中重写了父类的onCreate()方法，并通过调用getApplicationContext()方法得到了一个应用程序级别的Context，然后又提供了一个静态的getContext()方法，在这里将刚才获取到的Context进行返回。

接下来我们需要告知系统，当程序启动的时候应该初始化MyApplication类，而不是默认的Application类。只要在`AndroidManifest.xml`文件的`<application>`标签下进行指定就可以了，代码如下所示：
 
		<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
			package="com.example.networktest" 
			android:versionCode="1" 
			android:versionName="1.0" > 
		…… 
			<application 
			android:name="com.example.networktest.MyApplication"> 
		…… 
		</application> 
		</manifest> 
注意 在指定MyApplication的时候一定要加上**完整的包名**，不然系统将无法找到这个类。 
这样就实现了一种全局获取Context的机制，之后不管你想在项目的任何地方使用Context，只需要调用一下`MyApplication.getContext()`就可以了。

##使用Intent传递对象
>Intent我们可以借助它**启动活动，发送广播，启动服务**等，同样还可以**使用Intent传递对象**

使用Intent传递对象有两种方式 Serializable和Parcelable:
###Serializable方式
>Serializable是序列化，表示将一个对象转换成可存储或可传输的状态。序列化后的对象可以在网络上进行传输，也可以存储到本地。至于序列化的方法也很简单，只需要让这个类去实现Serializable这个接口就可以了。

	Intent intent = new Intent(FirstActivity.this, SecondActivity.class); 
	intent.putExtra("person_data", person);
	startActivity(intent);

接下来在SecondActivity中获取这个对象也很简单，写法如下：

	Person person = (Person) getIntent().getSerializableExtra("person_data");

###Parcelable方式：
>使用Parcelable也可以实现相同的效果，不过不同于将对象进行序列化，Parcelable方式的实现原理是将一个完整的对象进行分解，而分解后的每一部分都是Intent所支持的数据类型，这样也就实现传递对象的功能了。
下面我们来看一下Parcelable的实现方式，修改Person中的代码，如下所示：

	public class Person implements Parcelable {
		private String name;
		private int age;
		……
		@Override
		public int describeContents() {
			return 0;
		}

		@Override
		public void writeToParcel(Parcel dest, int flags) {
			dest.writeString(name); // 写出name
			dest.writeInt(age); // 写出age
		}

		public static final Parcelable.Creator<Person> CREATOR = new Parcelable.Creator<Person>() {
			@Override
			public Person createFromParcel(Parcel source) {
				Person person = new Person();
				person.name = source.readString(); // 读取name
				person.age = source.readInt(); // 读取age
				return person;
			}

			@Override
			public Person[] newArray(int size) {
				return new Person[size];
			}
		};
	}
 从上面的代码中可以看到，首先我们让Person类去实现了`Parcelable接口`，这样就必须重写`describeContents()`和`writeToParcel()`这两个方法。

其中`describeContents()`方法直接返回0就可以了，而`writeToParcel()`方法中我们需要调用Parcel的`writeXxx()`方法将Person类中的字段一一写出。注意字符串型数据就调用`writeString()`方法，整型数据就调用`writeInt()`方法，以此类推。

除此之外，我们还必须在Person类中提供一个名为`CREATOR`的常量，这里创建了`Parcelable.Creator`接口的一个实现，并将泛型指定为`Person`。接着需要重写`createFromParcel()`和`newArray()`这两个方法，在`createFromParcel()`方法中我们要去读取刚才写出的name和age字段，并创建一个Person对象进行返回，其中name和age都是调用Parcel的readXxx()方法读取到的，**注意这里读取的顺序一定要和刚才写出的顺序完全相同**。而`newArray()`方法，只需要new出一个Person数组，并使用方法中传入的size作为数组大小就可以了。

接下来在`FirstActivity`中我们仍然可以使用相同的代码来传递Person对象，只不过在`SecondActivity`中获取对象的时候需要稍加改动，如下所示：

	Person person = (Person) getIntent().getParcelableExtra("person_data");

对比一下，Serializable的方式较为简单，但由于会把整个对象进行序列化，因此效率方面会比Parcelable方式低一些，所以在通常情况下还是更加推荐使用Parcelable的方式来实现Intent传递对象的功能。

##使用Linkify给文本加超链接
>Linkify 是一个辅助类，它会自动地在TextView类（或者TextView的派生类）中通过`RegEx`模式匹配来创建超链接。那些匹配的文本将变成可单击的超链接，这些超链接可以隐式地使用匹配的文本作为目标URI来触发
	
	startActivity(new Intent(Intent.ACTION_VIEW,uri));
可以在代码中使用 

	 Linkify.addLinks(textView,Linkify.WEB_URIS|Linkify.EMAIL_ADDRESS);
或在配置中加上属性
	
	<TextView
    	android:antoLink="phone|email"

如果没设数据参数，只要Intent中的Action和category都出现在Intent-Filter中（Intent-Filter中可能有多个action和多个category），就能与之匹配，否则匹配失败


##使用WakeLock
>为了延长电池的使用寿命，Android设备会在闲置一段时间后使屏幕变暗，然后关闭屏幕提示，最后停止CPU.WakeLock是一个电源管理系统服务功能，应用程序可以使用它来控制主机设备的电源状态。 因为Wake lock会显著影响电池寿命，所以在创建它们之前，应用程序需要请求权限：`android.premission.WAKE_LOCK`


为创建一个Wake Lock，需要调用Power Manager的newWakeLock方法，并指定下面的一种wake Lock类型：

- FULL_WAKE_LOCK            保持屏幕最大亮度，键盘背光灯点亮以及CPU运行
- SCREEN_BRIGHT_WAKE_LOCK   保持屏幕最大亮度和CPU运行
- SCREEN_DIM_WAKE_LOCK      保持屏幕亮起（但是变暗）和CPU运行
- PARTIAL_WAKE_LOCK         保持CPU运行

//使用一个WakeLock
	
	WakeLock wakeLock;

	private class MyAsyncTask extends AsyncTask<Void,Void,Void>{
	    protected void doInBackground(void ..parameters){
	        PowerManager pm = (PowerManager)getSystemService(Context.POWER_SERVICE);
	        wakeLock = pm.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK,"MyWakeLock");
	        wakeLock.acquire();
	        //TODO  things in the background
	        return null;
	
	    }
	    
	    protected void onPostExecute(void parameters){
	        wakeLock.release();
	    }
	
	}
