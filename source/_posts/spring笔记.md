title: spring笔记
date: 2013/3/22 22:47:39
updated: 2013/3/22 22:47:54
tags:
- 技术
- spring

categories: ssh2
---
#spring简介
>spring是一个容器框架，它用于配置bean并维护bean之间的关系的框架(bean是java中的任何一种对象，javabean/service/action/数据源/dao,ioc(控制反转)，di(依赖注入))。它可以管理web层，业务层，dao层，持久层，该spring可以配置各个层的组件（bean）并且维护各个bean之间的关系 
当spring框架加载的时候，spring会自动的创建bean放入到内存中


#开发spring项目 
1. 引入spring架包（最小配置spring.jar该包把常用的jar都包括，还有common-loggings.jar） 
2. 创建一个spring的核心文件，`applicationContext.xml`,该文件一般放在src目录下，该文件中引入xsd文件，可以从给出的案例中拷贝一份 
3. 配置bean 

		<bean id="id" class=".."> 
		<property name="属性性名"> 
		<value>&&</value> 
		</property> 
		</bean> 

#在java文件中使用spring 
##得到spring的applicationContext对象（容器对象）
 
	ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");//创建spring容器对象，同时加载配置中的bean,此类为重量级，最好写一个工具类，单例模式设计 
	
	UserService us =(UserService)ac.getBean("bean的id");	//调用对象的方法 

spring实际上是一个容器构架，可以配置各种bean并且可以维护bean与bean之间的关系，当我们需要使用某个bean的时候，我们可以getBean(id)，使用即可。 

#ioc （inverse of controll）控制反转
>所谓控制反转就是把创建对象和维护对象的关系的权力从程序中转移到spring容器文件（applicationContext.xml） 

#di (dependency injection)依赖注入
>实际上di和ioc是同一个概念，spring的设计者认为di更能表示spring的核心 

spring开发提倡接口编程，配合di技术可以解决层与层之间的解耦，如web层与modle层 
bean工厂:最简单的容器，提供了基础的依赖 
注入支持。创建各种类型的Bean. 
应用上下文(ApplicationContext):建立在bean工厂基础之上，提供系统架构服务。 
#bean
##bean的scope 

	<bean scope=“singleton|prototype|request|session” /> 
	singleton: 单例 默认值 
	prototype: 原型 ，多例 
	request: 一次请求有效( java web开发中) 
	session: session级有效(java web开发中) 
	global-session:在web中spring容器ApplicationContext一致 

##bean工厂介绍 
工厂设计模式，创建分发各种bean。配置好它们之间的写作关系，参与bean的生命周期。
 
	BeanFactory factory = new XmlBeanFactory( 
	new ClassPathResource("applicationContext.xml")); 

bean工厂只把bean的定义信息载进来，用到的时候才实例化。factory.getBean("mybean");就可得到一个bean。 

##装配bean 
###ApplicationContext
与bean工厂中bean的区别： 

从ApplicationContext应用上下文加载配置文件时就会实例化所有的单例bean，而用bean工厂加载配置文件时不会实例化bean，只有在使用的时候才去实例化。 
**结论**：如果使用ApplicationContext，则配置的bean如果是singlton不管用不用都会实例化（好处是可以预先加载，缺点就是耗内存）。如果是BeanFactory则当你实例化该对象时候，配置的bean不会被实例化，当你使用的时候，才被实例化（好处是节约内存，缺点是速度），一般没有特殊要求，应当使用Application完成，手持设备可以使用beanfactory 

###使用应用上下文applicationContext 
>三种经常用到的实现 
* ClassPathXmlApplicationContext:从类路径中加载 
* FileSystemXmlApplictionContext:从文件系统中加载 
* XmlWebApplicationContext：从web系统中加载，就是当web容器启动的时候就会加载 

