title: SQL综合整理
date: 2012/10/18 13:28:14
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
>
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
   要有查看视图的权限
   方法有
   describe语句，show table status语句， show create view语句
   和查询information_schema数据库下的views表等
   1》》describe 视图名;  或 desc 视图名;
   2》》show table status like '视图名'
   3》》show create view 视图名  查看详细信息
   4》》在view表中查看所有视图的详细信息
      select * from information_schema.views \G

 
 4》修改视图
  1>使用create or replace view语句可以来修改视图,后面的和创建视力语句一样
   
   create or replace algorithm=temptable
   view department_view1(department,function,location)
   as select d_name,function,address from department;

  2>alter语句来修改视图
   alter[algorithm={undefined|merge|temptable}]
   view 视图名[(属性清单)]
   as select语句
   [with [cascaded|local]] check option];
 
   alter view department_view2(department,name,sex,location)
   as select d_name,worker.name,worker.sex,address
   from department,worker where department.d_id=work.d_id
    with check option;
  5》更新视图
    更新视图是指通过视图来插入，更新和删除表中的数据。因为视图是一个虚拟表，其中
    不有数据。通过视图更新时，都是转换到基本表来更新。更新视图时，只能更新权限范围内的数据。超出了范围，就不能更新。
   
先创建一个视图
    create view department_view3(name,function,address)
    as select d_name,function,address from departent where d_id=1001;
查询视图
   select * from department_view3;
更新视图
   update department_view3 set name='科研',function='新产品研发'，address='3号楼5    层';
——————————————————————————
  并不是所有视图都可以更新 以下情况不能更新视图
 1--视图中包含sum(),count(),max(),min()等函数 是不可以更新的
  create view worker_view4(name,sex,totla)
   as select name ,sex,count(name)from worker;
 2--视图中包含union,union all,distinct.group by,having等关键字
   create view worke_view5(name,sex,address)
    as select name,sex,homeaddress from worker group by d_id;
 3--常量视图
    create view worker_view6
    as select 'Aric' as name;
  因为视图中name字段是一人字符串常量“Aric”所以该视图是不能更新的
 4--视图中的select中包含子查询
   create view worker_view7(name)
    as select (select name from worker);
 5--由不可更新的视图导出的视图
  create view worker_view8
   as select * from worker_view7;
 6--创建视图时，algorithm 为temptable类型的视图也是不可更新的
 7--视图对应的表上存在没有默认值的列，而且该列没有包含在视图里。如表里包含name字段没有默认值，但是视图中不包括该字段。那么这个视图是不能更新的。因为更新视图时，这个没有默认值的记录将没有值插入，也没有null值插入。数据库系统是不会允许这种情况出现的
 
注意：视图中虽然可以更新视图，但是有很多限制,一般用来查询，最好不要更新
  6》删除视图
   删除视图，只能删除视图的定义，不能删除数据
   drop view [if exists] 视图名列表 [restrict|cascade]
 
  查看是否有权限删除视图
  select drop_priv from mysql.user where user='root';
 
  drop view if exists worker_view1;
#数据操作语言DML

#数据控制语言DCL  




  

 ************************************实例*************************
  创建work_infor表
  create table work_info(
  id int(10) not null unique primary key,
  name varchar(20) not null,
  sex varchar(4) not null,
  age int(5),
  address varchar(50),
  tel varchar(20)
  );

插入值
insert into work_info values(1,'张三','M',18,'海淀','1234567');
insert into work_info values(2,'李四','M',22,'昌平','2345678');
insert into work_info values  (3,'王五','F',17,'永州','3456789');
insert into work_info values (4,'赵六','F',25,'阜新','4567890');

创建视图in_view. 从表中选出 age>20的记录来创建视图
  create algorithm=merge view
  info_view(id,name,sex,address)
  as select id,name,sex,address from work_info where age>20
  with local check option;

查询视图 select * from info_view;
修改视图 alter algorithm=merge view
         info_view(id,name,sex,address)
         as select id,name,sex,address from work_info where age<20
         with local check option;
更新视图 update info_view set sex='M' where id=3;
删除视图 drop view if exists info_view;

_________________________________________________________
19.触发器
解发器（trigger）是由事件来触发某个操作。这些事件包括insert,update,delete。当数据库系统执行这些事件时，就会激活触发器执行相应的操作。
 
