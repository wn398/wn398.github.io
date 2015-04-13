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

//打印出一个目录下所有的Java文件名

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
文件处理基础类
- Files 可以让你轻松复制，移动，删除或处理文件的工具类
- WatchService  用来监视文件或目录的核心类，不管它们有没有变化

主要功能有：
1. 创建和删除文件
2. 移动，复制，重命名和删除文件
3. 文件属性的读写
4. 文件内容的读取和写入
5. 处理符号链接
6. 用WatchService发出文件修改通知
7. 使用SeekableByteChannel----一个可以指定位置及大小的增强型字节通道。

###文件的移动，复制，创建

	//创建一个文件夹和一个文件
		Path pathDir = Paths.get("d:/myTest");
		Path pathDir2 = Paths.get("d:/myTest2");
		Path pathFile = Paths.get("d:/myTest/test.text");
		try {
			if(!pathDir.toFile().exists()){
				Files.createDirectories(pathDir);
			}
			if(!pathDir2.toFile().exists()){
				Files.createDirectories(pathDir2);
			}
			if(!pathFile.toFile().exists()){
				Files.createFile(pathFile);
			}
			//复制文件
			Files.copy(pathFile, Paths.get("d:/myTest2/copyTest.text"),StandardCopyOption.REPLACE_EXISTING);
			//移动文件
			Files.move(pathFile, Paths.get("d:/myTest2/moveTest.text"),StandardCopyOption.REPLACE_EXISTING);
		} catch (IOException e) {
			e.printStackTrace();
		}

###文件属性
//获取文件相关属性
	
	Path pathFile = Paths.get("d:/myTest/test.text");
		try {
			if(!Files.exists(pathFile)){
				Files.createFile(pathFile);
			}
			System.out.println(Files.getLastModifiedTime(pathFile));
			System.out.println(Files.size(pathFile));
			System.out.println(Files.isSymbolicLink(pathFile));
			System.out.println(Files.isDirectory(pathFile));
			System.out.println(Files.readAttributes(pathFile, "*"));//输出全部属性
		} catch (IOException e) {
			e.printStackTrace();
		}
一些相关特定文件系统的属性Java7也支持，如隐藏文件和链接文件
###利用Files快速读取写入文件

	Path file = Paths.get("d:/myTest/test.text");
		try {
			if(!Files.exists(file)){
					Files.createFile(file);
			}
			//利用传统IO写入,读取
			try(BufferedWriter bw = Files.newBufferedWriter(file, StandardCharsets.UTF_8, StandardOpenOption.APPEND)){
				bw.write("This a test line,This is a test line.");
			}
			
			try(BufferedReader br = Files.newBufferedReader(file, StandardCharsets.UTF_8)){
				String line;
				while((line = br.readLine())!=null){
					System.out.println(line);
				}
			}
			
			//简化后的读取和写入
			List<String> lines = new ArrayList<String>();
			lines.add("\n this is writed by the simplified Files class");
			lines.add("\n this is writed by the simplified Files 222");
			
			Files.write(file, lines, StandardCharsets.UTF_8, StandardOpenOption.APPEND);
			
			
			List<String> list = Files.readAllLines(file, StandardCharsets.UTF_8);
			for(String str:list){
				System.out.println(str);
			}
			
			
		} catch (IOException e) {
			e.printStackTrace();
		}

###文件修改通知
在Java7中可以用java.nio.file.WatchService类监测文件或目录的变化。
//检测目录下文件有没有被修改

	Path dir = Paths.get("d:/myTest");
		try{
			if(!Files.exists(dir)){
				Files.createDirectories(dir);
			}
			
			WatchService watcher = FileSystems.getDefault().newWatchService();
			//监测变化的key
			WatchKey key = dir.register(watcher, StandardWatchEventKinds.ENTRY_MODIFY);
			
			while(true){
				WatchKey keys = watcher.take();
				//得到下一个key的事件
				for(WatchEvent<?> event: keys.pollEvents()){
					//检测是否为监测事件
					if(event.kind()==StandardWatchEventKinds.ENTRY_MODIFY){
						
						System.out.println("File have been modified!!"+event.context());
					}
					
				}
				//重新监测事件
				key.reset();
			}
			
		}catch(final Exception e){
			e.printStackTrace();
		}

###SeekableByteChannel
这个接口可能让开发人员能够改变字节通道的位置和大小。JDK中有一个java.nio.channels.SeekableByteChannel接口的实现类--java.nio.channels.FileChannel.这个类可以在文件读取或写入时保持当前位置。它具有寻址能力。

如读取一个文件的最后500个字符，在文件的一个位置覆盖一些内容

	Path file = Paths.get("d:/myTest/test.text");
		//设置一个缓冲区
		ByteBuffer byteBuffer = ByteBuffer.allocate(512);
		try {
			//打开相关文件的FileChannel
			FileChannel channel = FileChannel.open(file, StandardOpenOption.WRITE,StandardOpenOption.READ);
			//读取文件最后500个字符到缓冲区
			channel.read(byteBuffer, channel.size()-500);
			//打印出读取的字符
			System.out.println(new String(byteBuffer.array()));
			
			//从倒数200的位置写入我的字符  写入会覆盖原来的相应位置字符的
			String str = "****This inserted string********";
			ByteBuffer bb = ByteBuffer.wrap(str.getBytes());
			channel.write(bb, channel.size()-200);

			//再读取文件最后500个字符到缓冲区
			byteBuffer.flip();//清除缓存内容
			channel.read(byteBuffer, channel.size()-500);
			//再打印出读取的字符
			System.out.println(new String(byteBuffer.array()));
			
		} catch (IOException e) {
			e.printStackTrace();
		}
		
