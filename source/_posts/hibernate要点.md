title: hiberante要点
date: 2016/9/18 星期日 PM 5:39:40 
updated: 2016/9/18 星期日 PM 5:39:44 
tags:
- hibernate
- 技术
- ssh

categories: 框架
---
1.	Session接口是Hibernate应用中使用最广泛的接口，也被称作持久化管理器，它提供了和持久化相关的操作，如添加，更新，删除，加载和查询对象。它不是线程安全的，应该避免多个线程共享同一个session实例。Session实例是轻量级的
2.	HQL（Hibernate Query Language），面向的是类而不是数据库中的表，这是与sql	之间的差距，如果在一个项目中有重名的领域类名，则在HQL中需要加上类的包名
3.	对于分页操作来说，需要知道如下一些信息：当前正在操作的是第几页，每一页显示多少条记录数
4.	对于Query接口的list()方法与iterator()方法来说，都可以实现获取查询的对象，但是list()方法返回的每个对象都是完整的（对象中的每个属性都被表中的字段填充上了），而iterator（）方法中所返回的对象中仅包含了主键值（标识符），只有当你对iterator()中的对象进行操作时，Hibernate才会向数据库再次发送SQL语句来获取该对象的属性值,这两个不同的操作类型在优化数据库查询时要注意。
5.	用cascade配置级联的属性：
	![](/images/hibernate级联.png) 
6.	Hibernate和java,数据库中对应的类型图 
	![](/images/hibernate类型.png)
7.	Hibernate清理缓存的时间点  session级别的缓存为一级缓存
	1.	当应用程序调用org.hibernate.Transaction的commit()方法的时候，commit()方法先清理缓存，然后再向数据库提交事务
	2.	当应用程序显式调用session的flush()方法的时候
8.	SessionFactory级别的缓存叫做二级缓存
	1.	Hibernate提供了两级缓存，第一级缓存是Session的缓存。由于session对象的生命周期通常对应一个数据库事务或一个应用事务，因此它的缓存是事务范围的缓存。第一级缓存是必须的，不允许而且事实上也无法被卸载。在每一级缓存中，持久化类的每个实例都具有惟一的OID
	2.	第二级缓存是一个可插拔的缓存插件，它由sessionFactory负责管理。由于sessionFactory对象的生命周期和应用程序的整个进程对应，因此第二级缓存是进程范围的缓存。这个缓存中存放的是对象的散装数据。第二级缓存是可选的，可以在每个类或每个集合的粒度上配置第二级缓存。
9.	Session的update()方法
	1.	把客户对象重新加入到session缓存中，使它变成持久化对象
	2.	计划执行一个update语句。需要注意：session只有在清理缓存的时候才会执行update诗句，并且在执行时才会把Customer对象当前的属性值组装到update语句中。因此，即使程序中多次修改了客户对象的属性，在清理缓存时只会执行一次update语句。
10.	Hibernate的检索策略
	1.	立即检索策略
	2.	延迟检索策略
	3.	左外连接检索策略
11.	在多对一关联级别使用左外连接检索策略，默认情况下，多对一关联使用左外连接检索策略。如果把Order.hbm.xml文件的<many-to-one>元素的out-join属性设为true,总是使用左外连接检索策略。执行级联时合在一条语句中
12.	左外连接检索策略
	1.	优点：对应用程序完全透明，不管对象处于持久化状态，还是游离状态，应用程序都可以方便的从一个对象导航到与它关联的对象。 使用了外连接，select语句数目少。
	2.	缺点：可能会加载应用程序不需要访问的对象，白白浪费许多内存空间，复杂的数据库连接也会影响检索性能
	3.	适用范围：
		1.	多对一或者一对一关联。 
		2. 应用程序需要立即访问的对象  
		3. 数据库系统具有良好的表连接性能