1》创建触发器
   1--创建只一个执行语句的触发器
    create trigger 触发器名 before|after 触发事件
    on 表名 for each row 执行语句
   
   for each row 表示每个记录都会触发
   创建一个表
   create table trigger_time(
   exec_time time);
   
   create trigger job_trig1 before insert
   on  user for each row
   insert into trigger_time values(now());

   insert into user values(1,'abc','123','987654321');

   
  2--创建有多个执行语句的触发器
     create trigger 触发器名 before|after
    触发事件
    on 表名 for each row
    begin  
    执行语句列表
    end;
    执行语句列表，不同的的执行语句之间用分号隔开
  改变结束标志  
  delimiter &&
   
    create trigger user_trig2 after delete
    on user for each row
    begin
    insert into tirgger_time value('21:01:01');
    insert into tirgger_time value('22:02:01');
    end
    &&

    delimeter ;
2>>查看触发器
   1--
     show triggers \G
   2--
     mysql中，所有触发器的定义都存在information_schema数据库下的的triggers表中。
    select * from information_schema.triggers;
    select * from information_schema.triggers where trigger_name='user_trig2'\G;


3》触发器的使用
  mysql中，触发器的执行顺序是before触发器、表操作（insert.update和delete）、after触发器
 
   create table triger_test(
   id int primary key auto_increment,
   info varchar(20)
   );
 
 
   create trigger before_insert before insert
   on user for each row
   insert into triger_test value(null,'before');

   create trigger after_insert  after insert
   on user for each row
   insert into triger_test value(null,'after');

   select * from triger_test;

4》删除触发器
  drop trigger 名字
**************************************实例
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
  创建插入触发器
  create trigger product_bf_insert before insert
  on product for each row
  insert into operate values(null,'Insert product',now());
  创建更新触发器
  create trigger product_af_update after update
  on product for each row
  insert into operate values(null,'update product',now());
  创建删除触发器
  create trigger product_af_del after delete
  on product for each row
  insert into operate values(null,'delete product',now());


  insert into product values(1,'b','afa','afs');

  select * from operate;

  update product set address="aaaa";

select * from operate;
 
  delete from product;
 
  select * from operate;

  drop trigger product_bf_insert;
  drop trigger product_af_update;
  drop trigger product_af_del;

1.mysql查询数据
************************************************************
1.查询语句的基本语法
   select 属性列表
   from 表名和视图列表
   [where 条件表达式1]
   [group by 属性名1 [having 条件表达式2]]
   [order by 属性名2 [asc|desc]]
   
    如果有where子句，就按“条件表达式1”指定的条件进行查询，如果没有where子句就查询所有记录
    如果有group by子句，就按照 “属性名1“指定的字段进行分组，如果group by子句后有having关键字，那么只有满足‘条件表达式2’中指定的条件的才能够输出group by子句通常和count(),sum()等聚合函数一起使用。
   
  查询条件
  1--比较  =，<,<=,>,>=,!=,<>,!>,!<,
  2--指定范围 between and,not between and
  3--指定集合 in,not in
  4--匹配字符 like,not like
  5--是否为空值 is null,is not null
  6--多个查询条件 and ,or
