title: Struts2物点
date: 2016/9/18 星期日 PM 4:54:39 
updated: 2016/9/18 星期日 PM 4:54:44 
tags:
- struts2
- 技术
- ssh

categories: 后端
---
1.	Action 在struts1里是单实例的，在struts2是多例模式的
2.	Struts2的类型转换，对于8个原生数据类型以及Date,String等常见类型，Struts2可以使用内建的类型转换实现自动转换，但对于自定义的对象类型来说，就需要我们指定类型转换的方式了
3.	对于自定义的类型转换器来说，需要提供3个信息，Action的名字，Action中待转换的属性名以及该属性对应的类型转换器。其中Action的名字是通过属性文件名来获得的，Action中待转换的属性名是通过属性文件中的key来获得的，该属性对应的类型转换器是通过该key所对应的value来获得的。
4.	可以使用Struts2框架自带的类型转换器StrutsTypeConverter简化类型转换代码的编写，StrutsTypeConverter继承于DefaultTypeConverter父类，并且提供了两个抽象方法：convertFromString与converToString，分别表示从页面的字符串转换为后台对象以及从后台对象转换为页面的字符串，我们只需要实现这两个抽象方法即可实现类型转换。
5.	全局类型转换器，在src下与struts.xml同一目录下，xwrok-convertion.properties,该文件的内容是待转换的类=转换器的名字， 注意前面key需要全路径的类名。局部类型转换仅对某一个特定action的属性做转换
6.	Struts2也支持自定义方法，即在struts.xml中的action元素内定义method属性，属性值为待执行的方法。其中，该方法的声明要与execute保持一致。但是不推荐使用这种方式，因为它容易导致Action代码混乱。Action里的属性值增多，不同方法不同的属性值没有用。
7.	执行流程
	1.	首先进行类型转换
	2.	然后进行输入校验（执行validate方法）
	3.	如果在上述过程中出现了任何错误，都不会再去执行execute方法，会转向struts.xml中该action的名为input的result所对应的页面
8.	自定义Field级别的错误提示消息
	1.	新建一个以以Action名命名的properties文件，如，RegisterAction.properties
	2.	然后在该属性文件中指定每一个出错字段的错误消息：
`Invalid.fieldvalue.birthday=birthday invalid!`
9.	对于国际化的资源文件，其命名规则是：package_语言名_国家名，比如package_zh_CN,package_en_US
10.	Struts2框架校验执行顺序：
	1.	首先执行校验框架（xml文件）
	2.	执行自定义方法的校验方法（validateMyExcute）
	3.	执行validate方法   在这三个方法前执行的是类型转换
11.	JDK对于国际化的处理主要运用了Locale，ResourceBundle,MessageFormat三个类，locale代表了各个不同区域的代码，ResourceBundle可以用来加载属性文件并获取对应key的值，MessageFormat类用于设置属性文件里的参数
12.	我们可以在Action中定义异常与结果，也可以定义全局的异常与结果，局部总是优于全局的，如果定义成全局，那么可以为所有Action所公用，而局部的异常与结果只能被当前的Action所独享，不能为其他Action所共享。
13.	属性驱动与模型驱动的比较
	1.	属性驱动灵活，准确，模型驱动不灵活，很多时候，页面所提交过来的参数并不属于模型中的属性，也就是说页面所提交过来的参数与模型中的属性并不一致，这是很常见情况
	2.	模型驱动更加符合面向对象的编程风格，使得我们获得的是对象而不是一个个离散的值。
	3.	推荐使用属性驱动编写Action
14.	服务器端代码的单元测试有两种模式：
	1.	容器内测试（Jetty）
	2.	Mock测试（继承HttpServletRequest,HttpSession,HttpServletResponse等Servlet API） Jmock,easyMock
15.	Preparable接口的作用是让Action完成一些初始化工作，这些初始化工作是放在Preparable接口的prepare方法中完成的，该方法会在execute方法执行之前得到调用。
16.	采取请求转发的方式完成表单内容的添加会造成内容的重复插入（用户刷新一下页面就插入一条）增，删，更新的操作都应该防止这样的情况，应该采用重定向（带参数），采取重定向的方式不会造成数据的重复插入（Struts2里的结果类型redirect和redirectAction,redirectAction可以通过param里的actionName指定要重定向的action和其它参数名字指定携带的重定向参数）
17.	防止表单重复提交的两种方式
	1.	通过重定向
	2.	通过Session Token(Session令牌)  当客户端请求页面时，服务器会通过token标签生成一个随机数，并且将该随机数放置到session当中，然后将该随机数发向客户端，如果客户第一次提交，那么会将该随机数发往服务器端，服务器会接收到该随机数并且与session中所保存的随机数进行比较，这时两者的值是相同的，服务器认为是第一次提交，并且将更新服务器端的这个随机数值，如果此时再次重复提交那么客户端发向服务器端的随机数还是之前那个，两者不同，服务器认为是重复提交 结果名字invalid.token指向错误的页面
18.	拦截器（Interceptor）拦截器是Struts2的核心，Struts2的众多功能都是通过拦截器来完成的
19.	拦截器的配置
	1.	编写实现Interceptor接口的类
	2.	在Struts.xml文件 定义拦截器
	3.	在action中使用拦截器
