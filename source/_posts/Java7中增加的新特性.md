title: Java7中增加的新特性
date: 2014/4/1 15:29:03 
update: 2015/4/1 15:29:08 
tags:
- 技术
- JDK
categories: JavaSE
---
#Java7语言，语法的变化
##switch语句中的String
在Java6及之前，case语句中的常量只能是`byte`,`char`,`short`和`int`(也可以是对应的封装类型Byte,Character,Short和Integer)或`枚举常量`。Java7中增加了String

	public static void main(String[] args) {
		TestSwitchUseString("1");
		TestSwitchUseString("2");
		TestSwitchUseString("");
	}
	
	public static void TestSwitchUseString(String str){
		switch (str) {
		case "1":
			System.out.println("It's Monday");
			break;
		case "2":
			System.out.println("It's Tuesday");
			break;
		default:
			System.out.println("It's Default");
			break;
		}
	}

##更强的数字文本表示法
1. 二进制文本再也不用这样去转换  `int x = Integer.parseInt("1100110",2);`
在Java7中可以直接这样写： `int x = 0b1100110;`

2. 可以用下划线（_）作数字的分隔符，为了阅读数字更容易,Java在编译时会去掉下划线。
如 `long num = 3_456_231_890L;`

##改善后的异常处理

###multicatch
这是在Java6及之前的处理方式：

	public Configuration getConfig(String fileName) {
    Configuration cfg = null;
    try {
      String fileText = getFile(fileName);
      cfg = verifyConfig(parseConfig(fileText));
    } catch (FileNotFoundException fnfx) {
      System.err.println("Config file '" + fileName + "' is missing");
    } catch (IOException e) {
      System.err.println("Error while processing file '" + fileName + "'");
    } catch (ConfigurationException e) {
      System.err.println("Config file '" + fileName + "' is not consistent");
    } catch (ParseException e) {
      System.err.println("Config file '" + fileName + "' is malformed");
    }

    return cfg;
  }

现在利用multicatch可以在一个catch中处理多个异常

	public Configuration getConfig(String fileName) {
    Configuration cfg = null;
    try {
      String fileText = getFile(fileName);
      cfg = verifyConfig(parseConfig(fileText));
    } catch (FileNotFoundException | ParseException | ConfigurationException e) {
      System.err.println("Config file '" + fileName
          + "' is missing or malformed");
    } catch (IOException iox) {
      System.err.println("Error while processing file '" + fileName + "'");
    }

    return cfg;
  }

###final重抛
`catch(final Exception e){... throw e}`

关键字final 表明实际抛出的异常就是运行时遇到的异常，这样就不会抛出笼统的异常类型，从而避免在上层只能用笼统的catch捕获。

##try-with-resources(TWR)
Java6及之前的资源要自己在finaly中关闭而在Java7中可以实现资源自动关闭 如：

	try(OutputStream out = new FileOutputStream(file); InputStream is = url.openStream()){
	    byte[] buf = new byte[4096];
	    int len;
	    while((len= is.read(buf))>0){
	        out.write(buf,0,len);
	    }
	}

这是资源自动化管理代码块的基本形式---把资源放在try的圆括号内。要确保try-with-resources生效，正确的用法是为各个资源声明独立变量。因为嵌套声明的话，java编译器可能不能正确按顺序释放相关资源。目前TWR特性依靠一个新定义的接口实现`AutoCloseable`.TWR的try从句中出现的资源类必须实现这个接口，Java7平台的大多数资源类都被修改过，已经实现了`AutoCloseable`

##钻石语法
针对创建泛型定义的实例太过繁琐的问题，Java7做了一项改进，以减少处理泛型时敲键盘的次数。如：
`Map<Integer,Map<String,String>> usersLists = new HashMap<Integer,Map<String,String>>();`

在Java7中编译器会推断出右侧的类型信息

	Map<Integer,Map<String,String>> usersLists = new HashMap<>();
#NIO.2
##IO发展简史
Java1.4中引入NIO增加了以下特性：

1. 为IO操作抽象出缓冲区和通道层
2. 字符集的编码和解码能力
3. 提供了能够映射为内存数据的接口
4. 实现非阻塞IO的能力
5. 基于流行Perl实现的正则表达式类库

NIO.2中的目标

1. 一个能批量获取文件属性的文件系统接口，去掉和特定文件系统相关的API，还有一个用于标准文件系统实现的服务提供接口
2. 提供一个套接字和文件都能够进行异步（与轮询，非阻塞相对）IO操作的API
3. 完成通道功能，包括额外绑定，选项配置和多播数据报的支持。