13.	Map与set标签中的element子标签映射的是原子类型（string,date,int,long..）,即能够直接映射到数据库表字段上的类型，而one-to-many映射的则是实体类型，指的是无法映射到表的某个字段，而是要映射到整张表的类型，只有相互关联的实体设为inverse=true才能让对方来维护关联关系
14.	Bag(结合了List与Set)，可以重复且没有顺序的一种集合，是Hibernate提供的
15.	数据库排序使用的是`order-by`属性，内存排序使用的是`sort`,它有两个属性值（unsorted,natural）,其中natural指的是按照自然的升序排序，第三个属性值是我们自己写的排序规则类（实现comarater接口），然后将该自定义规则的类名作为sort的属性值即可。
16.	联合主键的映射规则
	1.	类中的每个主键属性都对应到数据库表中的一个主键列。Hibernate要求具有联合主键的实体类实现Serializable接口，并且重写上hashCode与equals方法，重写这两个方法的原因在于Hibernate要根据数据库的联合主键来判断某两行记录是否是一样的，如果一样的就认为是同一个对象，如果不一样，那么就认为是不同的对象。这反映到程序领域中的是根据hashCode与equals方法来判断某两个对象是否能够放到诸如set这样的集合中。联合主键的实体类实现Serializable接口的原因在于使用get或load方法的时候需要先构建出来该实体的对象，并且将查询依据（联合主键）设置进去，然后作为get或load方法的第二个参数传进去即可
	2.	将主键所对应属性提取出来一个类（称为主键类），并且主键类需要实现Serializable接口，重写equals方法与hashCode方法
17.	继承映射
	1.	每个子类一张表
	2.	一张表存储继承体系中所有的信息（数据库表实际上是继承体系中所有类的属性的并集所构成的字段）  需要在HBM文件中增加如下一行：
<discriminator column=”personType” type=”strubg”></discriminator>
	3.	公共信息放在父类表中，子类信息放在各自表中
18.	HQL检索方式  Query接口是HQL的查询接口
	1.	在查询语句中设定各种查询条件
	2.	支持投影查询，即仅检索出对象的部分属性
	3.	支持分页查询
	4.	支持连接查询
	5.	支持分组查询，允许使用having和group by关键字
	6.	提供内置聚集函数，如sum(),min()和max()
	7.	支持子查询，即嵌入式查询
	8.	支持动态绑定参数  
	如下：
	
	Query query = session.createQuery(“from Customer as c where “ + “c.name=:customerName” + “and c.age=:customeAge”);

//动态绑定参数
	`Query.setString(“customerName”,”Tom”);
	Query.setInteger(“customerAge”,21);`
//如果参数是对象类型要使用`setParameter(String name,Obejct value,Type type);`另一种更简单的方法是session.setEntity(String 参数名，Object 对象);
//执行查询语句，返回查询结果
`List result = query.list();`
19.	QBC api提供了检索对象的另一种方式，它主要由Criteria接口，Criterion接口和Expression类组成，它支持在运行时动态生成查询语句。
QBC检索方式：
//创建一个Criteria对象
`Criteria criteria = session.createCriteria(Customer.class);`转换对象成type类型，可以使用Hibernate.entity(对象名.class);
//设定查询条件，然后把查询条件加入到Criteria中

	`Criterion criterion1 = Expression.like(“name”,”T%”);
	Criterion criterion2 = Expression.eq(“age”,new Integer(21));
	Criteria = criteria.add(criterion1);
	Criteria = criteria.add(criterion2);`

	//执行查询语句，返回查询结果
	`List result = criteria.list();`

Query和Critera接口都提供了用于分页显示查询结果的方法：
--setFirstResult(int firstResult):设定从哪一个对象开始检索，参数firstResult表示这个对象在查询结果中的索引位置，索引位置的起始值为0.默认情况下，Query和Criteria接口从查询结果中的第一个对象，也就是索引位置为0的对象开始检索。
--setMaxResult(int maxResults):设定一次最多检索出的对象数目。默认情况下，Query和Criteria接口检索出查询结果中所有的对象。

20.	查询部分属性的使用方法：
	1. `Query query = session.createQuery(“select s.name,s.age 	from student s”);
		List list = query.list();
		For(int i=0;i<list.size();i++){
		Object[] obj = (Object[])list.get(i);
		System.out.println(obj[0]+”,”+obj[1]);
		}`
返回的是一个object的数组列表
	2.	查询部分属性放在对象里的方法：
`Query query = session.createQuery(“select new Student(s.name,s.age) from Student s”) ;`//把name,age离散值当作一个student，这次查询出来的结果就是student.  需要提供这样的构造函数
`List list = query.list();
For(int i=0;i<list.size();i++){
	Student s = list.get(i);
	System.out.println(s.name+“,”+s.age);`
}
21.	HQL连接查询  
	1.	内连接”from Team t join t.students”。 team类里有students属性，1对多。 返回的结果就是team和student作为一个数组的list列表。Team team = obj[0],Student student = obj[1]; 
