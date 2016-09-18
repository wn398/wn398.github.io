title: Java WEB
date: 2016/9/18 星期日 PM 2:30:24 
updated: 2016/9/18 星期日 PM 2:30:29 
tags:
- Web
- jsp
- servlet
- 技术


categories: 后端
---
1.	CSS的优先级
	1.	Inline style行内样式（在一HTML元素内）
	2.	Internal style sheet(inside the<head> tag)内嵌样式表
	3.	External style sheet 外部样式表
	4.	Browser default 浏览器默认
2.	CSS定义语法：`selector{property:value}`  如果一些属性被相同的选择器设置成不同的样式，值就会向更为具体的样式所继承（具体化）
3.	Http是一个基于请求/响应模式的，无状态的协议
4.	HTTP请求由三部分组成，分别是：请求行，消息报头，请求正文
	1.	请求行以一个方法符号开头，后面跟着请求URI和协议的版本，以CRLF作为结尾。请求行以空格分隔  格式为：`Method Request-URI HTTP-Version CRLF`
5.	HTTP响应由三部分组成，分别是：状态行，消息报头，响应正文
	1.	状态行由协议版本，数字形式的状态代码，相应的状态描述组成，各元素之间以空格分隔，除了结尾的CRLF（回车换行）序列外，不允许出现CR或LF字符，格式如下：`HTTP-Version Status-Code Reaseon-Phrase CRLF`
6.	HTTP响应状态代码：
	1.	1XX:指示信息-表示请求已接收，继续处理
	2.	2XX:成功-表示请求已经被成功接收，理解，接受
	3.	3XX:重定向-要完成请求必须进行更进一步的操作
	4.	4XX:客户端错误-请求有语法错误或请求无法实现
	5.	5XX:服务器端错误-服务器未能实现合法的请求
7.	Tomcat环境变量配置：要想启动Tomcat，需要配置好两个环境变量JAVA_HOME：值为JDK安装目录（bin目录的上一层目录），CATALINA_HOME:值为Tomcat的安装目录，C:\apache-tomcat-6.0.29
8.	配置Tomcat加载应用，可以在tomcat配置文件中</Host>前加上<Context path=”页面路径” docBase=”到WebRoot的路径” reloadable=”true”/>
9.	JSP声明格式`<%! Int a=3;%>`,脚本段形式：`<% int b=3;%>`当服务器将JSP转换为Servlet时，会将JSP中的声明转换为类的成员变量，而将脚本段转换为方法中的局部变量，Servelt是单实例的，这样成员变量的值只有一个，每个用户都会访问到它，而脚本段中的值则是局部变量，每个用户访问的时候各有一份，互不影响
10.	`<jsp:forward>` 指令用于转向页面，在该指令后面的所有代码都没有机会执行了，因为页面的流程已经转向了另外一个页面了。
11.	Request的`getParameter()`方法是`getParameterValues()`方法的特例，表示请求参数值只有一个，如果请求参数值有多个，要使用`getParameterValues()`方法。在多选框的情况下，需要使用`getParameterValues()`方法来获取用户所选择的多个复选框的值。Request的`getParameter()`方法作用是获取到客户端，通过表单或url请求参数所发送过来的参数值，是客户端与服务端之间的交互，服务器端要想获取到客户端发送过来的数据，就需要使用`getParameter()`方法来获取。
12.	Get的请求格式：
		`GET /test/LoginServet?username=heloo&password=world HTTP/1.1`
13.	Post的请求格式

	        Post /test/LoginServlet HTTP/1.1
    
    
    		Connection:Keep-Alive
    		Username=hello&password=word
14.	JSP执行流程：
	  ![](/images/jsp执行流程.png)
 
15.	JSP的九种内置对象
	1.	Request 请求对象
	2.	Response 响应对象
	3.	PageContext 页面上下文对象
	4.	Session 会话对象
	5.	Application 应用程序对象
	6.	Out 输出对象
	7.	Config 配置对象
	8.	Page 页面对象
	9.	Exception 异常对象
