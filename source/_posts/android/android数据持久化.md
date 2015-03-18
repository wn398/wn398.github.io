title: android数据持久化
date: 2015/3/13 20:42:50 
tags:
- 技术
- android
- 数据持久化
categories: android
---
>安卓数据持久化三种类型：*文件存储，sharedPreference存储和数据库存储*

##文件存储
###1.将数据存储在文件中
>Context类中提供了一个`openFileOutput()`方法，可以用于将数据存储到指定的文件中。它接收两个参数，第一个文件名，文件名不包括路径，默认存储到`/data/data/<packagename>/files`目录下，第二个参数文件的操作模式。`MODE_PRIVATE`(覆盖原文件内容)。`MODE_APPEND`(往文件中追加内容)。它返回FileOutputStream对象

###2.从文件中读取数据
>Context类的`openFileInput()`方法，它只接收一个参数文件名

获取SD卡根目录：调用`Environment`的`getExternalStorageDirectory()`方法获取到的是手机SD卡的根目录。

##SharedPreference

三种获取SharedPreference的方法：

1. Context类中的`getSharedPreference()`方法。它接收两个参数，第一个指定SharedPreference文件名称，不存在自动创建。文件都存在`/data/data/<packagename>/shared_prefs/`目录下。第二个指定操作模式，`MODE_PRIVATE`(默认操作模式，和直接传入0效果相同，表示只有当前应用程序才可以对这个文件进行读写)和`MODE_MULTI_PROCESS`（则一般用于会有多个进程中对同一个文件进行读写）

2. Activity类中的`getPreference()`方法。这个方法类似Context中的`getSharedPreference()`方法，但它只接收一个操作模式参数。使用这个方法会自动将当前活动的类名作为`SharedPreference的`文件名。

3. PreferenceManger类的`getDefaultSharedPreference()`方法。这是个静态方法，接收一个Context参数，并自动使用当前应用程序的包名作为前缀来命名SharedPreference文件。

###存入数据到SharedPreference：

1. 调用SharedPreference对象的edit()方法获取一个SharedPrefereces.Editor对象
2. 向SharedPreferences.Editor对象中添加数据，如putBoolean,putString()
3. 使用commit()或apply()将数据提交。完成数据存储

###从SharedPreferences中读取数据：
它提供了一系列的get方法对存储数据进行提取，每种get方法对应了Editor中的一种put方法。如getBoolean(),getString()这些方法接收两个参数，第一个参数是key,第二个参数提默认值，表示当找不到传入的key对应的value时返回些值。
如果不需要与其他模块共享数据，可以使用Activity.getPerences()方法保持数据私有。需要强调的是，我们无法直接在多个程序间共享Preferences数据。

##SQLite数据库

###SQLiteOpenHelper

>SQLiteOpenHelper是一个抽象类，有两个抽象方法`onCreate()`和`onUpgrade()`.  一般我们都要继承它并实现这两个方法。

SQLiteOpenHelper有两上构造方法可重写.一般使用参数少的那个,这个方法接收4个参数：

1. 第一个参数是context
2. 第二个参数是数据库名
3. 第三个参数允许我们在查询数据时候返回一个自定义的Cursor,一般传null
4. 第四个参数表示当前数据库的版本号，可用于对数据库升级操作。

构建出SQLiteOpenHelper实例后，再调用`getReadabeDatabase()`或`getWritableDatabase()`方法就能够创建数据库了，数据库文件会存放在`/data/data/<packageName>/database`目录下。此时重写的`oncreate()`方法也会得到执行，通常会在此方法中处理一些创建表的逻辑。

*SQLite的数据类型很简单，integer 整型，real 浮点型，text文本类型，blob二进制类型。*

###对数据库进行CRUD操作
调用getReadableDatabase()或getWritableDatabase()会返回一个SQLiteDatabase对象，借助这个对象可以对数据进行CRUD操作:

1. 添加数据     
>使用`SQLiteDatabase`的`insert()`方法，它接收三个参数:
>
	1. 第一个参数是表名
	2. 第二个参数用于在未指定添加数据的情况下给某些可为空的列自动赋值NULL,一般直接传入null即可
	3. 第三个参数是一个ContentValue对象，它提供了一系列的put()方法重载，用于向ContentValues中添加数据，只需要将表中的每个列名以及相应的待添加数据传入即可。

2. 更新数据
>使用`update()`方法，它接收4个参数:
>
	1. 第一个参数是表名
	2. 第二个参数是ContentValues对象，要把更新的数据组装进去。
	3. 第三，四个参数用于约束更新某一行或某几行中的数据，不指定的话默认更新所有行。

3. 删除数据 
>使用`delete()`方法，它接收3个参数:
>
	1. 第一个参数是表名
	2. 第二，三个参数用于去约束删除某几行的数据，不指定默认删除所有行。

4.查询数据
>使用`Query()`方法查询，它最短的重载有7个参数:
>
	1. 第一个表名
	2. 第二个参数用于指定去查询哪几列，默认查询所有
	3. 第三，四个参数用于约束查询某一行或某几行的数据，不指定默认查询所有
	4. 第5个参数用于指定需要group by的列，不指定表示不对查询结果进行group by操作
	5. 第六个参数对group by后的数据进行过滤，不指定不进行过滤
	6. 第7个参数用于指定查询结果的排序方式，不指定使用默认排序方式。
	
![query方法参数](/images/query方法参数.png)



###使用SQL操作数据库：

	db.execSQL("insert into Book(name,author,pages,price)value(?,?,?,?)", new String[]{"Java","Dan Brown","122",18.8});
	
	db.execSQL("update Book set price=? where name=?",new String[]{"18.8","Java"}
	
	db.execSQL("delecte from Book where pages>?",new String[]{"100"});
	
	db.rawQuery("select * from Book",null);

###SQLite的事务
>首先调用`SQLiteDatabase的beginTransaction()`方法开启一个事务，然后在异常捕获的代码中去执行具体的数据库操作，当所有的操作都完成之后，调用`setTransactionSuccessful()`表示事务已经执行成功，最后在`finally`代码中调用`endTransaction()`来结束事务。

升级数据库，运用数据库版本号当指定的数据为版本号大于当前数据库版本时，就会进入到onUpdate()方法中去执行更新操作