##Path
###关键基础类
- Path -->Path类中的方法可以用来获取路径信息，访问该路径中的各元素，将路径转换为其他形式，或提取路径中的一部分。有的方法还可以匹配路径字串以及移除路径中的冗余项
- Paths -->工具类，提供返回一个路径的辅助方法，比如get(String first,String...more)和get(URI uri)
- FileSystem -->与文件系统交互的类，无论是默认的文件系统，还是通过其统一资源标识（URI）获取的可选文件系统
- FileSystems -->工具类，提供各种方法，比如其中用于返回默认文件系统的FileSystems.getDefault();

>Path不一定代表真实的文件或目录。你可以随心所欲地操作Path，用Filse中的功能来检查文件是否存在，并对它进行处理。Path并不仅限于文件系统，它也能表示zip或jar这样的文件系统。

	//默认的获取Path实例方法
		Path path = Paths.get("/testpath/webmonitor/webmonitor.zip");//  等同于  Path path = FileSystems.getDefault().getPath("/testpath/webmonitor/webmonitor.zip");
		System.out.println("FileName:"+path.getFileName());
		System.out.println("Number of Name element:"+path.getNameCount());//路径名字分段个数
		System.out.println("Parent path:"+path.getParent() );
		System.out.println("Root path:"+ path.getRoot());
		System.out.println("SubPath from root:" + path.subpath(0, 1));
		
		path.normalize();//去掉冗余的信息
		System.out.println(path.toAbsolutePath());//获取绝对路径
		try {
			path.toRealPath();//这个是真实映射到实际物理硬盘上的路径
		} catch (IOException e) {
			e.printStackTrace();
		}
		
		//取得两个path之间的路径差
		Path testPath = Paths.get("/testpath/webmonitor");
		Path newPath = testPath.relativize(path);
		System.out.println("newPath:"+newPath);
		
		Path newPath2 = path.relativize(testPath);
		System.out.println("newPath2:"+newPath2);
		//合并path
		Path path1 = Paths.get("/test/");
		Path path2 = Paths.get("path2/file.name");//没加上“/”表示一个绝对路径
		Path path3 = path1.resolve(path2);// 等同于 path1.resolve("path2/file.name");
		System.out.println("path3:"+path3);

##处理目录和目录树
新引入的java.nio.file.DirectoryStream<T> 接口和它的实现类提供了很多功能：
1. 循环遍历目录中的子项，比如查找目录中的文件；
2. 用glob表达式（如 *Foobar*）进行目录子项匹配和基于MIME的内容检测（如text/xml文件）;glob模式匹配类似于正则表达式，但稍有不同
3. 用walkFileTree方法实现递归移动，复制和删除操作

###在目录中查找文件
//查找trunk工程中所有properties文件

	Path dir = Paths.get("C:/Users/twang/Desktop/trunk");//设定一个路径
		try(DirectoryStream<Path> stream = Files.newDirectoryStream(dir,"*.properties")){//返回一个经过过滤的DirectoryStream,其中包含.properties文件
			for(Path path:stream){
				System.out.println(path.getFileName());
			}
		}catch(IOException e){
			e.printStackTrace();
		}
//以上代码是单个目录中处理，如果需要递归过滤多个目标则用到时目录树

###遍历目录树
java7r提供了遍历目录树的功能，其中的关键方法是
`Files.walkFileTree(Path startingDir,FileVisitor<? super Path> visitor)`

实现FileVisitor接口比较麻烦，最少要实现下面5个方法（T 一般是path）

1. FileVisitResult preVisitDirectory(T dir)
2. FileVisitResult preVisitDirectoryFailed(T dir,IOException exc)
3. FileVisitResult visitFile(T file,BasicFileAttributes attrs)
4. FileVisitResult visitFileFailed(T file,IOException exc)
5. FileVisitResult postVisitDirectory(T dir,IOException exc)

Java7提供了一个默认的实现 SimpleFileVisitor<T>

//打印出一个目录下所有的Java文件

	private static void getAllJavaFile() {
		Path startDir = Paths.get("D:/tim/newWorkSpace");//设置起始目录
		try {
			Files.walkFileTree(startDir,new FindJavaVisitor());//调用 walkFileTree
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	private static class FindJavaVisitor extends SimpleFileVisitor<Path>{//扩展于SimpleFileVisitor<Path>

		@Override
		public FileVisitResult visitFile(Path file, BasicFileAttributes attrs)//重写文件访问方法
				throws IOException {
			if(file.toString().endsWith(".java")){
				System.out.println(file.getFileName());
			}
			return FileVisitResult.CONTINUE;//返回继续扫描的结果
		}
		
	}

##NIO文件系统IO
 