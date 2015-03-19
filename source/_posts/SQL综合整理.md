title: SQL综合整理
date: 2012/10/18 13:28:14
updated: 2015/3/19 13:52:05 
tags:
- 技术
- sql
- 数据库
categories: 数据库 
---
>SQL语言分为三部分:
>
	   数据定义语言  (Data Definition Language) DDL
	   数据操作语言   (Data Manipulation Language)DML
	   数据控制语言  （Data Control Language）DCL

# 数据定义语言DDL

## 创建数据库

```{bash}
create database name;
```

##删除数据库

```{bash}
 drop database name;
```
##创建数据库表

```{bash}
create table table_name(属性名  数据类型[完整性约束条件],
	属性名 数据类型[完整性约束条件],
	...
	属性名 数据类型);
```
>完整性约束条件包括：
>
	   primary key   主键
	   foreign key   外键
	   not null      不能为空
	   unique         唯一
	   auto_increament自增长
	   default 为属性设置默认值  

例1： 创建单字段主键

	  create table example1(
		  stu_id int primary key,
		  stu_name varchar(20),
		  stu_sex boolean
		  );

例2: 创建多字段主键

	create table exampel2(
	    stu_id int,
	    course_id int,
	    grade float,
	    primary key(stu_id,course_id)
	    );
###外键
设置外键的语法规则：
```{bash}
	constraint 外键名 foreign key(属性1.1，属性1.2，...,属性1.n)
                    references 表名(属性2.1，属性2.2，...,属性2.n)
```

例3： 创建含有外键的表：

	create table example3(
	    id int primary key,
	    stu_id int,
	    course_id int,
	    constraint c_fk foreign key(stu_id,course_id) references example2(stu_id,course_id)
	    );

例4： 非空约束表

	  create table example4(
		  id int not null primary key,
		  name varchar(20) not null,
		  stu_id int,
		  constraint d_fk foreign key(stu_id) references example1(stu_id)
		  );

例5： 唯一性约束

	  create table example5(
		  id int primary key,
		  stu_id int unique,
		  name varchar(20) not null
		  );
 
例6： 自动增长 auto_increment 默认从1开始
 
	  create table example6(
		  id int primary key auto_increment,
		  stu_id int unique,
		  name varchar(20) not null
		  );
 
例7： 默认值

	  create table example7(
		  id int primary key auto_increment,
		  stu_id int unique,
		  name varchar(20) not null,
		  English varchar(20) default 'zero',
		  Math float default 0,
		  Computer float default 0
		  );
##查看表结构
>查看表结构的字段数据类型，是否为空值，主键等：

```{bash}
describe 表名 //可以缩写为  desc 表名
```
##查看表的详细定义
>查出建表语句

```{bash}
show create table table_name;
```
##修改表结构

###修改表名

```{bash}
alter table 旧表名 rename [to] 新表名;
```

例8：`alter table example0 rename user;`

###修改表字段数据类型
```{bash}
   alter table 表名 modify 属性名 数据类型;
```

例9： `alter table user modify name varchar(30);`

###修改表字段名称
```{bash}
	alter table 表名 change 旧属性名 新属性名 新数据类型
```

例10：

	   alter table example1 change stu_name name varchar(20);修改字段名
	   alter table example1 change stu_sex sex int(2);修改字段名与长度
###为表添加新字段
```{bash}
	alter table 表名 add 属性名1 数据类型[完整性约束条件][first | after 属性名2];
```

例11：

1. 增加无完整性约束条件的字段
	
		alter table user add phone varchar(20);
2. 增加有完整性约束条件的字段
    	
		alter table user add age int(4) not null;
3. 增加放并放到第一个位置
     
		alter table user add num int(8) primary key first;
4. 增加字段并放到指定字段后面
    
		alter table user add address varchar(30) not null after phone;


###删除字段
```{bash}
   alter table 表名 drop 属性名;
```

例12：
   
	alter table user drop id;

###修改字段的排列位置
```{bash}
   alter table 表名 modify 属性名1 数据类型 first|after 属性名2;
```

例13:

1. 字段修改到第一个位置
   	
		alter table user modify name varchar(30) first;

