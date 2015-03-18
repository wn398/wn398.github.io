title: Context Provider-内容提供器
date: 2015/3/12 12:43:24 
tags:
- 技术
- android

categories: android
---
## 内容提供器Context Provider
>它主要用于在不同应用程序之间实现数据共享的功能，它提供了一套完整的机制，允许一个程序访问另一个程序中的数据，同时还能保证被访问数据的安全性。目前，使用内容提供器是Android实现跨程序共享数据的标准方式。
>
>内容提供器的用法一般有两种，一种是使用现有有内容提供器来读取和操作相应程序中的数据，另一种是创建自己的内容提供器给我们和程序的数据提供外部访问接口。
>
>利用内容提供器访问其他程序中的数据,当一个应用程序通过内容提供器对其数据提供了外部访问接口，任何其他的应用程序都可以对这部分数据进行访问。android系统自带的电话本，短信，媒体库等程序都提供了类似的访问接口。

##ContentResovler用法
>对于一个应用程序来说，如果想要访问内容提供器中共享的数据，就一定要借助ContentResolver类

1. 可以通过Context中的getContentResolver()方法获取该类的实例。
2. ContentResolver类提供了一系列的方法用于对数据进行CRUD操作。insert添加，update()更新，delete()删除，query()查询数据
3. 不同于SOLiteDatabase,ContentResolver中的增、删、改、查方法都是不接收表名参数的，而是使用一个Uri参数代替，这个参数被称为内容URI.内容URI给内容提供器中的数据建立了唯一标识符
	>它主要由两部分组成，权限（authority）和路径（path）.权限用于对不同的应用程序做区分的，一般为避免冲突，都会采用程序包名的方式来命名。如程序的包名为：com.example.app,则对应的权限可以命名为com.example.app.provider. 路径则是用于对同一应用程序中不同的表做区分的，通常都会添加到权限的后面。如某个程序数据库里存在两张表，tabel1和tabel2,这时就可以将路径分别命名为/tabel1和/tabel2，然后把权限和路径进行组合，然后在字符串的头部加上协议声明：	`content://com.example.app.provider/tabel1.`
	`context://com.example.app.provider/tabel2.`
	>这样内容URI就可以非常清楚地表达出想要访问哪个程序中哪张表里的数据。也因此，ContentResolver中的增删改查方法才都接收Uri对象作为参数，因为使用表名的话系统将无法得知我们期望访问的是哪个应用程序里的表。
4. **url解析** 在得到了内容URI字符串之后，还需要将它解析成Uri对象才可以作为参数传入。
`Uri uri = Uri.parse("content://com.example.app.provider/tabel1")`

	只需要调用Uri.parse()方法，就可以将内容URI字符串解析成Uri对象了,使用这个Uri对象查询tabel1中的数据
	`Cursor cursor = getContentResolver().query(
	uri, projection, selection, selectionArgs, sortOrder);`
	这些参数和SQLiteDatabase中query()方法里的参数很像，但要简单些

5. 剩下的增加，修改，删除操作类似于数据库的

##创建自己的内容提供器
###继承ContentProvider
1. 新建一个类继承ContentProvider的方式来创建一个自己的内容提供器。
2. ContentProvider类中有六个抽象方法，将这6个方法全部重写。

>1. onCreate()
	>初始化内容提供器时调用。通常会在这里完成对数据库的创建和升级操作，反回true表示内容提供器初始化成功，返回false失败。注意，只有当存在ContentResolver尝试访问我们程序中的数据时，内容提供器才会被初始化。
>2. query()
	>从内容提供器查询数据，使用uri参数来确定查询哪张表，projection参数用于确定查询哪些列，selection和selectionArgs参数用于约束查询哪些行，sortOrder参数用于对结果进行排序，结果放在Cursor对象中返回
>3. insert()
	>向内容提供器添加一条数据，使用uri参数来确定要添加到的表，待添加的数据保存在values参数中。添加完成后，返回一个用于表示这条新记录的URI
>4. update()
	>更新内容提供器中已有的数据，使用uri参数来确定要更新哪一张表中的数据，新数据保存在valuse参数中。selection和selectionArgs参数用于约束更新哪些行，受影响的行数作为返回值返回
>5. delete()
	>使用uri参数来确定删除哪一张表中的数据，selection和selectionArgs参数用于约束删除哪些行，被删除的行数将作为返回值返回
>6. getType() 
	>根据传入的内容URI来返回相应的MIME类型。

###内容URI格式
内容URI的格式有两种，一是以路径结尾期望访问表中所有数据，另一是以id结尾期望访问表中拥有相应id的数据。

格式1：`content://com.example.app.provider/tabel1`

>表示调用方期望访问的是com.example.app这个应用的tabel1表中的数据。

格式2：`content://com.example.app.provider/tabel1/1`

