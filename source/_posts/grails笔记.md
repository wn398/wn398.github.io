title: grails笔记
date: 2015/4/13 10:12:24 
updated: 2015/4/13 10:12:29 
tags:
- 技术
- grails
categories: grails
---
为什么要测试驱动开发？
>消除恐惧和不确定性是编写测试驱动代码的重要原因------测试驱动开发

#面向对象代码SOLID原则
- 单一职责原则（SRP）  每个对象都应该做一件事，并且只做一件事
- 开放/封闭原则（OCP）  对象应该是可扩展，但不可修改的
- 里氏替换原则（LSP）  对象应该可以被它的子类型实例替换
- 接口隔离原则（ISP）  特定的小接口更好
- 依赖倒置原则（DIP）  不要依赖具体实现

#Web框架的20条标准
1. 开发人员的工作效率---`能用1天或5天搭出一个CRUD页面吗`
2. 开发人员的看法---`用起来有意思吗`
3. 学习曲线----`学习一个星期或一个月后能干活吗`
4. 项目健康状态---`项目陷入绝境了吗`
5. 开发人员的充足性---`能找到经验丰富的开发人员吗`
6. 就业趋势---`将来能招到人吗`
7. 模板化---                `遵循DRY(不重复自己)原则吗`
8. 组件              ---                  `自带日期选择器之类的东西吗`
9. Ajax                                `支持客户端的异步JS调用吗`
10. 插件或附加项                `能加上Facebook集成之类的功能吗`
11. 扩展性                            `默认的控制器处理的并发用户数据能达到500+吗`
12. 测试支持                        `能做测试驱动的开发吗`
13. I18n或I10n                    `自带其他语种和地域的支持吗`
14. 校验                                `能轻松校验用户输入并迅速反馈吗`
15. 多语言支持                        `能同时用（比如）Java和Groovy吗`
16. 文档、教程质量                `常见的用例和问题在文档中都有体现吗`
17. 出版图书                             `有没有行业专家用它，并分享了自己的战斗事迹`
18. REST支持（服务端和客户端）        `它能按Http的设计宗旨使用该协议吗`
19. 移动支持                                    `是否很容易就能支持Android,ios和其他移动设备`
20. 风险程度                                    `是用来“保存食谱”的应用程序或是“核电站控制器”`

Grails是基于Groovy的快速Web应用框架，它集成了多个第三方类库，包括Spring,Hibernate,JUnit和Tomcat服务器等。它是一个完备的Web框架，上面的20条标准它全都满足

#Grails使用
##用Grails创建Web应用所需要的步骤如下：

1. 创建域对象
2. 测试驱动开发
3. 域对象的持久化
4. 创建测试数据
5. 控制器
6. GSP视图
7. 脚手架和自动化的UI创建
8. 快速开发的周转时间

`grails create-app` 名字，创建项目
`grails create-domain-class`  类完全名  创建域对象

##Grails插件
Grails有大量插件，可以帮助开发人员完成常见的Web开发任务，其中最流行的插件有：

1. Cloud Foundry Integration （用于将应用部署到云服务上）
2. Quartz (用于计划调度)
3. Mail 用于处理电子邮件
4. Twitter,Facebook 用于社交网络集成

要查看有哪些插件可用，执行`grails list-plugins`,然后再执行`grails plugin-info 名称`  查看插件详细信息

要安装插件请运行 `grails install-plugin 名称`

##Grails所有可用验证选项
![grails验证1.png](/images/grails验证1.png)
![grails验证2.png](/images/grails验证2.png)

##grails可用的验证错误消息
![grails验证错误信息1.png](/images/grails验证错误信息1.png)
![grails验证错误信息2.png](/images/grails验证错误信息2.png)

##grails关系映射
凭借`hasMany`声明，`belongsTo`声明和`toString`方法，我们已经设法创建了一个“一对多”关系 

##切换到外部数据库
1. 创建数据库，包括用户名和密码
2. 把JDBC驱动复制到grails-app/lib目录
3. 调整grails-app/conf/DataSource.groovy中的设置