2.在单表上查询数据
 1--查询所有字段  列出表的所有字段 使用*
 2--查询指定字段  select name,sex,homeaddress from emploee where d_id=1001;
 3--in关键字查询
   in关键字可以判断某个字段的值是否在指定的集合中，如果字段的值在集合中，则满足查询条件，该记录将被查询出来。如果不在集合中，则不满足查询条件。
   [not] in(元素1，元素2.....,元素n)
   
   
   select * from emploee where d_id in(1001,1004);
 4--between and范围查询
  [not] between 值1 and 值2
  select * from emploee where id beteewn 15 and 25;
  select * from emploee where id not beteewn 15 and 25;
 5--带like的字符串匹配查询
   [not] like '字符串'
   
   通配符%，代表任意的
         _, 表示一个字符
 
  select * from emploee where name like '张%';
 6--查询空值
  is [not] null
 
  查询work表中 info字段为空值的记录
  select * from work where info is null;
 7--and多条件查询
  select * from emploee where d_id<100 and age<26 and sex='男'
  select * from emploee where num in(1,2,3) and age between 15 and 25 and homeaddr like "%北京%";
 8--or 关键字多条件查询
 
  or和and共用
  select * from emploee where
    num in(1,2,3) and age=25
    or sex='女';
 9--查询结果不重复
   使用distinct使用d_id字段不重复
   select distinct d_id from employee;
 10--查询结果排序
   order by 属性名[asc|desc]
  //先用d_di升序排列，如果出现相同的再按age 降序排列  
  select * from employee order by d_id asc,age desc;
 11--分组查询
   group by 关键字可以将查询结果按某个字段或多个字段分组。字段中值相等的为一组。语法规则如下：group by 属性名[having 条件表达式][with rollup]
   1&&单独使用group by关键字来分组
     select * from employee group by sex;  //只显示每一组的第一条记录
     
   2&&group by关键字与group_concat()函数一起使用
     select sex,group_concat(name) from employee group by sex;
     把name字段的所有值都显示出来
   
   3&&group by关键字与集合函数一起使用
     select sex,count(sex) from employee group by sex;//记录各多少人

   4&&group by关键字与having一起使用
      select sex,count(sex) from employee
       group by sex having count(sex)>=3
    只有count(sex)>=3的分组才能显示出来

   5**按多个字段进行分组
     select * from employee group by d_id,sex;
     同时使用两个字段来分组
     首先用d_id进行分组，如果相同则再按sex进行分组
     
   6**group by关键与with rollup 一起使用
     select sex,count(sex) from employ ee
     group by sex with rollup;
     会对记录进行求和
   
     select sex,group_concat(name) from employee
     group by sex with rollup;

  12--用limit限制查询结果的数量
   
   select * from employee limit 2;限查2条 从第一条开始显示
   select * from employee limit 1,2;从第2条开始，只显示2条 查询时0表示第一条
   
   
3.使用聚合函数查询数据
  包括count(),sum(),avg(),max(),min().
   
   count(): select count(*)  from employee;
          select id_id,count(*) from emplyee group by d_di;
      先分组，再查出每个分组中的记录数
 
   sum():  select num,sum(score) from grade where num=1001;
        select num,sum(score) from grade group by num;
      sum()经常与group by一起使用
 
   avg():  select avg(age) from employee;
      select  course,avg(scroe) from grade group by course;

  max(): select max(age) from employee;
       select num,course,max(scroe) from grade  group by coure;分组之后查询每组最大值
   
    min()：select min(age) from employee;
       select num,course,min(scroe) from grade group by coure;



4.多表上联合查询
    1--内连接查询
     select num,name,employee.d_di,age,sex,d_name,function
       from employee,department
       where employee.d_id = department.d_id;
   
    2--外连接
    select 属性名列表
   from 表名1 left|right join  表名2
     on 表名1.属性名1=表名2.属性名2；

   1>>左连接查询  查询前面表的所有记录，没的用空值补充
      select num,name,employee,d_id,age,sex,d_name,function
      from employee left join department
      on employee.d_id=department.d_id;
   2>>右连接查询
      select num,name,employee,d_id,age,sex,d_name,function
      from employee right join department
      on employee.d_id = department.d_id;
   3>>复合条件查询
       内连接查询
      select num,name,employee.d_di,age,sex,d_name,function
       from employee,department
      where employee.id_id= department.d_id
        and age>24;
   


5.子查询

   内层查询语句的查询结果可以为外层查询提供查询条件
   1--in的子查询
     select　* from employee
        where d_id in
         (select d_id from department)
   2--比较运算符的查询
     
     查询出谁获得了一等奖学金
     select id,name,score from computer_stu
        where score>=
        (select score from scholarship
          where level=1);
     
     查询那些部门没有年龄是24的员工
     select d_id,d_name from employee
     where d_id !=
     (select d_di from employee
      where age=24);
   
    3—exists关键字的字查询   返回直假值，如果返回真，就执行外层查询，如果为假就不查询没结果
     
     select * from employee
       where exists
       (select d_name from department where d_id=1003);
   
     select * from employee
        where age>24 and exists
        (select d_name from department where d_id=1003);

     not exists相返回      不存在返回真，存在返回假

     4-- any 关键字的子查询
       满足内层查询语句返回的结果中的任何一个，就可通过该条件来执行外层查询语句
         
         查询谁获得奖学金  ，只要满足一条就可以
        select * from computer_stu
           where score>=any
       　　(select score from scholarship);
     5——all 关键字子查询
       
         查询一等奖学金的获得者
        select *　from computer_stu
         where score>=all
         (select score from scholarship)


6.合并查询结果
 
     是将多个select语句的查询结果合并到一起。
   
     select 语句1
     union|union all
     select 语句2
     union|union all
     select 语句n
 
     union表示会去掉重复的，union all则不会去掉重复的

     
