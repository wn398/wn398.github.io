title: hibernate曾旧的笔记
date: 2013/8/14 14:24:10 
updated: 2013/8/14 14:24:10 
tags:
- 技术
- ssh2
- hibernate
categories: ssh2
---
#使用hibernate步骤
1. 创建Hibernate的配置文件
2. 创建持久化类
3. 创建对象-关系映射文件
4. 通过Hiberante api 编写访问数据库的代码

#SessionFactory
一个SessionFactory实例对应一个数据存储源，应用从SessionFactory中获得Session实例，SessionFactory有以下特点：它是线程安全的，这意味着它只有一个实例可以被多个线程共享，这是重量级的。如果应用只访问一个数据库，只需要创建一个SessionFactory实例，在应用初始化时候创建一个该实例，如果应用同时访问多个数据库，则需要为每个数据库创建一个单独的`SessionFactory`实例。它主要用来创建session

#Session接口
Session接口是Hibernate应用使用最广泛的接口。session也被称为持久化管理器，它提供了和持久化相关的操作，如*添加，更新，删除，加载和查询对象*

它有以下特点：不是线程安全的，应该避免多个线程共享同一个Session实例,Session实例是轻量级的。session相当于一个数据库连接

注意：此session非彼Session(HttpSession)

#hibernate类型对应关系
<table>
	<tr><td>java类型</td><td>Hibernate类型</td><td>Sql类型</td><tr>
	<tr><td>java.lang.String</td><td>string</td><td>varchar</td><tr>
	<tr><td>int</td><td>int</td><td>int</td><tr>
	<tr><td>char</td><td>character</td><td>char(1)</td><tr>
	<tr><td>boolean</td><td>boolean</td><td>bit</td><tr>
	<tr><td>String</td><td>text</td><td>text</td><tr>
	<tr><td>byte[]</td><td>binary</td><td>blot</td><tr>
	<tr><td>java.sql.Date</td><td>date</td><td>date</td><tr>
	<tr><td>java.sql.Timestamp</td><td>timestamp</td><td>timestamp</td><tr>