##异步IO
异步IO是一种在读写操作结束前允许进行其他操作的IO处理

Java7的三个新的异步通道：
- AsynchronousFileChannel ---用于文件IO
- AsynchronousSocketChannel ---用于套接字IO，支持超时
- AsynchronousServerSocketChannel ---用于套接字接受异步连接

使用新的异步IO api时，主要有两种形式，将来式和回调式
###将来式
将来式用现有的Java.util.concurrent技术声明一个Future,用来保存异步操作的处理结果。通常用Future get()方法（带或不带超时参数）在异步IO操作完成时获取其结果。

AsynchronousFileChannel会关联线程池，它的任务是接收IO处理事件，并分发给负责处理通道中IO操作结果的结果处理器。跟通道中发起的IO操作关联的结果处理器确保是由线程池中的某个线程产生。

	try{
			Path path = Paths.get("D:/迅雷下载/ideaIU-14.0.3.exe");
			//异步打开文件，用后台线程
			AsynchronousFileChannel channel = AsynchronousFileChannel.open(path);
			//读取100000000字节，采取并发处理
			ByteBuffer buffer = ByteBuffer.allocate(100_000_000);
			Future<Integer> result = channel.read(buffer, 0);
		
			while(!result.isDone()){
				//可以做点其它事情
				System.out.println("Have not reading finish....,I can do some other things");
			}
			//获取结果
			Integer byteRead = result.get();
			System.out.println("Finish reading:"+byteRead);
		}catch(Exception e){
			e.printStackTrace();
		}

###回调式
回调式所采用的事件处理技术类似于Swing UI编程采用的机制。基本思想是主线程会派一个侦查员CompletionHandler到独立的线程中执行IO操作。这个侦查员将带着IO的操作的结果返回到主线程中，这个结果会触发它自己的completed或failed方法（要重写这两个方法）。在异步IO活动结束后，接口java.nio.channels.CompletionHandler<V,A>会被调用，其中V是结果类型，A是提供结果的附着对象。此时必须已经有了该接口completed（V,A）和failed(V,A)方法的实现，你的程序才能知道异步IO操作成功或失败时该如何处理。

	try{
			Path path = Paths.get("D:/迅雷下载/ideaIU-14.0.3.exe");
			//异步打开文件，用后台线程
			AsynchronousFileChannel channel = AsynchronousFileChannel.open(path);
			//读取100000000字节，异步回调处理
			ByteBuffer buffer = ByteBuffer.allocate(100_000_000);
			channel.read(buffer, 0, buffer, new CompletionHandler<Integer, ByteBuffer>() {
				
				//读取完成时的回调方法
				@Override
				public void completed(Integer result, ByteBuffer attachment) {
					System.out.println("Have read file finished:"+result);
				}
				//
				@Override
				public void failed(Throwable exc, ByteBuffer attachment) {
					System.out.println("Read file failed:");
					exc.printStackTrace();
					
				}
				
			});
			
			System.out.println("Main thread can do something-----");
			
		}catch(Exception e){
			e.printStackTrace();
		}

将来式和回调式的异步访问同样适用于AsynchronousServerSocketChannel和AsynchronousSocketChannel。我们可以用它们编写程序来处理网络套接字，比如语音IP。

##Socket和Channel的整合、
Java7推出了NetworkChannel,把Socket和Channel结合到一起。
- java.nio.channels包 定义通道，表示连接到执行IO操作的实体，比如文件和套接字，定义用于多路传输，非阻塞IO操作的选择器
- java.net.Socket类  该类实现了客户端套接字，套接字是两个机器间通信的端点。

###NetworkChannel
新接口java.nio.channels.NetworkChannel代表一个连接到网络套接字通道的映射。

	
	SelectorProvider provider = SelectorProvider.provider();
		try{
			//将NetworkChannel绑定到3999端口上
			NetworkChannel socketChannel = provider.openSocketChannel();
			SocketAddress address = new InetSocketAddress(3999);
			socketChannel = socketChannel.bind(address);
			//检查套接字选项
			Set<SocketOption<?>> socketOptions = socketChannel.supportedOptions();
			System.out.println(socketOptions.toString());
			//设置套接字Tos(服务条款)选项
			socketChannel.setOption(StandardSocketOptions.IP_TOS, 3);
			//获取SO_KEEPALIVE选项
			Boolean keepAlive = socketChannel.getOption(StandardSocketOptions.SO_KEEPALIVE);
		}catch(Exception e){
			e.printStackTrace();
		}
		

###MulticastChannel
利用它可以实现多播功能，为了让新来的NetworkChannel加入多播组，Java7提供了一个新接口java.nio.channels.MulticastChannel及其默认实现类DatagramChannel.你可以很轻松地对多播发送和接收数据。

	try {
			//选择网络端口
			NetworkInterface networkInterface = NetworkInterface.getByName("net1");
			//打开DatagramChannel
			DatagramChannel dc = DatagramChannel.open(StandardProtocolFamily.INET);
			//将通道设置为多播
			dc.setOption(StandardSocketOptions.SO_BROADCAST, true);
			dc.bind(new InetSocketAddress(8080));
			dc.setOption(StandardSocketOptions.IP_MULTICAST_IF, networkInterface);
			//加入多播组
			InetAddress group = InetAddress.getByName("127.0.0.1");
			MembershipKey key = dc.join(group, networkInterface);
			
		} catch (Exception e) {
			e.printStackTrace();
		}