7.为表和字段取别名
   
   为表取别名
   select * from department d
        where d.d_id=1001;
   
   为字段取别名
   属性名 [as] 别名
   select d_id as department_id d_name as department_name from department;
   
8.使用正则表达式查询
   
    属性名 regexp '匹配方式'
   
   正则表达式：
   ^ 匹配字符串开始的部分
   $ 匹配字符串结束的部分
   . 代表字符串中的任意一个字符，包括回车和换行
   [字符集合]  匹配‘字符集合’中的任何一个字符
   [^字符集合] 匹配除了‘字符集合’中的任何一个字符
   s1|s2|s3    匹配s1,s2,s3中的任意一个字符串
   * 代表多个该符号之前的字符 ，饭0个和1个
   + 代表多个该符号之前的字符   1个以上
   字符串{n} 字符串出现N次
   字符串｛M,N｝ 字符串出现至少n次，最多m次
 
   从info 表name字段中查询以L开头的记录
   select * from info where name regexp '^L';
   以字符串aaa开头的
   select * from info where name regexp '^aaa'
   以字符c结尾的
   select * from info where name regexp 'c$'
   以字符串aaa结尾的
   select * from info where name regexp 'aaa$'
   以a开头以c结尾，中间为任意两个字符的记录
   select * from info where name regexp '^a..c$'
   只要查询结果包括了a,b,c 任意一个就可以查询出来
   select * from info where name regexp '[abc]';
   包括0-9中任何一个
   select * from info where name regexp '[0-9]'
   包含0-9任一字线，abc任一个
   select * from info where name regexp '[0-9abc]'
   匹配包含 不在 a到w ，0-9外的任意一个字符的集合
   select * from info where name regexp ‘[^a-w0-9]’
   匹配包含指定字符串
   select * from info where name regexp 'ic'
   匹配任何一个
   select * from info where name regexp 'id|ab|uc'
   a在c前面至少出现一次
   select * from info where name regexp 'a+c'
   a连续出现3次
   select * from info where name regexp 'a{3}'
   ab最少一次，最多3次
   select * from info where name regexp 'ab{1,3}'

 **************************************实例*************
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

select * from student limit 1,3;
 查询计算机系和英语系的学生信息
 
 select * from student
  where department in('计算机机系','英语系');
 
 select * from student
  where department ='计算机机系' or '英语系';
 
  查询年龄在18-20间的学生
  select id,name,sex,2009-birth as age,department,address
    from student
   where 2009-birth between 18 and 20;
 
  各系有多少人
  select department,count(id)
   from student group by department;

  查询每个科目的最高分 先分组再查最高分
  select c_name,max(grade)
   from score group by c_name;
 
 查询李四的成绩
  select c_name,grade
   from score where stu_id =
   (select id from student
    where name="李四");

  查询所有学生的信息
  select student.id,name,sex,birth,department,address,c_name,grade
   from student,score
   where student.id=score.stu_id;

  计算每一个学生的总成绩
  select stu_id,sum(grade)
   from score group by stu_id;

  select student.id,name,sum(grade)
    from student,score
    where student.id = score.stu_id
   group by student.id;

  求每一科的平均成绩
   select c_name,avg(grade)
    from score group by c_name;

  查询计算机成绩低于95的学生信息
  select * from student
   where id in
   (select stu_id from score
    where c_name='计算机系'and grade<95);

  查询同时参加计算机和英语考试的学生的信息。先从score表中谁同时参加了两科
  取出学号，再在student里查询
 select * from student
    where id= any
    (select stu_id from score
      where stu_id in(
       select stu_id from
       score where c_name='计算机')
     and c_name='英语');

   将计算机成绩从高到低排序
   select stu_id,grade
   from score
   where c_name='计算机'
   order by grade desc;

   查询两个表中的所有学号
   select id from student
    union
   select stu_id from score;

  查询姓张或姓王的同学的姓名，院系，考试科目和成绩
  select student.id,name,sex,birth,department,address,c_name,grade
    from student,score
    where
       (name like '张%' or name like '王%')
       and
       student.id=score.stu_id;


***************************************
插入，更新，与删除数据