16.	HttpServletResponse对象的sendRedirect(String location)方法称作重定向。如果location地址前面加上“/”,则表示相对于Servlet容器的根来请求，即http://localhost:8080,如果location地址前没有加“/”,则表示相对于当前请求的URI来寻找地址。客户端会向服务端发送两个请求
17.	RequestDispatcher的forward(request,response)方法称作请求转发，请求转发，整个过程处于同一个请求中。 RequestDispatcher是通过HttpServletRequest对象的getRequestDispatcher()方法得到
18.	JSP访问JavaBean语法
	1.	导入JavaBean类 import
	2.	声明JavaBean对象  `<jsp:useBean id=”person” class=”com.tim.person” scope=”session”></jsp:useBean>`  id为新建的对象名
	3.	访问JavaBean属性  `<jsp:getProperty property=”name” name=”person”/>` name属性就是指前面useBean的id值创建出来的对象名称
	`<jsp:setProperty property=”name” name=”person” value=”lishi”或param=”**”/>`  可以使用param来设置值，是指request中的参数名
	4.	Scope属性决定了JavaBean对象存在的范围
		1.	Page(默认值)  客户每次请求访问JSP页面时，都会创建一个JavaBean对象。JavaBean对象的有效范围是客户请求访问的当前JSP网页。JavaBean对象在以下两种情况下会结束生命期：
			1.	客户请求访问的当前JSP页面通过<forward>标记将请求转发到另一个文件
			2.	客户请求访问的当前JSP页面执行完毕并向客户端发回响应
		2.	Request  客户每次请求访问JSP页面时，都会创建新的JavaBean对象，JavaBean对象的有效范围为：
			1.	客户请求访问的当前JSP页面
			2.	和当前JSP网页共享同一个客户请求的页面，即当前JSP网页中<%@inclued>指令以及<forward>标记包含的其它JSP文件
			3.	当所有共享同一个客户请求的JSP页面执行完毕并向客户端发回响应时，JavaBean对象结束生命周期
			4.	JavaBean对象作为属性保存在HttpRequest对象中，属性名为JavaBean的id，属性值为JavaBean对象，因此也可以通过HttpRequest.getAttribute()方法取得JavaBean对象
		3.	Session  JavaBean对象被创建后，它存在于整个Session的生存周期内，同一个Session中的JSP文件共享这个JavaBean对象。JavaBean对象作为属性保存在HttpSession对象中，属性名JavaBean的id,属性值为JavaBean的对象。除了可以通过JavaBean的id直接引用JavaBean对象外，也可以通过`HttpSession.getAttribute()`方法取得JavaBean对象
		4.	Application   JavaBean对象被创建后，它存在于整个web应用的生命周期内，Web应用中的所有JSP文件都能共享同一个JavaBean对象 JavaBean对象作为属性保存在application对象中，属性名为JavaBean的id,属性值为JavaBean对象，除了可以通过JavaBean的id直接引用JavaBean对象外，也可以通过`application.getAttribute()`方法取得JavaBean对象
19.	每一个Servlet都必须要实现Servlet接口,`GenericSerlet`是个通用的，不特定于任何协议的Servlet，它实现了Serlet接口，而`HttpServlet`继承于`GenericServlet`，因此`HttpServlet`也实现了Servlet接口，所以我们定义的Servlet只需要继承`HttpServlet`父类即可。Servlet接口中定义了一个service方法，HttpServlet对该方法进行了实现，实现方式就是将ServletRequest与ServletResponse转换为`HttpServletRequest`和`HttpServletResponse`. 转换完毕后会调用HttpServlet类中自己定义的service方法，在该service方法中，首先获得请求的方法名，根据方法名，调用对应的doXXX方法。
20.	SevletRequest接口的主要方法
	1.	GetAttribute
	2.	getContextType 返回客户请求数据MIME类型
	3.	getInputStream 返回以二进制方式直接读取客户端的输入流
	4.	getParameter  根据给定参数名返回参数值
	5.	getRemoteAddr  返回远程客户主机IP地址
	6.	getRemoteHost 返回远程客户主机名
	7.	getRemotePort 返回远程客户主机端口
21.	Servlet的生命周期分为三个阶段：
	1.	初始化阶段  `init()`  Servlet的整个生命周期中，init方法只会被调用一次
	2.	响应客户请求阶段  `service()`
	3.	终止阶段  `destroy()`  web应用被终止，或servlet容器终止运行，或servlet容器重新装载servlet的新实例时，servlet容器会先调用servlet的destroy方法，在destroy方法中可以释放已占用的资源
22.	某些Servlet在web.xml文件中只有<servlet>元素而没有<servlet-mapping>元素，这样我们就无法通过url地址的方式访问这个servlet了，这种servlet通常会在servlet元素中配置一个`<load-on-startup>`子元素，让容器在启动的时候自动加载该servlet并用调用其`init`方法完成 一些全局性的初始化工作
23.	当Servlet容器启动web应用时，并为每个web应用创建惟一的`ServletContext`对象。你可以把ServletContext看成是一个web应用的服务器端组件的共享内存。在ServletContext中可以存放共享数据，它提供了读取和设置共享数据的方法 `setAttribute()`,`getAttribute();`  `request.getSession().getServletContext();`它也就是JSP页面的application对象
24.	Servlet的多线程同步问题：servlet本身是单实例的，这样当有多个用户同时访问某个servlet时，会访问该唯一的Servlet实例中的成员变量，如果对成员变量进行写入操作，那就会导致servlet的多线程问题，即数据不一致。解决方案：去除实例变量，使用局部变量
25.	Web服务器跟踪客户状态通常有四种方法
	1.	建立含有跟踪数据的隐藏字段
	2.	重写包含额外参数的URL
	3.	使用持续的cookie
	4.	使用Servlet API中的session(会话)机制
26.	Session的运行机制
	1.	当一个session开始时，servlet容器将创建一个HttpSession对象，在HttpSession对象中可以存放客户状态的信息（如购物车）
	2.	Servlet容器为HttpSession分配一个惟一标志符，称为Session ID. Servlet容器把Session ID作为Cookie保存在客户的浏览器中。
	3.	每次客户发出HTTP请求时，Servlet容器可以从HttpServletRequest对象中读取Session ID,然后根据Session ID找到相应的HttpSession对象，从而获取客户状态信息
