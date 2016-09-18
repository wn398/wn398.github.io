title: servlet3.0新特性
date: 2016/9/18 星期日 PM 5:46:51 
updated: 2016/9/18 星期日 PM 5:46:58 
tags: 
- servlet
- 技术
- 新特性

- categories: 后端
1.	基于注解（Annotation）的注解。 `WebServlet(name=””,urlPatterns={“”,””}) WebListener，WebFilter`
2.	异步支持（AsyncContext）  服务器通知客户端
	1.	整个页面刷新（meta）定时向服务器请求
	2.	Ajax异步请求（轮询，polling）
	3.	反向Ajax(Comet,长轮询，服务器端推技术：server-side pushing)服务器和客户端保持长时间连接。  Servlet3.0就是原生的支持，在新起的线程中轮询服务器端有没有更新数据
	4.	AsynListner
3.	Servlet3.0监听器，过滤器及servlet的动态注册