1>插入
  可以为表的所有字段插入，也可以插入指定字段
  insert into 表名 values(值列表)
  insert into 表名 字段列表 values(值列表）
  同时插入多条记录
  insert into 表名 （字段列表）
      values(值列表)，（值列表）
  将查询结果插入到表中
  insert into 表名1（属性列表1）
  select 属性列表2 from 表名2 where 条表达式;
2>更新
  update 表名
  set 属性名1=取值1，属性名2=聚会2
  ...
  属性名n=取值n
  where 条件表达式;
 
 满足条件表达式的记录才会更新

3》删除数据
  delete from 表名[where 条件表达式];

  联表删除
   可以通过外键查询到所在表，然后删除


***********************************************************************
mysql的运算符

算术运算符 + - * / div  %  mod
 mod(a,3)
比较运算符 = <> != <=> < >  is null is not null between and  in like regexp
           表达式真返回1,假是0  字符等于字符串 a='a' 空不能用等号来判断
 
         
逻辑运算符 && and 与  ||  or 或  ！ not 非   xor 异或

位运算符 &按位与   |按位或    `按位取反   <<按位左移  左移右边补0
                >>按位右移  右移左边补0


运算符的优先级
 mysql 函数

数学函数
  绝对值函数，正弦函数，余弦函数，获取随机数的函数
  ABS(x)用来求绝对值 pi()获取圆周率
  Sort(x)求平方根 mod(x,y)用来求余数
  ceil(x)和ceiling(x)返回大于或等于x的最小整数
  floor(x)函数返回小于或等于x的最大整数
  rand()和rand(x)返回0-1的随机数 rand()完全随机 rand(x)x相同时返回的值是相同的
  四舍五入函数 round(x)返回离x最近的整数
  ,round(x,y)返回x保留到小数点后y位的值，截断时要四舍五入,
   truncate(x,y)返回x保留到小数点后y位的值，不进行四舍五入
  符号函数 sign(x) 返回x的符号，x是负数，0，正数分别返回-1，0，1.
  幂运算函数 pow(x,y)和power(x,y)这两个函数计算x的y次方
  exp(x)函数计算e的x次方
  对数运算函数 log(x)计算x的自然对数。log10(x)函数计算以10为底的对数。
  角度与弧度相互转换的函数 radians(x)将角度转换为弧度，degrees(x)将弧度转换为角度
  正弦函数和反正弦函数 sin(x) asin(x)
  余弦函数和反余弦函数 cos(x) acos(x) 超出范围返回null
  正切函数，反正切函数和余切函数 tan(x) atan(x) cot(x)
 


字符串函数
  字符串连接函数，字符串比较函数，将字符串的字母都变成小写或大写的函数，获取
  子串的函数等。
 
   char_length(s)函数计算字符串s的字符数;length(s)函数计算字符串S的长度
   合并字符串的函数concat(s1,s2,..)和concat_ws(x,s1,s2,..)第二个函数用参数x隔开
   替换字符串的函数insert(s1,x,len,s2)将字符串s1中x位置开始长度为len的字符串用s2替换
   字母大小写转换的函数 upuer(s) ucase（s)变大写。lower(s) lcase(s)变小写
   获取指定长度的字符串函数left(s,x),right(s,x)获取前几个字符和后几个字符
  填充字符串的函数lpad(s1,len,s2)将字符串s2填充到s1的开始处，使字符串长度
    达到len ,  rpad(s1,len,s2)将字符串s2填充到s1的结尾处，使字符串长度达到len
  删除空格的函数 ltrim(s)去掉字符串s开始处空格 rtrim(s)去掉字符串结尾处的空格
  trim(s)去掉开始处和结尾处的空格
   删除指定字符串的函数 trim(s1 from s)去掉字符串s中开始处和结尾处的字符串s1
  重复生成字符串函数 repeat(s,x)将字符串s重复x次
  空格函数space（n）返回n个空格 replace(s,s1,s2)函数将字符串s2替代字符串s中的s1
  比较字符串 strcmp(s1,s2)
  获取子串的函数 substring(s,n,len)和mid（s,n,len)从字符串s的第n个位置开始获取长度
  为len的字符串
  匹配子串开始位置locate(s1,s).position(s1 in s) instr(s,s1)从字符串s中获取s1的开始位置
  字符串逆序函数 reverse(s)
  返回指定位置的字符串的函数 elt(n,s1,s2,...)返回第n个字符串
  返回指定字符串位置的函数 field(s,s1,s2,..)返回第一个与字符串s匹配的位置
  返回子串位置的函数 find_in_set(s1,s2)返回在字符串s2中与s1匹配的字符串的位置
  选取字符串的函数 make_set(x,s1,s2,..)函数按x的二进制从s1,s2,..sn中选取字符串。如
  12的二进制为1100。这个二进制数从右到左的第三位和第四位是1.所以选取s3和s4.
   

日期和时间函数
  获取当前的时间的函数，获取当前日期的函数，返回年份的函数，返回日期
  的函数等
  curdate() 和current_date()获取当前日期
  current_time（）获取当前时间
  获取当前日期和时间的函数 now(),current_timestamp(),localtime()和sysdate()
 
  unix时间戳函数  unix_timestamp()函数以unix时间戳形式返回当前时间
  unix_timestamp(d)函数将时间d以unix时间戳形式返回。
  from_unixtime(d)函数把unix时间戳的时间转换为普通格式的时间
  unix_timestamp(d)函数和from_unixtime(d)互为反函数
  返回utc日期的函数和返回utc时间的函数  utc_date()返回utc日期 utc_time()返回
  utc时间。
  获取月份的函数month(d)和monthname(d)前者是数字，后者是英文单词
  获取星期的函数 dayname(d)显示其英文名 dayofweek(d)1表示星期日，2为星期一
    weekday(d) 0表示星期一，1表示星期二
  获取星期数的函数 week(d)和weekofyear(d);
 
  获取天数的函数dayofyear(d)本年第几天 和dayofmonth(d)本月的第几天;
  获取年份，季度，小时，分钟，秒钟的函数
  year(d)返回年份值，quarter（d）函数返回日期d是本年第几季度，hour(t)返回时间中的
 t 的小时值，minute(t)函数返回时间t时间T中的分钟值，second(d）返回秒钟值
  获取日期的指定值extract(type from d)
  时间和秒钟转换的函数 time_to_sec(t） sec_to_time(s)
  计算日期和时间的函数
   to_days(d)把日期转换成天数,from_days(n)天数转换成日期 和datediff(d1,d2)两个日期差
   adddate(d,n)给日期d加n天,subdate(d,n)给日期d减去n天,
   addtime(t,n)给时间t加n秒 和subtime(t,n)给时间减去n秒
   adddate(d,interval expr type)和date_add(d,interval expr type)
   计算一年一个月后的日期和时间
   select dt,adddate(dt,interval '1 1'year_month)from t4;
   将日期和时间格式化函数
   date_format(d,f)
   time_format(t,f)
   get_format(type,s)   参见mysql日期格式




流程函数
  这类函数主要用于在SQL语句中控制条件选择。包括if语句，case语句，when语句等
  if(expr,v1,v2)表达式expr成立，满足返回v1,否则返回v2
  ifnull(v1,v2)如果v1不为空，就显示v1否则显示v2
  case when expr1 then v1 [when expr2 then v2 ...][else vn] end
  case expr when e1 then v1[when e2 then v2..][else vn] end
   如 select id,grade, case when grade>60 then 'good' when grade=60 then 'pass'
   else 'fail' end level from t6;
   
系统信息函数
  获取mysql数据库的系统信息。其中包括获取数据库名的函数，获取当前用户的函数。获取数据库
  版本的函数。
  version()函数返回数据库的版本号
  connection_id()返回服务器的连接数，也就是到现在为止mysql服务的连接次数
  database()和schema()返回当前数据库名
  获取用户名的函数
   user(),system_user(),session_user(),current_user(),current_user返回当前用户名称
  获取字符串的字符集和排序方式的函数 charset(str)返回字符串str的字符集
  collation(str)返回字符串str的字符排列方式
  last_insert_id()函数返回最后生成的auto_increment值
 

加密函数
  对字符串进行加密解密。包括字符串加密函数，字符串解密函数等。
 
   password(str)对字符串str进行加密
   md5(str)可以对字符串str进行加密
   encode(str,pswd_str)可以使用字符串pswd_str来加密字符串str.加密的结果是一个二进制
   数，必须使用blob类型的字段来保存它。
   decode(crypt_str,pswd_str)可以使用字符串pswd_str来为crypt_str解密。

format(x,n)函数可以将数字x进行格式化，将x保留到小数点后n位，这个过程要四舍五入

不同进制的数字转换的函数
  ascii(s)返回字符串S的第一个字符的ASCII码
  Bin(x)返回X的二进制编码
  hex(x)返回x的十六进制编码
  oct(x)返回x的八进制编码
  conv(x,f1,f2)将x从f1进制数变成f2进制数

ip地址与数字相互转换的函数
  inet_aton(ip)将ip地址转换为数字表示
  inet_ntoa(n)将数字n转换成ip的形式，其中ip值要加引号，互为反函数


加锁函数和解锁函数
  get_lock(naem,time)定义一个名称为name，持续时间长度为time秒的锁。如果锁定成功
  返回1,如果尝试失败返回0，如果遇到错误，返回null
  release_lock(name)解除名称为name的锁，如果解锁成功返回1，如果尝试超时，返回0，如果
  解锁失败返回null， is_free_lock(name)判断是否使用名为name的锁。如果使用返回0，否则
  返回1

重复执行指定操作的函数
  benchmark(count,expr)函数将表达式expr重复执行count次，然后返回执行时间。该函数
  用来判断mysql处理表达式的速度

改变字符集的函数
  convert(s using cs)将字符串s的字符集变成cs

改变字段数据类型的函数
  cast(x as type) convert(x,type)将x变成type类型这两个函数只对binary,char,date
   datetime,time,signed integer, unsignde integer这些类型起作用，但两种方法只是改变了
   输出值的数据类型，并没有改变表中字段的类型

*************************************

存储过程和函数
  存储过程和函数是在数据库中定义一些sql语句的集合，然后直接调用这些存储过程和函数来执行
已经定义好的sql语句。存储过程和函数可以避免开发人员重复的编写相同的sql语句。而且存储过程
和函数是在mysql服务器中存储和执行的，可以减少客户端和服务器端的数据传输。

创建存储过程  
 create procedure sp_name
  ([proc_parameter[,..]])
     [characteristic...]routine_body
      [in|out|inout]param_name type
 
  下面创建一个名为num_from_emplyee存储过程
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
 
创建存储函数
  create function sp_name([func_parameter[,...]])
    retruns type
    [characteristic..]routine_body

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
 
 
变量的使用  
  存储过程和函数可以定义变量和使用变量。用户可以使用declare关键字来定义
变量。然后可以为变量赋值。这些变量的作用范围是begin...end程序段中。

  下面定义变量my_sql，数据类型为int型，默认值为10
  declare my_sql int defaut 10;
 
  为变量赋值
  set my_sql=30;
  下面从employee表中查询id为2的记录，将该记录的d_id值赋给变量my_sql
   select d_id into my_sql
         from employee where id=2;
 

定义条件和处理程序
  是事先定义程序执行过程中可能遇到的问题。并且可以在处理程序中定义解决这
些问题的办法。这种方式可以提前预测可能出现的问题，并提出解决办法。mysql
中都是通过declare关键字来定义条件和处理程序。
  定义条件
   declare condion_name condition for condition_value
   condition_value;
      sqlstate[value]sqlstate_value|mysql_error_code
  下面定义Error 1146(42s02)这个错误，名称为can_not_find,可以用两种不同的
方式定义
  1.declare can_not_find condition for sqlstate '42s02';
  2.declare can_not_find condition for 1146;

定义处理程序
  declare handler_type handler for condition_value[,..]sp_stateement
  handler_type
     continue|exit|undo
  condition_value:
     sqlstate[value]sqlstate_value|condition_name|sqlwarnin|notfound|
     sqlexception|mysql_error_code

下面定义处理程序的几种方式代码如下
  1.捕获sqlstate_value
     declare continue handler for sqlstate '42s02' set @info='can not find';
  2.捕获mysql_error_code
     declare continue handler for 1146 set @info='can not find';
  3.先定义条件，然后调用
     declare can_not_find condition for 1146;
     declare continue handler for can_not_find set @info='can not find';
  4.使用sqlwarning
    declare exit handler for sqlwarning set @info='error';
  5.使用not found
    declare exit handler for notfound set @info='can not find';
  6.使用sqlexception
    declare exit handler for sqlexception set @info='can not find'

光标的使用  
  查询语句可能查询出多条记录，在存储过程和存储函数中使用光标来逐条读取查询
结果集中的记录。有些书将光标称为游标。光标的使用声明在处理程序之前，并且声明
在变量和条件之后。  

1.声明光标
  declare cur_employee CURSOR for select name,age from employee;
2.打开光标
  open cur_employee;
3.使用光标
  fetch cur_employee into emp_name,emp_age;
4.关闭光标
  close cur_employee;

流程控制的使用
  存储过程和存储函数中可以使用流程控制来控制语句的执行。
  mysql中可以使用if语句，case语句，loop语句，leave语句，aterate语句，
  repeat语句，while语句来进行流程控制。
1.if语句
  if search_condition then statement_list
    [elseif search_condition then statement_list]...
    [else statement_list]
  end if
 
  示例 if age>20 set @count1=@count1+1;
       elseif age=20 then @count2=@count2+1;
       else @count3=@count3+1
2.case语句
  case case_value
       where when_value then statement_list;
       [when when_value then statement_list]...
       [else statement_list]
  end case;
  示例 case age
          when 20 then set @count1=@count1+1;
          else set @count2=@count2+1;
       end case;
3.loop
  [begin_label]loop
       statement_list
   end loop [end_label]

   示例  add_num:loop
         set @count=@count+1;
          end loop add_num;
4.leave语句   跳出循环
     add_num:loop
      set @count=@count+1;
      if @count=100 then
          leave add_num;
     end loop add_num;
5.iterate语句 跳出本次循环
  add_num:loop
    set @count=@count+1;
    if @count=100 then
       leave add_num;
   else if mod(@count,3)=0 then
      iterate add_num;
   select * from employee;
  end loop add_num;

6.repeat
  [begin_label]repeat
     statement_list
    until search_condition
  end repeat [end_label]

  示例 repeat set @count=@count+1;
        until @count=100;
       end repeat;
7.while语句
   [begin_label]while serch_condition do
           statement_list
    end while
  示例 while @conte<100 do
       set @count=@count+1
       end while;

调用存储过程和函数
  1.调用存储过程后，数据库将执行存储过程中的语句。然后将结果返回给输出值
    调用存储过程
    call sp_name([parameter[,...]]);  
 
  call num_from_employee(1002,@n);
  select @n;
 
  2.调用存储函数
    select name_from_employee(3);

查看存储过程和函数
  用户可以通过show status语句来查看存储过程和函数的状态。也可通过show
 create 语句来查看存储过程和函数的定义。用户也可通过查询information_schema
数据库下的routines表来查看存储过程和函数的信息。
 
  show {procedure|function} status[like 'pattern'];  
  后面like ‘pattern’参数用来匹配存储过程和函数的名称
show procedure status like 'num_from_employee'\G

  show create {procedure|function}sp_name;
 
show create procedure num_from_employee;


修改存储过程和函数
 alter procedure语句来修改存储过程，通过alter function语句来修改存储函数

alter {procedure|function}sp_name [characteristic...]
     characteristic:
    { contains sql | no sql | reads sql data | modifies sql data }
     | sql security{definer| invoker}
     | comment 'string'

 下面修改存储过程num_from_employee的定义，将读写权限改为modifiel sql data
并指明调用者可以执行。
  alter procedure num_from_employee
       modifies sql data
       sql security invoker;


 下面修改存储函数name_from_employee的定义。将读写权限必为reads sql data
并加上注释信息‘find name’
  alter function name_from_employ
      reads sql data
      comment 'find name';


 删除存储过程和函数

drop {procedure|function} fp_name;
 

mysql用户管理

mysql用户包括普通用户和root用户。这两种用户的权限不一样。root 用户是超级管理员
，拥有所有的权限。root用户的权限包括创建用户，删除用户。修改普通用户的密码等管理
权限。而普通用户只拥有创建该用户时赋予它的权限。用户管理包括管理用户的账户，权限等
。

权限表介绍
 安装mysql时会自动安装一个mysql的数据库。mysql数据库下面存储的都是权限表。这些权限表
 中最重要的是user表，，db表和host表。除此之外，还有tables_priv表，columns_priv表
 proc_priv表等
 
 user表 基本包括四种列 用户列，权限列，安全列，资源控制列    
 db表和host表也是权限表。db表存储了某个用户对一个数据库的权限。db表比较常用，而
  host表少用到。 基本包括 用户列 和 权限列
 
  tables_priv表可以对单个表进行权限设置。columns_priv表可对单个列进行权限设置
  tables_priv包含8个字段。分别是Host.Db,User.Table_name,Table_priv,Colunm_priv
  Timestamp和Grantor。前四个字段分别表示主机名，数据库名，用户名和表名。
  table_priv表示对表进行操作的权限。这些权限包括select.insert,update,delete.
  create,drop,grant.references,index和alter.Column_priv表示对表中的数据列进行操作的权限
  。这些权限包括select,insert,update和references。timestamp表示修改权限的时间。
  grantor表示权限是谁设置的。






用户登陆和退出mysql服务器

创建和删除普通用户

普通用户和root用户的密码管理

权限管理