##bean的生命周期： 
1. 实例化（当程序加载配置文件的时候），把我们的bean(前提是scope=singleton)实例化到内存中 
2. 调用set方法，设置属性 
3. 如果实现了bean名字关注接口（beanNameAware），则可以通过setBeanName方法的参数获得bean在文件中配置的id 
4. 如果实现了bean工厂关注接口（beanFactoryAware）,则可以通过setBeanFactory方法获得beanFactory 
5. 如果实现了applicationContext关注接口（applicationContextAware）同样通过setApplicationContext方法获得上下文对象 
6. BeanPostProcessor后置处理器，写一个类实现这个接口，把这个类的bean配置到文件中，每实例化一个对象都会调用该处理器实现类的两个方法（before,after）,面向切面编程，针对所有对象编程，会调用before方法 
7. 如果实现的InitializingBean接口，则会调用afterPropertySet方法 
8. 如果自己在<bean init-method="init"/>则可以在bean定义自己的初始化方法，init为方法名 
也可用注解@PostConstruct 
9. 如果bean和一个后置处理器关联，则会调用Object postProcessAfterInitialization的after方法 
10. 现在可以使用bean了 
11. 关闭容器 
12. 如果实现disposiable接口，则在它的destroy方法中我们可以处理关闭bean之前做一些处理 
13. 也可以自己定制销毁方法<bean destroy-method="mydestroy"/>同定制初始化方法类似也可用注解@PreDestroy 

我们在实际开发中往往没有用到上面这么多的过程，常见的流程是1，2，6，9，10，11 
bean在工厂中创建的生命周期与applicationContext中生命周期不一样，factory相对简单一些 

##配置bean： 
1. 使用xml配置（三种方式，）spring的bean默认配置是单例模式，使用原型bean会对性能产生影响，尽量不要设置为singleton 
2. bean元素的property子元素指明了使用它的set方法来注入，可以注入任何东西，从基本类型到集合类，甚至是应用系统的bean,也可使用内部bean 

		<bean id=""> 
		<property name=""> 
		<!--第一种方法引用bean> 
		<ref bean=""> 
		<!--第二种方法，内部bean> 
		<bean></bean> 
		</property> 
		</bean> 

###如何给集合类型注入值 
1. 数组配置

		<property name="数据组名"> 
			<list> 
				<value>**</value> 
				<value>**</value> 
				... 
			</list> 
		</property> 
2. list配置

		<property name="list名"> 
			<list> 
				<ref bean="b1"> 
				<ref bean="b2"> 
				... 
			</list> 
		</property> 

3. set配置

		<property name="set名"> 
			<set> 
				<ref bean="b1"> 
				<ref bean="b2"> 
				... 
			</set> 
		</property> 

4. map配置 

		<property name="map名"> 
			<map> 
				<entry key="" value-ref=""/> 
				<entry key="" value-ref=""/> 
			</map> 
		</property> 
5. property配置 

		<property name="property名"> 
			<props> 
				<prop key="">值 </prop> 
			</props> 
		</property> 

###继承配置 
>在bean标签中配置一个parent='父对象id"，则会继承父对象中的属性值，如果自己再配置则会覆盖父对象中传过来的属性值 

###构造器注入方式 

		<bean id="id" class=""> 
			<constructor-arg index="0" type="java.lang.String" value=".."/> 
			<constructor-arg index="1" type="java.lang.String" value=".."/> 
		</bean> 

set注入的缺点是无法清晰表达哪些属性是必须的，哪些是可选的，构造注入的优势是通过构造强制依赖关系，不可能实例化不完全或无法使用的bean 

###自动装配方式注入（自动装配bean的属性值）： 
>有5种方式：`autowire=no,byName,byType,autodetect,constructor`. 
1. byName根据属性名自动装配（找id名与属性名相同的bean） 
2. byType根据类型自动装配，找不到，找到多个会报错 
3. constructor查找和bean的构造参数一致的一个或多个bean，若找不到或找到多个抛异常，按照参数的类型装配,构造函数只能有一个 
4. autodetect 3和2之间选一个， 
5. default 这个需要在beans属性中指定default-autowire="...",它指定了它下面所有bean的autowire的默认值。当你在<beans>指定了default-autowire后，所有有bean的默认autowire就是指定的装配方法,如果没有在<beans defautl-autowire=“指定”/>没有default-autowire="指定",则默认是default-autowire="no" 
6. no 不自动装配 

spring提供了`<context:annotation-config/>`配置，该配置可激活在类中探测到的各种注解 `@Required``@Autowire``@PostConstruct``@PreDestroy``@Resource``@EJB``@Persistence``@WebServiceRef`等等也可以选择为这此注解激活单独的后处理器。 
如AutowiredAnnotationBeanPostProcessor(处理自动装配) 
CommonAnnotationBeanPostProcessor(生命周期等) 
注意：该标记不能激活事务处理，需要使用tx:annotaion-driven 

###使用spring的特殊bean 
让spring特殊对待这些bean。使它们可以：
 