22.	Hibernate事务
	1.	Mysql默认事务提交模式为自动提交，可以通过set @@autocommit =0 设置为手动提交，1是自动提交。设成手动提交后要显式的commit命令才能把前面的提交到数据库中。
	2.	数据库事务4个特性：
		1. 原子性（atom） 
		2.一致性(consistence)  
		3 隔离性(isolation)  
		4 持久性(duration)
	3.	多个事务并发运行时的并发问题
		1.	第一类丢失更新：撤消一个事务时，把其他事务已提交的更新数据覆盖（第一个人撤消一条 第二个人已更新后的数据）
		2.	脏读：一个事务读到另一个事务未提交的更新数据（第一人还没有提交更新第二人已经读到更新，当第一人撤消后，第二人读到的是更新后的数据）
		3.	虚读：一个事务读到另一个事务已提交的新插入的数据（第一人的新插入数据还没有提交，第二人就读到，当第一人撤消后，第二人读到算是不存在的数据）
		4.	不可重复读：一个事务读到另一个事务已提交的更新数据（）
		5.	第二类丢失更新：这是不可重复读中的特例，一个事务覆盖另一事务已提交的更新数据
	4.	查询事务隔离级别：select @@tx_isolation;如果设置隔离级别为Read committed:可以使用：set transaction isolation level read committed;
		1.	Hibernate的配置文件中可以显式的设置隔离级别 一种隔离级别对应一个整数：1. Read Uncommitted  2.Read Committed  4. Repeatable Read 8. Serializable. 通过hibernate.connection.isolation=2;设置
23.	乐观锁与悲观锁
	1.	悲观锁 是由数据库使用的  session.get(Accout.class,new Long(1),LockModel.UPGRADE);
	2.	乐观锁 是由应用程序提供的一种机制  这种机制既能保证多个事务并发访问数据，又能防止第二类丢失更新问题  一般通过version(hibernate 映射里有提供version标签，类型整型)或timestamp（hibernate提供了timestamp标签），hibernate每次做更新时都会检查版本号或timestamp。   
	3.	实现乐观锁的其它方式：如果应用程序是基于已有的数据库，而数据库表中不包含代表版本或时间的字段，Hibernate提供了其它实现乐观锁的办法，将class元素中 optimistic-lock属性设为“all” 如：<class name=”Account” talbe=”ACCOUNTS” optimistic-lock=”all” dynamic-update=”true”> Hibernate会在update语句的where子句中包含Account对象被加载时的所有属性：update ACCOUNTS set BALANCE=900 where ID=1 and NAME=”TOM” and BALANCE=”1000”
24.	Hibernate拦截器 应该程序能够响应Hibernate内部产生的特定事件是非常有用的。Intercepter接口提供了从会话（session）回调（callback）应用程序的机制，这种回调机制可以允许应用程序在持久化对象被保存，更新，删除或加载之前，检查并（或）修改其属性。  
	1.	你可以直接实现Interceptor接口，也可以（最好）继承EmptyInterceptor。拦截器可以有两种：--session范围内的， ---SessionFactory范围内的
	2.	使用拦截器 1. 定义实现Interceptor接口的拦截器类  2.通过session启用拦截器，或者通过Configuration启用全局拦截器
	3.	当使用某个重载sessionFactory.openSession()使用Interceptor作为参数调用打开一个session的时候，就指定了Session范围内的拦截器  sessionFcotry.openSession(new AuditInterceptor());
25.	Hibernate使用二级缓存 <property name=”hibernate.caceh.use_second_level_cache”>true</property>  <property name=”hibernate.cache.povider_class”>org.hibernate.cache.EhCacheProvider</property>
在需要缓存的地方设置<cache usage=”XXXX”/> usage有4种值可选，配置ehcache.xml
	1.	Transactional:必须在受管的环境下使用，保证可重复读的事务隔离级别，对于读/写比例大，很少更新的数据采用这个
	2.	Read-write:使用timestamp机制维护已提交事务隔离级别，对于读/写比例大，很少更新的数据采用这个
	3.	Nonstrict-read-write: 二级缓存与数据库中的数据可能会出现不一致的情况。在使用这种策略的时候，应该设置足够短的缓存过期时间，否则就有可能从缓存中读取到脏数据。当一些数据很少改变（一天，两天就不改变的数据），并且这些数据如果出现数据库与缓存不一致的情况下影响并不大的时候，那么可以采取这种缓存策略。
	4.	Read-only:当确定数据不会被改变时，我们可以使用这种策略