20.	一旦定义了自己的拦截器，将其配置到action上后，我们需要在action的最后加上默认的拦截器栈：defaultStack
21.	方法过滤拦截器（可以对指定方法进行拦截的拦截器），在方法过滤拦截器中，如果即没指定includeMethods参数，也没有指定excludeMethods参数，那么所有方法都会被拦截。也就是所有的方法都被认为是includeMethods的（方法过滤拦截器继承MethodFilterIntercepter）. 拦截器里的方法参数ActionInvocation可以给要拦截的方法加一个监听器，表示在执行完action方法，到渲染页面前执行一些事情
22.	Package元素的namespace属性起到命名空间分割的作用，通常将namespace的属性值定义成页面所在的目录名
23.	进行文件上传时，必须将表单的method属性设为post ，将enctype属性设为multipart/form-data
24.	Struts2在进行文件上传操作时，实际上是通过两个步骤实现的
	1.	首先将客户端上传的文件保存到struts.multipart.saveDir所指定的目录中，如果該键所对应的目录不存在，那么就保存到javax.servlet.context.tempdir环境变量所指定的目录中
	2.	Action中所定义的File类型的成员变量file实际上指向的是临时目录中的临时文件，然后在服务器端通过IO的方式将临时文件写入到指定的服务器端目录中。
25.	OGNL（Object Graph Navigation Language）对象图上表达式语言，OGNL表达式中所指的元素，如果不是根元素就要加上#，Struts2的标签库就是基于OGNL来实现的，Struts2的所有标签库都支持OGNL
	1.	OgnlContext(上下文对象)，存在唯一的叫做根的对象（root）,可以通过程序设定上下文对象中的哪个对象作为根对象
	2.	在OGNL中，如果表达式没有使用#号，那么OGNL会从根对象中寻找该属性对应的get方法，如果寻找的不是根对象中的属性，那么则需要以#号开头，告诉OGNL，去寻找你所指定的特定对象中的属性。
	3.	当使用OGNL调用静态方法的时候，需要按照如下语法编写表达式：`@pacakage.classname@methodname(parameter)`,如果是静态成员变量，表达式如：`@pacakage.classname@staticfield` ,如果调用不是静态的方法直接在表达式里使用 `xx.methodname(parameter)`。  Java.lang.Math是其默认类，如果调用java.lang.Math类中的方法可以不用写，如：`@@methodname(parameters)`。
	4.	对于OGNL来说，数组与集合是一样的，都是通过下标索引来去访问的。构造集合的时候使用`｛。。。｝`形式
	5.	使用OGNL来处理映射（Map）的语法格式如下：`#{‘key1’:’value1’,’key2’:’value2’,’key3’:’value3’}[‘keyvalue’]`
	6.	OGNL的运算：过滤  `collection.{?|^|$ expression}`
		1.	过滤（filtering）:对集合进行过滤，语法格式为：collection.{? expression},在过滤操作中，#this代表当前操作的对象。如果persons集合里有几个person对象，每个person对象有name，现在拿到person的name长度大于4的集合：`#persons.{? #this.name.length()>4}`
		2.	OGNL针对集合提供了一些伪属性（如size,isEmpty）,让我们可以通过属性的方式来调用方法（本质原因在于集合当中的很多方法并不符合javabean的命名规则），但我们依然可以通过调用方法来实现与伪属性相同的目的
		3.	过滤获取到集合中第一个元素 `collection.{^ expression}`返回的是一个集合，只不过是包含符合条件的第一个元素
		4.	过滤获取到集合中最后一个元素 `collection.{$ expression}` 同样返回的是一个集合，取最后一个元素
	7.	OGNL的运算：投影 `collection.{expression}`。过滤对应数据库中操作行，而投影对应数据库表中操作列
26.	关于Struts2标签库属性值的%与#号的关系：
	1.	如果标签的属性值是OGNL表达式，那么无需要加上`%{}`
	2.	如果标签的属性值是字符串类型，那么在字符串当中凡是出现的`%{}`都会被解析成OGNL表达式，解析完毕后再与其它字符串拼接构造成最后的字符串值
	3.	 我们可以在所有的属性值上加上`%{}`，这样如果該属性值是OGNL表达式，那么标签处理类就会将%{}忽略掉
27.	在Struts2中，根对象就是ValueStack。在Struts2的任何流程当中，ValueStack中的最顶层对象一定是Action对象
	1.	Struts2中的命名参数对象  在Struts2中valuestack相当于ONGL中的根对象，所以通过OGNL表达式从action中取的成员变量并不需要加#号，而request这些命名参数并不在valuestack中，所有使用时要加上#号  
		1.	parameters, #parameters.username
		2.	request  #request.username
		3.	session, #session.username
		4.	application,#application.username
		5.	attr,#attr.username 在当前页面，request,session,application中依次找
28.	Struts2文件上传时，默认最大为2mb,自己可以在struts.xml中配置`<constant name=”struts.multipart.maxSize” value=”1012121212”></constant>`或者在struts.properties文件里配置`struts.multipart.maxSize=123123123`