1. 通过配置后加工bean，涉及到Bean和Bean工厂生命周期。 
2. 改变依赖注入，将字符串转换成其它类型。 
3. 从属性文本装载信息，包括信息国际化。 
4. 监听并处理其它bean及spring发布的系统消息。 
5. 知道自己在spring中的唯一表识。 

###对bean进行后处理 
BeanPostProcessor接口提供机会来修改bean。

	public interface BeanPostProcessor{ 
	//Bean初始化(调用afterPropertiesSet()以及Bean的指 
	//定initmethod方法)之前被调用。 
	Object postProcessorBeforeInitialation(…); 
	//在初始化之后马上调用 
	Object postProcessorAfterInitialization(…); 
	} 

###注册后处理Bean 
如果使用的是Bean工厂,需要调用工厂的addBeanPostProcessor()来注册. 
factory.addBeanPostProcessor(…); 
如果使用的是上下文同其他Bean一样: 
`<bean id="" class="……" />`

###对bean工厂进行后处理 
BeanPostProcessor在bean载入后，对bean进行一些后处理工作。而BeanFactoryPostProcessor在bean工厂载入所有bean的定义后，实例化bean之前，对Bean工厂做一些后处理工作。

	　 public interface BeanFactoryPostProcessor{ 
	public void postProcessorBeanFactory(…); 
	} 
如果使用的是应用上下文不需要将他注册为Bean工厂后处理器。上下文会自动注册它。按照正常的Bean声明即可 
###分散配置(有两种方式引入文件) 
将配置文件分成几个分散的配置文件。如数据源。 
	
	<bean class="...PropertyPlaceholderConfigurer"> 
		<property name="locations"> 
			<list> 
				<value>xx/yy/db.properties</value> 
				<value>xx/yy/db2.properties</value> 
			</list> 
		</property> 
	</bean> 
	<!– 名字空间配置(2.5) --> 
	<context:property-placeholder location="classpath:com/hsp/spring/dispatcher/db.properties"/> 
如果有多个文件，在classpath后用逗号隔开写多个文件路径 

	jdbc.properties 
	database.url= 
	database.driver= 
	datebase.username= 
	datebase.password= 
###分散配置(续) 
使用占位符变量代替bean装配文件中的硬编码配置。占位符采 
	
	${variable}形式。 
	<bean id="datasource" 
	　　　 class="...DriverManagerDataSource"> 
	<property name="url"> 
		<value>${database.url}</value> 
	</property> 
	...... 
	</bean> 
###感知其他bean 
运行在spring容器中的bean不知道自己的注册名，运行在哪里。实现以下三个接口：

- `BeanNameAware`:知道自己的名字。 
- `BeanFactoryAware`：所处的bean工厂。 
- `ApplicationContextAware`：所在上下文 

实现BeanNameAware接口。无需为该接口的setBeanName()方法作任何处理，bean被载入时，容器会自动调用该方法，为其设置id或name的值。 
知道你在哪里 
实现BeanFactoryAware(感知)或ApplicationContextAware接口。分别定义了setBeanFactory()和setApplicationContext()方法。 

#AOP编程 
>面向切面（方面）编程，是对所有对象或者是一类对象编程，核心是在不增加代码的基础上，还增加新功能 
配置包括：被代理的对象，通知，代理对象 
前两个普通bean配置 
代理对象配置如下：
 
	<bean id="proxyFactoryBean" class="org.springframework.aop.framework.ProxyFactoryBean"> 
		<!--代理接口集--> 
		<property name="proxyInterfaces"> 
		<list> 
		<value>接口类</value> 
		<value>接口类</value> 
		</list> 
		</property> 
	<!--把通知织入到代理对象，配置通知--> 
		<property name="interceptorNames"> 
		<!--相当于拦截器--> 
		<value>通知id</value> 
		</property> 
		<！--配置代理的对象--> 
		<property name="target" ref=""/> 
	</bean> 
步骤: 

1. 定义被代理的接口 
2. 编写对象（被代理对象=目标对象） 
3. 编写通知（前置通知） 
4. 在spring文件中配置 
配置被代理对象，配置通知，配置代理对象是ProxyFactoryBean实例 
在代理对象中配置代理接口，织入通知 

spring中aop中当你通过代理对象去实现aop技术的时候，获取的proxyFactoryBean是什么类型？ 
返回的是一个动态代理对象，如果目标对象实现了接口，则spring使用jdk的proxy动态代理技术完成，如果目标对象没有实现接口，则spring使用cglib动态代理技术完成