##Grails中有4种范围：request,flash,session和application
1. request 范围里的变量，仅出现在响应中，然后就马上丢弃
2. flash范围里的变量（通常用于Grails中的错误消息 ）可以在单次重定向之后仍存在
3. session范围用于那些需要在多个请求中使用，但仅限于当前用户的值
4. application 范围里的变量是“全局的”--它们的生命周期跟Grails运行的时间一样长，在所有用户中共享


##sitemash模板
非常适合于组合两个完整的GSP
要在Grails里合建局部模板，只需要在GSP文件名前加下划线即可


##GrailsUnitTestCase提供了3个模拟方法：
- mockDomain()  它允许你模拟出整个数据库层
- mockForConstraintsTests() 它模拟所有约束条件
- mockLoggin() 

运行所有测试  `grails tes-app`
#Grails中的约定
##Controller和View的约定
1. 所有的Controller类的命名都以Controller结尾，并放在grails-app/controllers文件夹中

2. Controller中所有的public方法，都可以被web请求访问

3. 在一个空的Controller中加入static scffold = true或者static scaffold = <领域类的类名>，运行时将会动态生成相应的controller脚手架和GSP页面。

4. Controller中默认从	`org.codehaus.groovy.grails.plugins.web.api.ControllersApi`混入超过30个方法：
	```{bash}
	Object bindData(Object target, Object args) (plus five variants)
	Object chain(Map args)
	String forward(Map params)
	String getActionName()
	String getActionUri()
	ApplicationContext getApplicationContext()
	Map getChainModel()
	String getControllerName()
	String getControllerUri()
	Errors getErrors()
	FlashScope getFlash()
	GrailsApplication getGrailsApplication()
	GrailsApplicationAttributes getGrailsAttributes()
	ModelAndView getModelAndView()
	GrailsParameterMap getParams()
	HttpServletRequest getRequest()
	HttpServletResponse getResponse()
	ServletContext getServletContext()
	HttpSession getSession()
	String getTemplateUri(String name)
	String getViewUri(String name)
	GrailsWebRequest getWebRequest()
	boolean hasErrors()
	void header(String headerName, Object headerValue)
	Object redirect(Map args)
	Object render(Object o)
	Object render(String txt)
	Object render(Map args)
	Object render(Closure c)
	Object render(Map args, Closure c)
	Object withForm(Closure callable)
	```
	另外，`void render(Converter converter)`和`void jsonHeader(Object value)`从`org.codehuas.groovy.grails.plugins.converts.api.ConvertersControllersApi`混入。`Object withFormat(Closurecallable)`方法从`org.codehaus.groovy.grials.plugins.web.api.ControllersMimeTypesApi`混入。`

5. 如果一个controller中的action的名称，与其使用的GSP的名称相同，GSP放在相应的文件夹中，这时，你可以不用设置action对应的GSP。

	例如：
	
		class UserController{
		    def list(){
		        [conut: count, users: users]
		    }
		}
	如果grails-app/views/user/文件夹中存在list.gsp，那么grails将使用list方法返回的[count:count, users: users]来渲染list.gsp。当然，你可以指定gsp页面，例如：
	
		def list(){
		    render(view: 'list', model: [conut: count, users: users])
		
		}
6. 可以从paramsmap中拿到web请求的参数，或者调用某个重载了bindData的方法

7. GSP布局约定。在GSP的head标签中定义：<meta name='layout' content='main'>，意指使用grails-app/views/layouts/main.gsp作为该gsp页面的模板。使用的是Sitemesh模板技术。在Contoller级定义模板只需要在Controller类中设置布局属性：static layout = 'main'。全局模板定义需要在Config.groovy中定义：grails.sitemesh.default.layout = 'main'，Grails默认使用grails-app/views/layouts/application.gsp作为页面的模板。只需要在相应的级别放入模板页面，Grails将自动使用该模板。

##URI约定。

所有的URI规则约定配置在UrlMappings.groovy中，默认使用RESTfull风格。

##Service约定。

所有的Service类约定入在grails-app/services文件夹中，名称要求以Service结尾。所有的Service的方法默认是加事务的。默认情况，所有的service都将注册为Spring的一个bean，并且是singletons的。可以在service加入属性static scope = 'session'就可以更改其范围。bean的名称就是该Service类名第一个字母小写。

##Domain约定。

所有domain类都放在grails-app/domain文件夹中，doamin类所有字段默认都被持久化，不论字段访问范围（如public,private等）。类名将为表名，类中的字段名对应表中的字段名，不论类名、类中的字段名，如果是驼峰的，Grails将使用下划线+小写代替大写。

实体的实例，包括20个实例方法：

    * Object attach()
    * void clearErrors()
    * void delete()
    * void delete(Map)
    * void discard()
    * Errors getErrors()
    * Map getProperties()
    * Boolean hasErrors()
    * Serializable ident()
    * boolean instanceOf(Class)
    * boolean isAttached()
    * Object lock()
    * Object merge()
    * Object merge(Map)
    * Object mutex(Closure)
    * Object refresh()
    * Object save()
    * Object save(Map)
    * Object save(boolean)
    * BindingResult setProperties(Object)
    * String toString()
    * boolean validate()
    * boolean validate(boolean)
    * boolean validate(List)
    * boolean validate(Map)
包含70个静态方法：

	* static Integer count()
	* static Criteria createCriteria()
	* static void deleteAll(Object[])
	* static void deleteAll(Iterable)
	* static List executeQuery(String)
	* static List executeQuery(String, Collection)
	* static List executeQuery(String, Map)
	* static List executeQuery(String, Collection, Map)
	* static List executeQuery(String, Map, Map)
	* static Integer executeUpdate(String)
	* static Integer executeUpdate(String, Map)
	* static Integer executeUpdate(String, Collection)
	* static Integer executeUpdate(String, Collection, Map)
	* static Integer executeUpdate(String, Map, Map)
	* static boolean exists(Serializable)
	* static Object find(Object)
	* static Object find(String)
	* static Object find(Closure)
	* static Object find(Object, Map)
	* static Object find(String, Map)
	* static Object find(String, Collection)
	* static Object find(String, Collection, Map)
	* static Object find(String, Map, Map)
	* static List findAll()
	* static List findAll(Object)
	* static List findAll(String)
	* static List findAll(Closure)
	* static List findAll(Object, Map)
	* static List findAll(String, Map)
	* static List findAll(String, Collection)
	* static List findAll(Map, Closure)
	* static List findAll(String, Map, Map)
	* static List findAll(String, Collection, Map)
	* static List findAllWhere(Map)
	* static List findAllWhere(Map, Map)
	* static Object findOrCreateWhere(Map)
	* static Object findOrSaveWhere(Map)
	* static Object findWhere(Map)
	* static Object findWhere(Map, Map)
	* static Object first()
	* static Object first(String)
	* static Object first(Map)
	* static Object get(Serializable)
	* static List getAll()
	* static Integer getCount()
	* static PersistentEntity getGormPersistentEntity()
	* static Map getValidationErrorsMap()
	* static Map getValidationSkipMap()
	* static Object last()
	* static Object last(String)
	* static Object last(Map)
	* static List list()
	* static List list(Map)
	* static Object load(Serializable)
	* static Object lock(Serializable)
	* static Object merge(Object)
	* static Object proxy(Serializable)
	* static Object read(Serializable)
	* static List saveAll(Iterable)
	* static List saveAll(Object[])
	* static DetachedCriteria where(Closure)
	* static DetachedCriteria whereAny(Closure)
	* static DetachedCriteria whereLazy(Closure)
	* static Object withCriteria(Closure)
	* static Object withCriteria(Map, Closure)
	* static Object withDatastoreSession(Closure)
	* static Object withNewSession(Closure)
	* static Object withNewTransaction(Closure)
	* static Object withSession(Closure)
	* static Object withTransaction(Closure)
	* static Object withTransaction(TransactionDefinition, Closure)