#构建SessionFactory及Session支持类

	public class hibernateUtil {
	    static SessionFactory factory=null;
	    static Session session=null;
	      static{
	        try {
	            //读取配置文件（hibernate.cfg.xml）
	            Configuration cfg=new Configuration().configure();
	            //根据配置文件取得SessionFactory工厂
	            factory=cfg.buildSessionFactory();
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	        
	    }
	    
	    
	    //取得Session
	    public static Session getSession(){
	           
	        session=factory.openSession();
	        return session;
	    }
	    public static void closeSession(Session session){
	        if(session!=null){
	            if(session.isOpen())
	                session.close();
	        }
	        
	    }
	}
#hibernate的事务控制示例

	public void savePerson(Person person)
	{
	    Session session = HibernateUtil.getSession();
	               Transaction tx = session.beginTransaction();
	               try
	    {
	        session.save(person);
	                                tx.commit();
	    }catch(Exception ex)
	    {
	        if(null!=tx)
	        {
	            tx.rollback();
	        }
	    }
	    finally
	    {
	        HibernateUtil.close(session);
	    }
	}

#使用hql查询语句

	Query query = session.createQuery("HQL语句");
	List<People> list = (List<People>)query.list();
如果加入分页则可以用如下：

	Query query = session.createQuery("HQL语句").setFirstResult("开始记录数").setMaxResult("每页多少条记录");

把二进制文件从数据库里读出来：

	byte[] buffer = people.getFile();//获得从查出来的属性
	OutputStream os = new FileOutputStream("c:/"+people.getId+".pdf");
	os.write(buffer）;
	os.close();

#query方法与list方法区别
对于query接口的list()方法与iterator()方法来说，都可以实现获取查询的对象，但是list()方法返回的每个对象都是完整的（对象中的每个属性都被表中的字段填充上了），而iterator()方法所返回的对象中仅包含了主键值（标识符），只有当你对iterator()中的对象进行操作时，Hiberante才会向数据库再次发送SQL语句来获取该对象的属性值。实际上就是延迟加载

#理解Session的缓存
1. 当Session的save()方法持久化一个Customer对象时，Customer对象被加入到Session的缓存中，以后即使应用程序中的引用变量不再引用Customer对象，只要Session的缓存还没有被清空，Customer对象仍然处于生命周期中。
2. 当Session的load()方法试图从数据库中加载一个Customer对象时，Session先判断缓存中是否已经存在这个Customer对象，如果存在，就不需要再到数据库中检索。
3. 减少访问数据库的频率，应用程序从内存中读取持久化对象的速度显然比到数据库中查询数据的速度快多了，因此Session的缓存可以提高数据访问的性能。
4. 保证缓存中的对象与数据库中的相关记录保持同步。当缓存中持久化对象的状态发生了变化，Session并不会立即执行相关的SQL语句，这使得Session能够把几条相关的Sql语句合并成一条SQL语句，以便减少访问数据库的次数，从而提高应用程序的性能。

清理缓存（关闭session）是指按照缓存中对象的状态的变化同步更新数据库

Session会以下面的时间点清理缓存：

1. 当应用程序调用org.hibernate.Transaction的commit()方法的时候，commit()方法先清理缓存，然后再向数据库提交事务
2. 当应用程序显式调用Session的flush()方法的时候

hibernate自身一对多双向关系，可以表示无限的层级关系（任何孩子只有一个父母），在实体类里有一个对父对象的引用，还有一个集合表示它下面的孩子

Hibernate的二级缓存结构

1. Hibernate提供了两级缓存，第一级缓存是Session的缓存。由于Session对象的生命周期通常对应一个数据库事务或者一个应用事务，因此它的缓存是事务范围的缓存。第一级缓存是必须的，不允许而且事实上也无法被卸除。在第一级缓存中，持久化类的每一个实例都具有唯一的OID.
2. 第二级缓存是一个可插拔插件，它由SesssionFactory负责管理。由于SessionFactory对象的生命周期和应用程序的整个进程对应，因此第二级缓存是进程范围的缓存。这个缓存中存放的是对象的散装数据。第二级缓存是可选的，可以在每个类或每个集合的粒度上配置第二级缓存。

二级缓存EhCache在sessionFactory中配置

	<property name="hibernate.cache.user_second_level_cache">true</property>
	<property name="hibernate.cache.provider_class">org.hibernate.EhCacheProvider</property>

二级缓存一对多，多的一方，在set中加`<cache usage="read-only"/>`在多的对方配置文件里也要加`<cache usage="read-only"/>`
在src目录下加一个`ehcache.xml`,常见配置如下 

	<ehcache>
	<diskStore path="c:/ehcache"/>//在磁盘的存放目录
	//默认的缓存策略
	<defaultCache  maxElementsInMemory="200"//内存最多存放多少个对象
	       eternal="false"//是否永恒
	    timeToIdleSeconds="50"//
	    timeToLiveSeconds="60"//
	    overflowToDisk="true"
	/>
	
	    <cache name="com.hibernate.Student"/>//针对具体类的配置，它继承上面默认的也可以在里面设置覆盖。
	
	</ehcache>
二级缓存：
1. transactional:必须在受管的环境下使用，保证可重复读的事务隔离级别，对于读/写比例大，很少更新的数据通常可以采取这种方式。
2. read-write:使用timestamp机制维护已提交事务隔离级别，对于读/写比例大，很少更新的数据通常可以采取这种方式。
3. nonstrict-read-write:二级缓存与数据库中的数据可能会出现不一致的情况。在使用这种策略的时候，应该设置足够短的缓存过期时间，否则就有可能从缓存中读取到脏数据。当一些数据很少改变（一天，两天都不改变的数据），并且这些数据如果出现数据库与缓存不一致的情况下影响并不大的时候，那么可以采取这种缓存策略。
4. read-only:当确定数据不会被改变时，我们可以使用这种缓存策略

#在Hibernate应用中Java对象的状态
1. 临时状态（transient）:刚刚用new语句创建，还没有被持久化，不处于Session的缓存中。处于临时状态的Java对象被称为临时对象
2. 持久化状态（persistent）:已经被持久化，加入到Session的缓存中。处于持久化状态的Java对象被称为持久化对象
3. 游离状态（detached）:已经被持久化，但不再处于Session的缓存中。处于游离状态的Java对象被称为游离对象

Session的方法主要作用是把对象从游离状态转化为持久化状态
1. 把customer对象重新加入到Session缓存中，使它变为持久化对象
2. 计划执行一个update语句，值得注意的是，Session只有在清理缓存的时候才会执行update语句，并且在执行时才会把Customer对象当前的属性值组装到update语句中。因此，即使程序中多次修改了Customer对象的属性，在清理缓存时只会执行一次update语句。

#hibernate的检索策略：
1. 立即检索策略
2. 延迟检索策略
3. 左外连接检索策略

在多对一关联级别使用左外连接检索策略

1. 默认情况下，多对一关联使用左外连接检索策略
2. 如果把Order.hbm.xml文件的<many-to-one>元素的outer-join属性高为true，总是使用左外连接检索策略，即在查询的时候就会把关联一的那一方查询出来，只用一条sql语句，从而节省了sql语句。

**优点：**对应用程序完全透明，不管对象处于持久化状态，还是游离状态，应用程序都可以方便的从一个对象导航到与它关联的对象。使用了外连接，select语句数目减少。
**缺点：**可能会加载应用程序不需要访问的对象，白白浪费了许多内存空间。复杂的数据库表连接也会影响检索性能。

适用范围：

1. 多对一或才一对一关联
2. 应用程序需要立即访问的对象
3. 数据库系统具有良好的表连接性能

在程序中显式指定左外连接检索策略

1. 在映射文件中设定的检索策略是固定的，要么是延迟检索，要么是立即检索，要么是外连接检索。
2. 但应用逻辑是多种多样的，有些情况下需要延迟检索，而有些情况下需要外连接检索。
3. Hibernate允许在应用程序中覆盖映射文件中设定的检索策略，由应用程序在运行时决定检索对象力的深度。

以下Session的方法都用于检索OID为1的Customer对象：

	session.createQuery("from Customer as c where c.id=1");
	session.createQuery("from Customer as c left join fetch c.orders  where c.id =1");
在执行第一个方法时，将使用映射文件配置的检索策略，
在执行第二个方法时，在HQL语句中显式指定左外连接检索关联的Oreder对象，因此覆盖映射文件配置的检索策略。不管在Customer.hbm.xml文件中<set>元素的lazy属性是true还是false，Hibernate都会执行以下select语句：

	select＊from CUSTOMERS left outer join ORDERS on CUSTOMERS.ID = ORDERS.CUSTOMER_ID where CUTROMERS.ID =1;

#映射配置
##一对一映射：
1. 主键关联（主键值是一样的）

	<id name="id" column="id" type="string">
	    <generator class="foreign">//表示作为外键来使用，使用别的表的主键，只在一方这样配置，另一方正常
	        <param name="property">student</param>//使用那个表的主键，使用本类中student对象的表的id
	    </generator>
	</id>后面还要加上one-to-one配置，再配置上级联
一对一默认使用的是立即加载，如需要使用延迟加载，那么需要在one-to-one元素中将`constrained`属性设为true，并且将待加载的一方的class元素中的lazy属性高为true(或者不去设置，因为该属性默认值是true).一对一加载时默认使用左外连接，可以通过修改fetch属性为select修改成每次发送一条select语句的形式

fetch属性可以改变查询语句的方式，join表示连接查询，select表示单独select查询，one-to-one默认的是join连接查询

2. 外键关联（特殊的一对多）
在一方使用many-to-one加上属性unique="true"表示唯一就变成了one-to-one。

根据实体类配置生成数据库表结构

	SchemaExport export = new SchemaExport(new Configuration().configuer());
	export.create(true,true);

第一个参数打印出sql语句，第二个参数把sql语句导入到数据库

多对多关系类似于一对多，只是要在set中指定中间表，并在key与one-to-many中指定相关外键

map映射采用map标签,实际上是采用另外一张表的形式存储它

	<map name="students" table="student">
	    <key column="team_id"></key>//关联的表中的外键 
	    <index column="name" type="string"></index>//指定map中的键存储
	    <element column="description" type="string"></element>//指定map中的值
	</map>
如果map的值是另一个实体

	<map name="students" table="student">
	    <key column="team_id"></key>//关联的表中的外键 
	    <index column="name" type="string"></index>//指定map中的键存储
	    <one-to-many class="com.hibernate.Student"/>//必须有student映射文件
	</map>

set集合中存放的全是简单属性(一个类对应两张表)

	<set name="student" talbe="student">
	    <key column="team_id"></key>
	    <element column="name" type="string"></element>
	</set>

map与set标签中的element子标签映射的是原子类型（string,date,int,long...）,即使能够直接映射到数据库表字段上的类型，而one-to-many映射的则是实体类型，指的是无法映射到表的某个字段，而是要映射到整张表的类型

list的元素是可以重复的，而且是有顺序的。set中的元素是不允许重复的

	<list name="student" table="student" cascade="all">
	    <key column="team_id"></key>
	    <index column="index_"></index>//数据库中存放顺序的字符（不然无法排序）
	    <one-to-many class="com.hibernate.Student"/>
	</list>

不能加inverse="true"因为多的一方是无法知道排序的，所以要在一的一方进行维护

Bag（结合了List与Set），可以重复且没有顺序的一种集合，是Hiberante提供的，可以用list来模拟bag

	<bag name="student" table="student" cascade="all" inverse="true">
	    <key column="team_id"></key>
	    <one-to-many class="com.hibernate.Student"/>
	</bag>

#查询排序（内存排序及数据库排序）
数据库排序使用order-by="name asc"(name是数据库里的字段)，在set,list,bag，map标签上使用，order-by的内容可以指定多个字段排序，先按第一个字段排序，如果相等则按第二个字段排序

内存排序用`sort="natural"`按自然排序，它有三个属性（nusorted,natural）,其中的natural指的是按照自然的升序排序。第三个属性值是我们自定义的排序规则类（实现Comparator接口里的`comparat`方法），在sort中指定我们排序规则类的全称

#数据库联合主键
1. 类中的每个属性都对应到数据表中的每个主键列

	一个类有联合主键，则它必须实现serializable接口，并且重写equls和hashcode方法(两个方法中关联联合主键涉及到的属性)。hibernate要求具有联合主键的实体类实现Serializable接口，并且重写hashCode与equals方法，重写这两个方法的原因在于Hibernate要根据数据库的联合主键来判断某两行记录是否是一样的，如果一样认为是同一个对象，如果不一样，那么认为是不同的对象，这反映到程序领域中就是根据hashCode与equals方法来判断某两个对象是否能够放到诸如Set这样的集合当中。
	
	联合主键映射：
	
		<composite-id>
		    <key-property name="cardId" column="cardId" type="string"></key-property>
		               <key-property name="name" column="name" type="string"></key-property>
		</composite-id>
	
	联合主键的实体类实现Serializable接口的原因在于使用get或load方法的时候需要先构建出该实体的对象，并且将查询依据（联合主键）设置进去，然后作为get或load访求的第二个参数传进去即可

2. 将主键所对应的属性提取出来一个类（称之为主键类），并且主键类需要实现Seriliable接口并且重写hashCode()与equlas接口，在主类中引用这个主键类
	映射文件如下：
	
		<composite-id name="stuentPrimaryKey" class="com.hibernate.StudentPrimaryKey">
		  <key-property name="cardId" column="cardId" type="string"></key-property>
		               <key-property name="name" column="name" type="string"></key-property>
		</composite-id>
	这两种方式生成的表结构是一样的
	
	组件映射（把一对一关联映射到一张表中也只是针对单向的如学生的家庭地址和学校地址组成一个地址类在学生表中的映射）
	
		<component name="address" class="com.hibernate.Address">
		    <property name="homeaddress" type="string"></property>
		    <property name="schooladdress" type="string"></property>
		</component>
	在set标签中也可以用组件映射，适用于单向

3. 继承映射

	1. 每个子类一张表（映射方式没有特别，把父类的属性映射到子类文件中）
	`Query query = session.createQuery("from com.hibernate.Person");`//多态查询，会查询出它所有子类及它本身.如果没有映射文件，则必须加上包的名字
	2. 一张表存储体系中所有类的信息（数据库表实体上是继承体系中所有类的属性的并集构成的字段），映射用继承的父类来命名,需要在hbm文件中增加如下一行用来区分不同子类
	`<discriminator column="personType" type="string"></discriminatro>`
	
	子类信息配置

			<subclass name="com.hibernate.Student" discriminator-value="student">//类名
			    <property name="cardId" type="string"/>//属性
			</subclass>
			<subclass name="com.hibernate.Teacher" discriminator-value="teacher">//类名
			    <property name="salary" type="int"/>//属性
			</subclass>
	
	查询可以获得所有信息，通过instanceof比较子类，可以获得特定子类的信息
	3. 每个类一张表,公共信息放在父类表中，独有信息放在子类表中，每个子类对应一张表
	其中有关联父类表的id

		<joined-subclass name="com.hibernate.Student" table="student">
		    <key column="id"></key>//主键关联父类
		    <property name="cardId" type="string"></property>
		</joined-subclass>
		
		<joined-subclass name="com.hibernate.Teacher" table="teacher">
		    <key column="id"></key>//主键关联父类
		    <property name="salary" type="int"></property>
		</joined-subclass>

#Hibernate的查询
1. 导航对象图检索方式：根据已经加载的对象，导航到其他对象，如，对于已经加载的Customer对象，调用它的getOrders().iterator()方法可以导航到所有关联的Order对象，假如在关联级别使用了延迟加载检索策略，那么首先执行此方法时，Hibernate会从数据库中加载关联的Order对象，否则就从缓存中取得Order对象
2. OID检索方式：按照对象的OID来检索对象。Session的get()和load()方法提供了这种功能。如果在应用程序中事先知道了OID，就可以使用这种检索对象的方式
3. HQL检索方式： Hibernate提供了Query接口，这是Hibernate提供的专门的HQL查询接口，能够执行各种复杂的HQL查询语句
4. QBC查询方式：使用QBC（Query By Criteria）api来检索对象。这种api封装了基于字符串形式的查询语句，提供了更加面向对象的接口。

##hql检索步骤：
	//创建一个Query对象
	Query query = session.createQuery("from Customer as c where 
	    + "c.name=:customerName"
	    + "and c.age=:customerAge");
	//动态绑定参数
	query.setString("customerName","tom");
	query.setInteger("customerAge",21);
	//执行查询语句，返回查询结果
	List result = query.list();

##qbc检索方式
采用hql检索方式时，在应用程序中需要定义基于字符串形式的hql查询语句
qbc api提供了检索对象的另一种方式，它主要由Criteria接口，Criterion接口和Expression类组成，它支持在运行时动态生成查询语句

	//创建一个Criteria对象
	Criteria criteria = session.createCriteria(Custome.class);
	//设定查询条件，然后把查询条件加入到Criteria中
	Criterion creiterion1 = Expression.like("name","T%");
	Criterion creiterion2 = Expression.eq("age",new Interger(21));
	criteria=criteria.add(criterion1);
	criteria=criteria.add(criterion2);
	//执行查询语句，返回查询结果
	List result = criteria.list();

查询部分属性
	
	Query query = session.createQuery("select s.name,s.age from Student s");//查询部分属性
	List list = query.list();
	for(int i=0;i<list.size();i++)
	{
	    Object[] obj = (Object[])list.get(i);//查询出来的每一行数据都是以object[]形式返回
	    System.out.println(obj[0]+","+obj[1]);//第一个是name，第二个是age
	}
	
	Query query = session.createQuery("select new Student(s.name,s.age) from Student s");
此时返回的是包含部分信息的student对象集合（但必须提供以上形式的构造函数）

连接操作

内连接，返回的是两个对象的数组的集合

	Query query = session.createQuery("from Team t join t.students");//根据team与student内连接
	List list = query.list();
	for(int i=0;i<list.size();i++)
	{
	    Object[] obj =(Object[])list.get(i);
	               Team team = (Team)obj[0];//连接的第一张表
	     Student student =(Student)obj[1];//连接的第二张表
	    System.out.println(team.getName());
	}

进行表的连接查询，在hql中实际上是覆盖了延迟加载，都变成了直接加载了，配置文件中的不起使用。


对实体进行参数绑定查询方法

	Team team = (Team)session.get(Team.class,"id值");
	Query query = session.createQuery("from Student  s where s.team=:team and s.age >20");
	
	query.setParameter("team",team,Hibernate.entity(Team.class));//1.命名参数名字去掉冒号2.参数所赋值对象3.对象转化成type类型
	
	//以上也可以用这个简便方法 query.setEntity("team",team);
	List<Student> list = query.list();
	
	
	Query query = session.createFillter("参数1"，"hql查询条件(where age>20) ");参数1为获得的持久化对象的集合，这就是hibernate的过滤查询
	Query query = session.createFillter(tema.getStudents(),"where age>20");
	
	Criteria criteria = session.createCriteria(Student.class).add(Restrictions.between("age",new Integer(20),new Integer(30));查询年龄在20到30之间的学生
	List<Student> list = criteria.list();
	between是包含小的也包含大的
	
	Criteria criteria = session.createCriteria(Student.class).addOrder(Order.asc("age")).addOrder(Order.desc("cardId"));

hibernate官方推荐使用hql查询

#数据库的事务与并发处理
事务transation是一组相互依赖的操作行为，如银行交易，股票交易或网上购物，事务的成功取决于这些相互依赖的操作行为是否都能执行成功，只要有一个操作行为失败，就意味着整个事务的失败。数据库事务是对现实生活的模拟，它由一组在业务逻辑上相互依赖的SQL语句组成。

在实际系统中除了查询操作外，绝对不允许设计为自动提交

JTA java事务接口

每启动一个mysql.exe程序，就会得到一个单独的数据库连接，每个数据库连接都有个全局变量@@autocommit,表示当前的事务模式，它有两个值：

0--：表示手工提交模式

1--：默认值，表示自动提交模式

如果要察看当前的事务模式，可使用如下sql命令：`select @@autocommit`
如果要把当前的事务模式改为手工提交模式，可以使用sql命令：`set autocommit=0`
在自动提交模式下，每个sql语句都是一个独立的事务，mysql会自动提交这个事务

数据库事务的4个特性acid：

1. 原子性atom
2. 持久性consistence
3. 隔离性isolation
4. 持久性duration

在手工模式下，必须显式指定事务开始边界和结束边界：
事务的开始边界：begin
提交事务：commit
撤销事务：rollback

jdbc Connection提供了以下用于控制事务的方法
`setAutoCommit(boolean autoCommit)`设置是否提交事务,`commit（）`提交事务
`rollback（）`撤销事务

	try{
	    con=java.sql.DriveManager.getConnection(dbUrl,dbUser,dbPwd);
	    //设置手工提交模式
	    con.setAutoCommit(false);
	    stmt=con.createStatement();
	    //数据库更新操作1
	    stmt.executeUpdate("更新语句");
	    //数据库更新操作2
	    stmt.executeUpdate("更新语句");
	    con.commit();//提交事务
	}catch(Exception e){
	    tyr{
	        con.rollback();//操作不成功则撤销事务
	    }catch(Exception ex){
	    //处理异常
	    。。。
	    }
	    //处理异常
	}finally{...}

多个事务并发运行时的并发问题
1. 丢失更新：撤销一个事务时，把其他事务已提交的更新数据覆盖
2. 脏读：一个事务读到另一个事务未提交的更新数据
3. 虚读：一个事务读到另一个事务已提交的新插入的数据
4. 不可重复读：一个事务读到另一个事务已提交的更新数据
5. 第二类丢失更新：这是不可重复读中的特例，一个事务覆盖另一个事务已提交的更新数据

数据库的事务隔离级别：
`ReadUncommited`,`ReadCommited`,`Repeatable Read`,`Serializable`

查看当前的隔离级别：
`select @@tx_isolation`

设置隔离级别
`set transaction isolation level read committed`

在Hibernate的配置文件中中可以显式设置隔离级别。每一种隔离级别都对应一个整数：
1. Read Uncommitted
2. Read Committed
4. Repeatable Read
8. Serializable
在hibernate.cfg.xml文件中 `hibernate.connection.isolation=2`

使用悲观锁

	Account account = (Account)session.get(Account.class,new Long(1),LockMode.UPGRADE);

hibernate执行的select语句为：

	select * from ACCOUNTS where ID=1 for update;//多了个for update

乐观锁是由应用程序提供的一种机制，这种机制既能保证多个事务并发访问数据，又能防止第二类丢失更新问题
在应用程序中，可以利用Hibernate提供的版本控制功能来实现乐观锁。对象-关系映射文件中的<version>元素和<timestamp>元素都具有版本控制功能
-<version>元素利用一个递增的整数来跟踪数据库表中记录的版本
-<timestamp>元素用时间戳来跟踪数据库表中记录的版本

使用乐观锁
1. 在实体类中加一个version属性，作为版本控制，映射该属性的时候用`<version name="version" column="version" type="integer">`
时间戳方式控制
在实体类中另一个日期类型的属性作为时间戳，配置文件中`<timestamp name="lastDate" column="lastDate"/>`

悲观锁是由数据库底层实现的，乐观锁是由应用程序来实现的

实现乐观锁的其他方法：

如果应用程序是基于已有的数据库，而数据库表中不包含代表版本或时间戳的字段，Hibernate提供了其他实现乐观锁的办法。把<class>元素的optimistic-lock属性设为“all”：

	<class name="Account" table="ACCOUNTS" optimistic-lock="all" dynamic-update="true">
hibernate会在update语句的where子句中包含Account对象被加载时的所有属性：

	update ACCOUNTS set balanec=900 where id=1 and name="tom" and balance="1000";

#hibernate拦截器与事务
应用程序能够响应hibernate内部产生的特定事件是非常有用的。这样就允许实现某些通用的功能以及允许对hibernate功能进行扩展（监听事件）

持久层框架底层的拦截器机制是对诸如spring等业务管理容器拦截机制的有益的补充，使得我们可以在更低层次，更广的对象范围上进行AOP操作（Spring虽然将hibernate纳入到其容器管理的范围内，但是并没有途径实现对其实体对象的管理）。这样就允许实现某些通知的功能，以及允许对hibernate功能进行扩展。

Interceptor接口提供了从会话（session）回调（callback）应用程序（application）的机制，这种回调机制可以允许应用程序在持久化对象被保存，更新，删除或是加载之前，检查并（或）修改其属性。其子类是EmptyInterceptor

你可以直接实现Interceptor接口，也可以（最好）继承自EmptyInterceptor.

拦截器有两种：

-session范围内的

-sessionFactory范围内的

使用拦截器按如下步骤进行：

1. 定义实现Interceptor接口的拦截器
2. 通过session启用拦截器，或者通过configuration启用全局拦截器

当使用某个重载的`SessionFactory.openSession()`使用Interceptor作为参数调用打开一个session的时候，就指定了Session范围内的拦截器。

	Session session = factory.openSession(new AuditInterceptor());

SessionFactory范围内的拦截器要通过configuration中注册，而这必须在创建SessionFactory之前。在这种情况下，给出的拦截器会被这个SessionFactory所打开的所有session使用了，除非session打开时明确指明了使用的拦截器。SessionFactory范围内的拦截器，必须是线程安全的，因为多个session可能并发使用这个拦截器，要因此小心不要保存与session相关的状态

事件系统（Event system）
如果需要响应持久层的某些特殊事件，你也可以使用Hibernate3的事件框架。该事件系统可以用来替代拦截器，也可以作为拦截器的补充来使用。
基本上，Session接口的每个方法都有相应的事件。如LoadEvent,FlushEvent等等（可以查询xml配置文件的dtd以及org.hibernate.event包来获得所有已定义的事件的列表）

当某个方法被调用时，Hibernate Session会生成一个相应的事件并激活所有配置好的事件监听器（观察者模式）。被监听的方法所做的其实仅仅是激活监听器，实际的工作是由监听器完成的。你可以自由地选择实现一个自己定制的监听器:比如，实现并注册用来处理LoadEvent的LoadEventListener接口，来处理所有的调用Session的load()方法的请求。

监听器在运行时被实例化为单例，也就是说，所有同类型的事件的处理共享同一个监听器实例，因此监听器不应该保存任何与请求相关的状态。
用户定制的监听器需要实现相关的事件监听器接口，或者从一个合适的基类继承（甚至是从Higbernate自带的默认事件监听器继承）。

用户定制的监听器可以通过编程使用Configuration对象来注册，也可以在Hibernate的xml格式的配置文件中进行声明。

你还需要修改一处配置，来告诉Hibernate，除了默认的监听器，还要附加选定的监听器。

	<hibernate-configuration>
	    <session-factory>
	    ..
	    <event type="load">
	        <listener class="com.eg.MyLoadListener"/>
	        <listener class="org.hibernate.event.def.DefaultLoadEventListener">
	</event>
	</session-factory>
	</hiberante-configuration>

通过编程的方式注册

	Configuration cfg = new Configuration();
	LoadEventListener[] stack={new MyLoadListener(),new DefaultLoadEvnetListener()};
	cfg.getEventListeners().setLoadEventListeners(stack);

通过在xml配置文件声明而注册的监听器不能共享实例。如果在多个<listener/>节点中使用了相同类的名字，则每一个引用都将会产生一个独立的实例。如果你需要在多个监听器类型之间共享监听器的实例，则你必须使用编程的方式来进行注册。

数据库连接池（connection pool）,c3p0，apache的dbcp，hibernate内置支持c3p0连接池
可以主文件中用<property name="hibernate.c3p0.属性"></property>来设置连接池

hibernate tool与ant实现代码自动生成
在src目录下建build.xml与build.properties
build.properties内容
src=src
dbschema=dbschema//schema存储目录
libs=libs//ant所需要的架文件，hibernate.tool架包
bin=bin

build.xml文件内容

	<project name="hibernate_tools" basedir=".">(.表示当前目录)
	<property file="build.properties"></property>(指定属性文件位置)
	<target name="init">
	    <path id ="lib.path">
	        <pathelement path="${bin}"/>
	        <fileset dir="${libs}">
	            <include name="**/*.jar"/>(引用目录下所有文件)
	        </fileset>
	    </path>
	</target>

	<taskdef name="hibernatetools" classname="org.hibernate.tool.ant.HibernateTooTask" classpathref="lib.path">(定义任务)
	</taskdef>
	
	<target name="dbschema">
	    <hibernatetools>
	        <configuration configurationfile="${src}/hibernate.cfg.xml"/>
	        <hbm2ddl destdir="${dbschema}" export="true" outputfilename="dbschema.sql"/>
	        <hbm2java jdk5="true" destdir="${src}"/>
	    </hibernatetools>
	</target>
	
	
	</project>