>表示调用方期望访问的是com.example.app这个应用的tabel1表中id为1的数据。

我们可以使用通配符的方式来分别匹配这两种格式的内容URI,规则如下：

>1. *：表示匹配任意长度的任意字符
>2. \#：表示匹配任意长度的数字
>
>所以，一个能够匹配任意表的内容URI格式如下：
`content://com.example.app.provider/*`
而一个能够匹配tabel1表中任意一行数据的内容URI格式可以写成如下：
`content://com.example.app.provider/tabel1/#`

###使用UriMatcher
>接着，再借助UriMatcher这个类可以轻松实现匹配内容URI的功能。
>
>UriMatcher中提供了一个addURI()方法，这个方法接收三个参数，可以分别把权限，路径和一个自定义的代码传进去，当调用UriMatcher的match()方法时，就可以将一个Uri对象传入，返回值是某个能够匹配这个Uri对象所对应的自定义代码，得到这个代码，我们就可以判断出调用方期望访问的是哪张表中的数据了。

实例代码：

	public class MyProvider extends ContentProvider{
	public static fianl int TABLE1_DIR = 0;
	public static fianl int TABLE1_ITEM = 1;
	public static final int TABLE2_DIR＝２；
	public static final int TABLE2_ITEM = 3;

	private static UriMatcher uriMatcher;
	static{
	    uriMatcher = new UriMathcer(UriMatcher.NO_MATCH);
	    uriMatcher.addURI("com.example.app.provider","table1",TABLE1_DIR);
	    uriMatcher.addURI("com.example.app.provider","table1/#",TABLE1_ITEM)
	    uriMatcher.addURI("com.example.app.provider","table2",TABLE2_DIR)
	    uriMatcher.addURI("com.example.app.provider","table2/#",TABLE2_ITEM)
	}

	@overrider
	public Cursor query(Uri uri,String[] projection,String selceion,String[] selectionArgs,String sortOrder){
		switch(uriMathcer.match(uri)){
			case TABEL1_DIR:
		        //查询table1表中所有数据
		    break;
			case TABEL1_ITEM:
		        //查询table表中单条数据
		    break;
			case TABEL2_DIR:
		        //查询table2表中所有数据
		    break;
			case TABEL2_ITEM:
		        //查询table2表中间条数据
		    break;
			default:
		         break;
	    }
	}
###getType()方法：
>它是所有的内容提供器都必须提供的一个方法，用于获取Uri对象所对应的MIME类型。

**MIME类型:**
>一个内容URI所对应的MIME字符串主要由三部分组成，Android对这三个部分做了如下格式规定
>
  1. 必须以vnd开头
  2. 如果内容URI以路径结尾，则后接android.cursor.dir/，如果内容URI以id结尾，则后接android.cursor.item/
  3. 最后接上vnd.<authority>.<path>

`content://com.example.app.provider/table1`这个内容目录URI，它所对应的MIME类型就可以写成：
`vnd.android.cursor.dir/vnd.com.example.app.provider.table1`

`content://com.example.app.provider/table1/1`这个内容条目URI，它所对应的MIME类型就可能写成：
`vnd.android.cursor.item/vnd.com.example.app.provider.table1`

现在可以完善Myprovider中的getType()方法中的逻辑如下：

	public class Myprovider extends ContentProvider{
	...
	@Override
	public String getType(Uri uri){
	    switch(uriMatcher.match(uri)){
		    case TABLE1_DIR:
		        return "vnd.android.cursor.dir/vnd.com.example.app.provider.table1";
		    case TABLE1_ITEM:
		    	return "vnd.android.cursor.item/vnd.com.example.app.provider.table1";
		    case TABLE2_DIR:
		    	return "vnd.android.cursor.dir/vnd.com.example.app.provider.table2";
		    case TABLE2_ITEM:
		    	return "vnd.android.cursor.item/vnd.com.example.app.provider.table2";
		    default:
		    	break;
		}
		return null;
			}
		}
	}

###注册内容提供器：
要实现跨程序数据共享还需要将内容提供器在AndroidManifest.xml文件中注册才可以，如下：
	
	<application>
	    <provider
	        android:name = "com.example.MyDataBaseProvider"
	        android:authoritier="com.example.provider"/>
	</application>

##利用系统的内容提供器访问
`content://media/internal/images`(这个URI将返回设备上存储的所有图片)

`content://contacts/people/5`(联系人信息中ID为5的联系人记录)

>为了使得大家更容易理解，在android.provider包下提供了一系列的辅助类，其中包含了很多以类变量形式给出的查询字符串，因此我们可以将上面的第2个URI改写成以下方式：
`Uri person = ContentUris.withAppendedId(People.CONTENT_URI,5)`
所有与其Content Providers互动的过程中都可以使用这个URI常量。每个ContentResolver都把URI作为其第一个参数，它决定了ContentResolver将与哪一个Provider对话。