2. 把一个字段放到另一个字段后面
   
		alter table user modify sex tinyint(1）after age;

###修改表的存储引擎
```{bash}
alter table 表名 engine=存储引擎名
```

例14：

	   alter table user engine=myisam;

###删除表的外键约束
```{bash}
   alter table 表名 drop foreign key 外键别名;
```

例15：

	   alter table example3 drop foreign key c_fk;

###删除表

1. 删除没有被关联的表
```{bash}
drop table 表名;
```
2. 删除被其它表关联的表
>先删除关联外键再删除表
```{bash} 
   	alter table example4 drop foreign key d_fk;
    drop table example1;
```
## 索引
>索引是一种特殊的数据库结构，可以用来快速查询数据库表中的特定记录，提高对表中数据的查询速度.索引是由数据库中的一列或多列组成

>索引优点：
>>可以提高检索速度，对于有依赖关系的子表和父表之间联合查询时，可以提高查询速度

>索引缺点：
>>创建和维护索引需要耗费时间，耗费时间的数量随着数据量的增加而增加，它需要占用物理空间，每一个索引要占一定的物理空间：增加，删除和修改数据时，都要进行维护索引，造成数据的维护速度降低了。

>索引分类:
>
1. 普通索引  创建时不附加任何限制条件，其值是否唯一和非空由字段本身决定
2. 惟一性索引  创建时限定值是惟一的
3. 全文索引  通过全文索引可以快速查询数据量较大的类型字段
4. 单列索引  在单个字段上创建索引
5. 多列索引  该索引指向创建时对应的多个字段 id,name,sex建一个索引 查询时只有使用id才能使用索引
6. 空间索引 只能建立在空间数据库上.空间类型包括 Geometry point, linestring.polygon等 目前只有myISAM存储引擎支持空间检索 很少用到
 
>索引设计原则：
>
1. 选择惟一性索引
2. 为经常需要排序，分组和联合操作的字段建立索引,为其建立索引可以有效地避免排序操作
3. 为常作为查询条件的字段建立索引
4. 限制索引的数目
5. 尽量使用数据量少的索引
6. 尽量使用前缀来索引
7. 删除不再使用或者很少使用的索引
 
###创建索引
####创建表时直接创建索引
```{bash}
	create table 表名 （属性名 数据类型[完整性约束]，
    属性名 数据类型[完整性约束条件],创建索引]
```
例16

1. 创建普通索引

		create table index1(
			id int,
			name varchar(20),
			sex boolean,
			index(id)
			);
2. 创建唯一性索引

		   create table index2(
			   id int unique,
			   name varchar(20),
			   unique index index2_id (id asc)
			   );

3. 创建全文索引

		   create table index3(
			   id int,
			   info varchar(20),
			   fulltext index index3_info(info)
			   )engine=myisam;

4. 创建单列索引  长度为10，只使用它的前10个字符来检索
   
		   create table index4(
			   id int,
			   subject varchar(30),
			   index index_st(subject(10))
			   );
5. 创建多列索引

		   create table index5(
			   id int,
			   name varchar(20),
			   sex char(4),
			   index index5 (name,sex)
			   );
 
6. 创建空间索引,要求相应的存储引擎
   
			create table index6(
			   id int,
			   space geometry not null,
			   spatial index index6_sp(space)
			   )engine=myisam;

####在存在的表上创建索引
```{bash}
   create [unique|fulltext|spatial] index 索引名 on 表名（属性名[(长度)][asc|desc]);
```

例17

1. 创建普通索引
		
		create index index7_id on user(name);
   
2. 创建唯一性索引
    
		create table index8(
		    id int,
		    name varchar(20)
		    );
	   
	    create unique index index8_id on index8(id);

3. 创建全文索引

	    create table index9(
		    id int,
		    info varchar(50)
		    )engine=myisam;
	   
	    create fulltext index index9_info on index9(info);

4. 创建单列索引
    
		create table index10(
		    id int,
		    address varchar(20)
		    );
	   
	    create index index10_addr on index10(address(4));

5. 多列索引
    
		create table index11(
		    id int,
		    name varchar(20),
		    address varchar(50)
		    );
	   
	   create index index11_na on index11(name,address);
   
6. 空间索引
    
		create table index12(
		    id int,
		    line geometry not null
		    )engine=myisam;
	   
	    create spatial index index12_line on index12(line);
   
####使用alter table 语句来创建索引
```{bash}
   alter table 表名 add [unique|fulltext|spatial] index
               索引名 （属性名[(长度)][asc|desc]）;
```
   
例18

1. 创建普通索引

	    alter table user add index index12_name(name(20));
2. 创建唯一性索引
	    
		create table index14(
		    id int,
		    name varchar(20)
		    );
	   
	   alter table index14 add unique index index14_id(id);
3. 创建全文索引
	   
		 create table index15(
		    id int,
		    info text
		    )engine=myisam;
	   
	    alter table index15 add fulltext index index15_info(info);
4. 创建单列索引
    
		create table index16(
		    id int,
		    address varchar(20)
		    );
	   
	    alter table index16 add index index16_addr(address(4));
5. 创建多索引
    
		 create table index17(
		     id int,
		     name varchar(20),
		     address varchar(50)
		     );
	 
	     alter table index17 add index index17(name,address);
6. 创建空间索引
   
		 create table index18(
		    id int,
		    line geometry not null
		    )engine=myisam;
	   
	    alter table index18 add spatial index index18_line(line);

###删除索引
```{bash}
 	drop index 索引名 on 表名;
```

例19：

	 drop index id on index1;


##视图
>视图是从一个或多个表中导出来的表，是一种虚拟存在的表。视图就像一个窗口，通过这个窗口可以看到系统专门提供的数据。这样，用户可以不用看到整个数据库表中的数据，而只是关心对自己有用的数据。视图可以使用户的操作更方便，而且可以保障数据库系统的安全性。是在原有的表或视图的基础上重新定义的虚拟表，这可以从原有的表上选取对用户有用的信息。 
>
视图作用：
>
1. 使操作简单化 
2. 增加数据的安全性 
3. 提高表的逻辑独立性

###创建视图
```{bash}
   create [algorithm={undefined|merge|temptable}]
      view 视图名 [(属性清单)]
      as select 语句
    [with[cascaded|local]check option];
```

1. undefined-->自动选择算法 
2. merge ----->将使用视图的语句与视图定义合并起来，使得视图定义的某一部分取代语句的对应部分;
3. temptable-->表示将视图的结果存入临时表，然后使用临时表执行语句
4. cascaded--->是可选参数,表示更新视图是要满足所有相关视图和表的条件，该参数为默认值
5. local------>表示更新视图时要满足该视图本身的定义的条件即可

例20：

1. 在单表上创建视图

	    create view department_view1
	    	as seleclt * from department;

	    create view department_view2(name,function,location)
	    	as select d_name,function,address from department;
 
2. 在多表上创建视图
   
		create algorithm=merge view worker_view1 	(name,department,sex,age,address)
		   as select name,department.d_name,sex,2009-birthday,address
		   from worker,department where worker.id_id = department.id_id
		   with local check option;

###查看视图
>要有查看视图的权限
方法有：
```{bash}
   	describe语句

	show table status语句

	show create view语句
```

例21：

查询information_schema数据库下的views表等

1. describe 视图名;  或 desc 视图名;
2. show table status like '视图名'
3. show create view 视图名  查看详细信息
4. 在view表中查看所有视图的详细信息`select * from information_schema.views` 

 
###修改视图
1. 使用create or replace view语句可以来修改视图,后面的和创建视图语句一样
```{bash}
   create or replace algorithm=temptable
   view department_view1(department,function,location)
   as select d_name,function,address from department;
```
2. alter语句来修改视图
```{bash}
   alter[algorithm={undefined|merge|temptable}]
   view 视图名[(属性清单)]
   as select语句
   [with [cascaded|local]] check option];
```

		   alter view department_view2(department,name,sex,location)
		   		as select d_name,worker.name,worker.sex,address
		   		from department,worker where department.d_id=work.d_id
		    	with check option;

###更新视图
>更新视图是指通过视图来插入，更新和删除表中的数据。因为视图是一个虚拟表，其中并没有数据。通过视图更新时，都是转换到基本表来更新。更新视图时，只能更新权限范围内的数据。超出了范围，就不能更新。
   
例22：

1. 先创建一个视图

	    create view department_view3(name,function,address)
	    as select d_name,function,address from departent where d_id=1001;
2. 查询视图
   
		select * from department_view3;
3. 更新视图
   
		update department_view3 set name='科研',function='新产品研发'，address='3号楼5层';

###不能更新的视图分类：
>并不是所有视图都可以更新 以下情况不能更新视图:

1. 视图中包含`sum()`,`count()`,`max()`,`min()`等函数 是不可以更新的
	  
		create view worker_view4(name,sex,totla)
		   as select name ,sex,count(name)from worker;
2. 视图中包含`union`,`union all`,`distinct`,`group by`,`having`等关键字
  
		 create view worke_view5(name,sex,address)
	   	 as select name,sex,homeaddress from worker group by d_id;
3. 常量视图
    
		create view worker_view6
	    	as select 'Aric' as name;
	因为视图中name字段是一人字符串常量“Aric”所以该视图是不能更新的
4. 视图中的select中包含子查询
   
		create view worker_view7(name)
	    as select (select name from worker);

5. 由不可更新的视图导出的视图
  
		create view worker_view8
	  	 as select * from worker_view7;
6. 创建视图时，algorithm 为temptable类型的视图也是不可更新的
7. 视图对应的表上存在没有默认值的列，而且该列没有包含在视图里。如表里包含name字段没有默认值，但是视图中不包括该字段。那么这个视图是不能更新的。因为更新视图时，这个没有默认值的记录将没有值插入，也没有null值插入。数据库系统是不会允许这种情况出现的
 
**注意：视图中虽然可以更新视图，但是有很多限制,一般用来查询，最好不要更新**

###删除视图
>删除视图，只是删除了视图的定义，不能删除数据
```{bash}
 drop view [if exists] 视图名列表 [restrict|cascade]
```
查看是否有权限删除视图:
```{bash}
  select drop_priv from mysql.user where user='root';
  drop view if exists worker_view1;
```

综合实例：

1. 创建work_infor表
  
		create table work_info(
		  id int(10) not null unique primary key,
		  name varchar(20) not null,
		  sex varchar(4) not null,
		  age int(5),
		  address varchar(50),
		  tel varchar(20)
		  );

2. 插入值

		insert into work_info values(1,'张三','M',18,'海淀','1234567');
		insert into work_info values(2,'李四','M',22,'昌平','2345678');
		insert into work_info values  (3,'王五','F',17,'永州','3456789');
		insert into work_info values (4,'赵六','F',25,'阜新','4567890');

3. 创建视图in_view. 从表中选出 age>20的记录来创建视图
  
		create algorithm=merge view
		  info_view(id,name,sex,address)
		  as select id,name,sex,address from work_info where age>20
		  with local check option;

4. 查询视图
		
		select * from info_view;
5. 修改视图 
	
		alter algorithm=merge view
	         info_view(id,name,sex,address)
	         as select id,name,sex,address from work_info where age<20
	         with local check option;
6. 更新视图 
		
		update info_view set sex='M' where id=3;
7. 删除视图 
	  
        drop view if exists info_view;

##触发器
>解发器（trigger）是由事件来触发某个操作。这些事件包括`insert`,`update`,`delete`。当数据库系统执行这些事件时，就会激活触发器执行相应的操作。
 
###创建触发器
1. 创建只有一个执行语句的触发器
	```{bash}
		create trigger 触发器名 before|after 触发事件
		    on 表名 for each row 执行语句
	```
	>for each row 表示每个记录都会触发
	
	例23：
	
	创建表
		   create table user(id int,name char(20))
	
		   create table trigger_time(
		   exec_time time);
	创建一个触发器：
	
		   create trigger job_trig1 before insert
		   on  user for each row 
			insert into trigger_time values(now());
	
	插入值：	
		insert into user values(1,'abc');

2. 创建有多个执行语句的触发器
```{bash}
     create trigger 触发器名 before|after
    	触发事件
    	on 表名 for each row
    	begin  
    	执行语句列表
    	end;
```
>执行语句列表，不同的的执行语句之间用分号隔开
>可以改变结束标志用delimiter &&

例24：

	    create trigger user_trig2 after delete
	    on user for each row
	    begin
	    insert into tirgger_time value('21:01:01');
	    insert into tirgger_time value('22:02:01');
	    end
	    &&
	    delimeter ;

###查看触发器

1. 使用sql语句查看
	```{bash}
		show triggers
	```
2. mySqL中查看trigger
>mysql中，所有触发器的定义都存在information_schema数据库下的的triggers表中。
```{bash}
	select * from information_schema.triggers;
    select * from information_schema.triggers where trigger_name='user_trig2';
```

###触发器的使用
>mysql中，触发器的执行顺序是before触发器、表操作（insert、update和delete）、after触发器

例24：

创建表：

	   create table triger_test(
	   id int primary key auto_increment,
	   info varchar(20)
	   );
 
创建两个触发器：

	   create trigger before_insert before insert
	   on user for each row
	   insert into triger_test value(null,'before');
	
	   create trigger after_insert  after insert
	   on user for each row
	   insert into triger_test value(null,'after');

执行sql语句：

		select * from triger_test;

###删除触发器
```{bash}
	drop trigger 名字
```

触发器综合实例：

1. 创建表

	  create table product(
	  id int(10) not null unique primary key,
	  name varchar(20) not null,
	  function varchar(20) not null,
	  address varchar(50)
	  );
	
	  create table operate(
	  op_id int(10) not null unique primary key auto_increment,
	  op_name varchar(20) not null,
	  op_time time not null
	  );

2. 创建触发器 
  
>创建插入触发器

	  create trigger product_bf_insert before insert
	  		on product for each row
	  		insert into operate values(null,'Insert product',now());
  
>创建更新触发器
  
		create trigger product_af_update after update
	  		on product for each row
	  		insert into operate values(null,'update product',now());
  
>创建删除触发器
  
		create trigger product_af_del after delete
	  		on product for each row
	  		insert into operate values(null,'delete product',now());

>执行sql操作，查询触发器内容

		insert into product values(1,'b','afa','afs');

		select * from operate;

		update product set address="aaaa";

		select * from operate;
 
		delete from product;

		select * from operate;

>删除触发器
  
		drop trigger product_bf_insert;
		drop trigger product_af_update;
		drop trigger product_af_del;

#数据操作语言DML
##数据查询

###查询语句的基本语法

```{bash}
   select 属性列表
   from 表名或视图列表
   [where 条件表达式1]
   [group by 属性名1 [having 条件表达式2]]
   [order by 属性名2 [asc|desc]]
```
>如果有where子句，就按“条件表达式1”指定的条件进行查询，如果没有where子句就查询所有记录,如果有`group by`子句，就按照 `属性名1` 指定的字段进行分组，如果`group by`子句后有`having`关键字，那么只有满足`条件表达式2`中指定的条件的才能够输出,`group by`子句通常和`count()`,`sum()`等聚合函数一起使用。
   
查询条件:

1. --比较  =，<,<=,>,>=,!=,<>,!>,!<,
2. --指定范围 between and,not between and
3. --指定集合 in,not in
4. --匹配字符 like,not like
5. --是否为空值 is null,is not null
6. --多个查询条件 and ,or

###在单表上查询数据

1. --查询所有字段  列出表的所有字段 使用*
2. --查询指定字段 
 
			select name,sex,homeaddress from emploee where d_id=1001;
3. --in关键字查询
	in关键字可以判断某个字段的值是否在指定的集合中，如果字段的值在集合中，则满足查询条件，该记录将被查询出来。如果不在集合中，则不满足查询条件。
	
	```{bash}
	[not] in(元素1，元素2.....,元素n)
	```
	例25：
		select * from emploee where d_id in(1001,1004);

4. --between and范围查询
	```{bash}
	[not] between 值1 and 值2
	```
	例26：
	
		select * from emploee where id beteewn 15 and 25;
		select * from emploee where id not beteewn 15 and 25;

5. --带like的字符串匹配查询
	```{bash}
	[not] like '字符串'
	```
	>通配符%，代表任意的,   _ 表示一个字符
	
	例27：
	
		select * from emploee where name like '张%';

6. --查询空值
	```{bash}
	is [not] null
	```
	例28： 查询work表中 info字段为空值的记录
	
		select * from work where info is null;

7. --and多条件查询
	```{bash}
	select * from emploee where d_id<100 and age<26 and sex='男'
	select * from emploee where num in(1,2,3) and age between 15 and 25 and homeaddr like "%北京%";
	```

8. --or 关键字多条件查询
	```{bash}
	select * from emploee where
	num in(1,2,3) and age=25
	or sex='女';
	```
9. --查询结果不重复

	>使用distinct使用d_id字段不重复
	```{bash}
	select distinct d_id from employee;
	```
10. --查询结果排序
    >order by 属性名[asc|desc]

    ```{bash}
	  //先用d_di升序排列，如果出现相同的再按age 降序排列  
	  select * from employee order by d_id asc,age desc;
	```
11. --分组查询
	>group by 关键字可以将查询结果按某个字段或多个字段分组。字段中值相等的为一组。语法规则如下：
	>
	```{bash}
	group by 属性名[having 条件表达式][with rollup]
	```
    1. 单独使用group by关键字来分组
    ```{bash}
     select * from employee group by sex;  //只显示每一组的第一条记录
    ```
    2. group by关键字与group_concat()函数一起使用
    ```{bash}
     select sex,group_concat(name) from employee group by sex;
     ```
		>把name字段的所有值都显示出来
   
    3. group by关键字与集合函数一起使用
    ```{bash}
     select sex,count(sex) from employee group by sex;//记录各多少人
	```
   4. group by关键字与having一起使用
    ```{bash}
      select sex,count(sex) from employee
       group by sex having count(sex)>=3
	```
    	>只有count(sex)>=3的分组才能显示出来

   5. 按多个字段进行分组
    ```{bash}
     select * from employee group by d_id,sex;
    ```
		>同时使用两个字段来分组,首先用d_id进行分组，如果相同则再按sex进行分组
     
   6. group by关键与with rollup 一起使用
    ```{bash} 
	select sex,count(sex) from employ ee
     group by sex with rollup;
	```
		>会对记录进行求和
   
		     select sex,group_concat(name) from employee
		     group by sex with rollup;

    7. --用limit限制查询结果的数量
    ```{bash}
	   select * from employee limit 2;限查2条 从第一条开始显示
	   select * from employee limit 1,2;从第2条开始，只显示2条 查询时0表示第一条
    ```
   
###使用聚合函数查询数据
>聚合函数包括`count()`,`sum()`,`avg()`,`max()`,`min()`.

1. count(): 
    ```{bash}
	select count(*)  from employee;
	```
	先分组，再查出每个分组中的记录数
		
		select id_id,count(*) from emplyee group by d_di;
2. sum():
	```{bash}
	select num,sum(score) from grade where num=1001;
	```
	sum()经常与group by一起使用
		
         	select num,sum(score) from grade group by num;
3. avg():  
	```{bash}
	select avg(age) from employee;
	select  course,avg(scroe) from grade group by course;
	```
4. max(): 
	```{bash}
	select max(age) from employee;
	```
	分组之后查询每组最大值
		
			select num,course,max(scroe) from grade  group by coure;
   
5. min()：
	```{bash}
		select min(age) from employee;
        select num,course,min(scroe) from grade group by coure;
	```


###多表上联合查询
---
####内连接查询
```{bash}
 select num,name,employee.d_di,age,sex,d_name,function
   from employee,department
   where employee.d_id = department.d_id;
```
####外连接查询
```{bash}
    select 属性名列表
   		from 表名1 left|right join  表名2
     	on 表名1.属性名1=表名2.属性名2；
```
1. 左连接查询  查询前面表的所有记录，没的用空值补充,保留前面表记录加中符合条件右面表记录
	```{bash}
	select num,name,employee,d_id,age,sex,d_name,function
		from employee left join department
		on employee.d_id=department.d_id;
	```
2. 右连接查询
	```{bash}
	select num,name,employee,d_id,age,sex,d_name,function
		from employee right join department
		on employee.d_id = department.d_id;
	```
####复合条件查询
```{bash}
select num,name,employee.d_di,age,sex,d_name,function
from employee,department
where employee.id_id= department.d_id
and age>24;
```
###子查询
>内层查询语句的查询结果可以为外层查询提供查询条件

1. --in的子查询
	```{bash}
	     select　* from employee
	        where d_id in
	         (select d_id from department)
	```
2. --比较运算符的查询

	例29： 查询出谁获得了一等奖学金
	
	     select id,name,score from computer_stu
	        where score>=
	        (select score from scholarship
	          where level=1);
     
	例30： 查询那些部门没有年龄是24的员工
	
	     select d_id,d_name from employee
	     	where d_id !=
	     	(select d_di from employee
	      	where age=24);
   
3. exists关键字的字查询，返回直假值，如果返回真，就执行外层查询，如果为假就不查询没结果

	例31 查询开发部id为1003的员工名字存在不存在，如果存在则查出所有雇员信息

	     select * from employee
	       where exists
	       (select d_name from department where d_id=1003);
	   
	     select * from employee
	        where age>24 and exists
	        (select d_name from department where d_id=1003);

4. any 关键字的子查询,满足内层查询语句返回的结果中的任何一个，就可通过该条件来执行外层查询语句

     例32： 查询谁获得奖学金，只要满足一条就可以

        select * from computer_stu
           where score>=any
       (select score from scholarship);
5. all 关键字子查询

     例33： 查询一等奖学金的获得者
        
		select *　from computer_stu
         where score>=all
         (select score from scholarship)

6. 合并查询结果，是将多个select语句的查询结果合并到一起。
	```{bash}
	select 语句1
	union|union all
	select 语句2
	union|union all
	select 语句n
	```
	union表示会去掉重复的，union all则不会去掉重复的

7. 为表和字段取别名
   
   >为表取别名
    ```{bash}
	表名 别名
	```

例34：

	select * from department d
	       where d.d_id=1001;
   
   >为字段取别名
    ```{bash}
	属性名 [as] 别名
	```

例35：

	select d_id as department_id d_name as department_name from department;
   
###使用正则表达式查询
```{bash}
    属性名 regexp '匹配方式'
```
>正则表达式：
>
		^          匹配字符串开始的部分
		$          匹配字符串结束的部分
		.          代表字符串中的任意一个字符，包括回车和换行
		[字符集合]  匹配‘字符集合’中的任何一个字符
		[^字符集合] 匹配除了‘字符集合’中的任何一个字符
		s1|s2|s3   匹配s1,s2,s3中的任意一个字符串
		*          代表多个该符号之前的字符 ，饭0个和1个
		+          代表多个该符号之前的字符   1个以上
		字符串{n}   字符串出现N次
		字符串｛M,N｝ 字符串出现至少n次，最多m次
 
例36： 从info 表name字段中查询以L开头的记录
   
	select * from info where name regexp '^L';
例37： 以字符串aaa开头的
   	
	select * from info where name regexp '^aaa'
例38：以字符c结尾的
   
	select * from info where name regexp 'c$'
例39：以字符串aaa结尾的
   
	select * from info where name regexp 'aaa$'

例40：以a开头以c结尾，中间为任意两个字符的记录

	select * from info where name regexp '^a..c$'

例41：只要查询结果包括了a,b,c 任意一个就可以查询出来
   
	select * from info where name regexp '[abc]';
例42： 包括0-9中任何一个
   
	select * from info where name regexp '[0-9]'

例43： 包含0-9任一字线，abc任一个
   
	select * from info where name regexp '[0-9abc]'

例44： 匹配包含 不在 a到w ，0-9外的任意一个字符的集合

	select * from info where name regexp ‘[^a-w0-9]’
例45： 匹配包含指定字符串
   
	select * from info where name regexp 'ic'
例46： 匹配任何一个
   
	select * from info where name regexp 'id|ab|uc'

例47： a在c前面至少出现一次

	select * from info where name regexp 'a+c'

例48： a连续出现3次

	select * from info where name regexp 'a{3}'
例49：ab最少一次，最多3次
   
	select * from info where name regexp 'ab{1,3}'

###查询综合实例

1. 准备数据

		create table  student(
		id int(10) not null unique primary key,
		name varchar(20) not null,
		sex varchar(4),
		birth year,
		department varchar(20),
		address varchar(50)
		);
		
		create table score(
		id int(10)not null unique primary key auto_increment,
		stu_id int(10) not null,
		c_name varchar(20),
		grade int(10)
		);
		
		insert into student values(901,'张老大','男',1985,'计算机系','海淀区');
		insert into student values(902,"张老二","男",1986,"中文系","昌平区");
		insert into student values(903,"张三","女",1990,"中文系","永州");
		insert into student values(904,"李四","男",1990,"英语机系","阜新");
		insert into student values(905,"王五","女",1991,"英语机系","厦门");
		insert into student values(906,"王六","男",1988,"计算机系","衡阳");
		
		insert into score values(null,901,'计算机',98);
		insert into score values(null,901,'英语',80);
		insert into score values(null,902,'计算机',65);
		insert into score values(null,902,'中文',88);
		insert into score values(null,903,'中文',95);
		insert into score values(null,904,'计算机',70);
		insert into score values(null,904,'英语',92);
		insert into score values(null,905,'英语',92);
		insert into score values(null,906,'计算机',90);

2. 查询学生表中从index 1开始的3条数据（第一条记录index为0）

		select * from student limit 1,3;
 
3. 查询计算机系和英语系的学生信息

		select * from student
		  where department in('计算机机系','英语系');
	 
		select * from student
		  where department ='计算机机系' or '英语系';
 
4. 查询年龄在18-20间的学生

		select id,name,sex,2009-birth as age,department,address
		from student
		where 2009-birth between 18 and 20;
 
5. 查询各系有多少人

		select department,count(id)
		   from student group by department;

6. 查询每个科目的最高分 先分组再查最高分

		select c_name,max(grade)
		   from score group by c_name;
 
7. 查询李四的成绩
  
		select c_name,grade
			from score where stu_id =
	 		(select id from student
			where name="李四");

8. 查询所有学生的信息

		select student.id,name,sex,birth,department,address,c_name,grade
			from student,score
			where student.id=score.stu_id;

9. 计算每一个学生的总成绩

		  select stu_id,sum(grade)
		   from score group by stu_id;

		  select student.id,name,sum(grade)
		    from student,score
		    where student.id = score.stu_id
		   group by student.id;

10. 求每一科的平均成绩
   
		select c_name,avg(grade)
			from score group by c_name;

11. 查询计算机成绩低于95的学生信息

		select * from student
			where id in
			(select stu_id from score
				where c_name='计算机系'and grade<95);

12. 查询同时参加计算机和英语考试的学生的信息。先从score表中查出谁同时参加了两科取出学号，再在student里查询

		select * from student
		where id= any
			(select stu_id from score
				where stu_id in(
					select stu_id from
					score where c_name='计算机')
					and c_name='英语');

13. 将计算机成绩从高到低排序
   
		select stu_id,grade
			from score
				where c_name='计算机'
					order by grade desc;

14. 查询两个表中的所有学号
   
		select id from student
		union
		select stu_id from score;

15. 查询姓张或姓王的同学的姓名，院系，考试科目和成绩

		select student.id,name,sex,birth,department,address,c_name,grade
		from student,score
		where
			(name like '张%' or name like '王%')
		and
			student.id=score.stu_id;

##插入数据
>可以为表的所有字段插入，也可以插入指定字段

```{bash}
insert into 表名 values(值列表)
insert into 表名 字段列表 values(值列表）
```

>同时插入多条记录

```{bash}
insert into 表名 （字段列表）values(值列表)，（值列表）
```
>将查询结果插入到表中

```{bash}
insert into 表名1（属性列表1） select 属性列表2 from 表名2 where 条表达式;
```
##更新数据
```{bash}
	update 表名
		set 属性名1=取值1，属性名2=聚会2
		...
		属性名n=取值n
		where 条件表达式;
```
满足条件表达式的记录才会更新

##存储过程和函数
>存储过程和函数是在数据库中定义一些sql语句的集合，然后直接调用这些存储过程和函数来执行
已经定义好的sql语句。存储过程和函数可以避免开发人员重复的编写相同的sql语句。而且存储过程和函数是在mysql服务器中存储和执行的，可以减少客户端和服务器端的数据传输。

###创建存储过程  
```{bash}
	create procedure sp_name
	  ([proc_parameter[,..]])
	     [characteristic...]routine_body
	      [in|out|inout]param_name type
```

下面创建一个名为num_from_emplyee存储过程:

	  delimiter&&
	   create procedure num_from_employee(in emp_id int,out count_num int)//参数输入与输出类型
	          reads sql data
	          BEGIN
	             select count(*) into count_num
	             from employee
	             where d_id=emp_id;
	          END
	          &&
	    delimiter;
 
###创建存储函数
```{bash}
	create function sp_name([func_parameter[,...]])
	    retruns type
	    [characteristic..]routine_body
```
创建一个名为name_from_employee的存储函数
 
	  delimiter &&
	  create function name_from_employee(emp_id int)
	         returns varchar(20)
	         BEGIN
	            return (select name
	              from employee
	              where num=emp_id);
	         END
	         &&
	   delimiter;
 
 
###变量的使用  
>存储过程和函数可以定义变量和使用变量。用户可以使用`declare`关键字来定义
变量。然后可以为变量赋值。这些变量的作用范围是`begin...end`程序段中。

下面定义变量my_sql，数据类型为int型，默认值为10
```{bash}
declare my_sql int defaut 10;
```
为变量赋值
```{bash}
  set my_sql=30;
```

下面从employee表中查询id为2的记录，将该记录的d_id值赋给变量my_sql
	   
	select d_id into my_sql
		from employee where id=2;
 

###定义条件和处理程序
>是事先定义程序执行过程中可能遇到的问题。并且可以在处理程序中定义解决这些问题的办法。这种方式可以提前预测可能出现的问题，并提出解决办法。mysql中都是通过declare关键字来定义条件和处理程序。

定义条件:

	declare condion_name condition for condition_value
	condition_value;

条件值可以用：sqlstate[value]sqlstate_value|mysql_error_code

下面定义Error 1146(42s02)这个错误，名称为can_not_find,可以用两种不同的
方式定义：

	  1.declare can_not_find condition for sqlstate '42s02';
	  2.declare can_not_find condition for 1146;

定义处理程序：

	  declare handler_type handler for condition_value[,..]sp_stateement
	  handler_type
	     continue|exit|undo
	  condition_value:
	     sqlstate[value]sqlstate_value|condition_name|sqlwarnin|notfound|
	     sqlexception|mysql_error_code

下面定义处理程序的几种方式代码如下
1. 捕获sqlstate_value

	     declare continue handler for sqlstate '42s02' set @info='can not find';
2. 捕获mysql_error_code

	declare continue handler for 1146 set @info='can not find';
3. 先定义条件，然后调用
     
	declare can_not_find condition for 1146;
     declare continue handler for can_not_find set @info='can not find';
4. 使用sqlwarning
    
	declare exit handler for sqlwarning set @info='error';
5. 使用not found
    
	declare exit handler for notfound set @info='can not find';
6. 使用sqlexception
    
	declare exit handler for sqlexception set @info='can not find'

###光标的使用  
>查询语句可能查询出多条记录，在存储过程和存储函数中使用光标来逐条读取查询结果集中的记录。有些书将光标称为游标。光标的使用声明在处理程序之前，并且声明在变量和条件之后。  

1. 声明光标
  
	declare cur_employee CURSOR for select name,age from employee;
2. 打开光标
  
	open cur_employee;
3. 使用光标
  
	fetch cur_employee into emp_name,emp_age;
4. 关闭光标
  
	close cur_employee;

###流程控制的使用
>存储过程和存储函数中可以使用流程控制来控制语句的执行。mysql中可以使用if语句，case语句，loop语句，leave语句，aterate语句，repeat语句，while语句来进行流程控制。

1. if语句
	```{bash}
		if search_condition then statement_list
		    [elseif search_condition then statement_list]...
		    [else statement_list]
		end if
	```
	示例:
		 if age>20 set @count1=@count1+1;
	       elseif age=20 then @count2=@count2+1;
	       else @count3=@count3+1

2. case语句
	```{bash}
	  case case_value
	       where when_value then statement_list;
	       [when when_value then statement_list]...
	       [else statement_list]
	  end case;
	```
	示例 case age
	
	          when 20 then set @count1=@count1+1;
	          else set @count2=@count2+1;
	       end case;
3. loop
	```{bash}
	[begin_label]loop
	       statement_list
	   end loop [end_label]
	```
	示例
	
		  add_num:loop
	         set @count=@count+1;
	          end loop add_num;
4. leave语句  跳出循环
	```{bash}
	     add_num:loop
	      set @count=@count+1;
	      if @count=100 then
	          leave add_num;
	     end loop add_num;
	```
5. iterate语句 跳出本次循环
	```{bash}
	  add_num:loop
	    set @count=@count+1;
	    if @count=100 then
	       leave add_num;
	   else if mod(@count,3)=0 then
	      iterate add_num;
	   select * from employee;
	  end loop add_num;
	```
6. repeat
	```{bash}
	  [begin_label]repeat
	     statement_list
	    until search_condition
	  end repeat [end_label]
	```
	示例:
	
		 repeat set @count=@count+1;
	        until @count=100;
	       end repeat;
7. while语句
	```{bash}
	   [begin_label]while serch_condition do
	           statement_list
	    end while
	```
	示例:
		
		 while @conte<100 do
	       set @count=@count+1
	       end while;

###调用存储过程和函数

1. 调用存储过程后，数据库将执行存储过程中的语句。然后将结果返回给输出值
 
	  call sp_name([parameter[,...]]);  
	  call num_from_employee(1002,@n);
	  select @n;
 
2. 调用存储函数

    select name_from_employee(3);

###查看存储过程和函数
>用户可以通过show status语句来查看存储过程和函数的状态。也可通过show create 语句来查看存储过程和函数的定义。用户也可通过查询information_schema数据库下的routines表来查看存储过程和函数的信息。
```{bash}
  show {procedure|function} status[like 'pattern'];
```
后面like ‘pattern’参数用来匹配存储过程和函数的名称

	show procedure status like 'num_from_employee'\G
	
	show create {procedure|function}sp_name;
	 
	show create procedure num_from_employee;


###修改存储过程和函数
alter procedure语句来修改存储过程，通过alter function语句来修改存储函数
```{bash}
	alter {procedure|function}sp_name [characteristic...]
	     characteristic:
	    { contains sql | no sql | reads sql data | modifies sql data }
	     | sql security{definer| invoker}
	     | comment 'string'
```

下面修改存储过程num_from_employee的定义，将读写权限改为modifiel sql data并指明调用者可以执行。

	  alter procedure num_from_employee
	       modifies sql data
	       sql security invoker;


下面修改存储函数name_from_employee的定义。将读写权限必为reads sql data并加上注释信息‘find name’

	  alter function name_from_employ
	      reads sql data
	      comment 'find name';


###删除存储过程和函数
```{bash}
drop {procedure|function} fp_name;
```

##删除数据
```{bash}
	delete from 表名[where 条件表达式];
```

联表删除: 可以通过外键查询到所在表，然后删除
#数据控制语言DCL  