27.	默认情况下，JSP网页都是支持Session的，也可以通过以下语句显式声明支持Session:
`<%@ page session= “true”>` 默认为true
28.	HttpSession接口
	1.	GetId()  返回Session的ID
	2.	Invalidate()  使当前的Session失效，Servlet容器会释放HttpSession对象占用的资源
29.	Servlet过滤器
	1.	Servlet过滤器能够对servlet容器的请求和响应对象进行检查和修改
	2.	Servlet过滤器本身并不生成请求和响应对象，它只提供过滤作用
	3.	它能够在Servlet被调用之前检查Request对象，修改Request Header和Request内容
	4.	在servlet被调用之后检查Response对象，修改Response Header和Response内容。Servlet过滤负责过滤的web组件可以是`Servlet`,JSP或html文件
30.	Servlet过滤器Filter接口生命周期   filter也是单实例的，多线程也要注意
	1.	init(FilterConfig):这是Servlet过滤器的初始化方法，Servlet容器创建Servlet过滤器实例后将调用这个方法。在这个方法中可以读取web.xml文件中Servlet过滤的初始化参数
	2.	doFilter(ServletRequest req,ServletResponse res,FilterChain chain) 这个方法完成实际的过滤操作。当客户请求访问与过滤器关联的URL时，Servlet容器将先调用过滤器的doFilter方法，FilterChain参数用于访问后续过滤器。在自定义的过滤器中这个方法里，要显式地调用doFilter(xx)方法，以方便调用后面的过滤器。如果不加的话，请求到这里就结束了，到不了severlet。doFilter()方法后面的代码是在severlet执行完后response返回来会执行。
	3.	Destroy()：servlet容器在销毁过滤器实例前调用该方法，在这个方法中可以释放servlet过滤器占用的资源。
	 ![](/images/servlet过滤器.png)
31.	串联Servlet过滤器工作流程
    ![](/images/串联servlet过滤器.png)
32.	Servlet Listner
	1.	Listener是Servlet的监听器，它可以监听客户端的请求，服务端的操作等。通过监听器，可以自动激发一些操作，比如监听在线的用户的数量。
	2.	当增加一个HttpSession时，就激发sessionCreated(HttpSessionEvent se)方法，这样就可以给在线人数加1 
33.	过滤器与监听器的区别，过滤器会过滤所有经过的请求，而监听器只会监听一些事件。
34.	常用的监听接口有以下几个
	1.	ServletContextAttributeListener监听对ServletContext属性的操作，比如增加，删除，修改属性
	2.	ServletContextListener监听ServletContext。当创建ServletContext时，激发contextInitialized(ServletContextEventsce)方法：当销毁ServletContext时，激发contextDestoryed(ServletContextEventsce)方法
	3.	HttpSessionListener 监听HttpSession的操作。当创建一个Session时，激发sessionCreated(HttpSessionEvent se)方法，当销毁一个Session时，激发sessionDestroyed(HttpSessionEvent se)方法
	4.	HttpSessionAttributeListener监听HttpSession中的属性操作。增加，替换，去除
35.	表达式语言EL(expression language)
语法：`${expr}`  EL默认对象  pageContext  pageScope  requestScope  sessionScope applicationScope  param  paramValues  header  headerValues cookie 
EL存取变量数据的方法很简单，如：${username}.它的意思是取出某一范围中名称为username的变量。因为我们并没有指定哪一个范围内的username,所以它的默认值会先从page范围找，假如找不到，再依序到Request,Session,Application范围。如果途中找到username，就直接回传，不再继续找下去，如果全部范围都没有找到时就回传null.
36.	创建客户化JSP标签的步骤
	1.	创建标签的处理类
		1.	Servlet容器编译JSP网页时，如果遇到自定义标签，就会调用这个标签的处理类
		2.	标签处理类必须扩展以下两个类之一：`javax.servlet.jsp.tagext.TagSupport`
`Javax.servlet.jsp.tagext.BodyTagSupport`
		3.	JSP容器在调用`doStartTag`或`doEndTag`方法前，会先调用`setPageContext`和`setParent`方法，设置`pageContext`和`parent`
		4.	在`doStartTag`或`doEndTag`方法中可以通过`getParent`方法获取上层标签的处理类;在TagSupport类中定义了protected类型的pageContext成员变量，因此在标签处理类中可以直接访问`pageContext`变量
		5.	PageContext类提供了保存和访问Web应用的共享数据的方法：`public void setAttribute(String name,Object value,int scope)`
`Public Object getAttribute(String name,int scope)`
		6.	在标签处理类里应该将这个标签属性作为成员变量，并且分别提供设置和读取属性的方法
	2.	创建标签库描述文件
		1.	标签库的后缀名为`.tld`,必须存放在与web.xml同一目录下
			  ![](/images/tld描述文件.png)
	3.	在JSP文件中引入标签库，然后插入标签，如`<mm:hello/>`
`<%@ taglib uri=”/myTag” prefixe=”hello”%>` prefix可以随意定
