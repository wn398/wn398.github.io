title: mysql数据库
date: 2013/3/19 14:44:43
tags:
- 技术
- mysql
- 数据库
categories: 数据库
---
#mysql手动配置目录
>在mysql安装目录下的my.ini，其它的ini文件都是针对不同数据库大小的配置模板，真正起作用的是my.ini所以修改配置就是修改my.ini文件

#mysql的数值类型

##整数类型

- tinyint 1字节
- smallint 2字节, 
- mediumint 3字节, 
- integer 4字节, 
- int 4字节, 
- bigint 8字节

##浮点数类型 
- float 4字节,
- double 8字节
- float(6,2)长度为6，小数点后保留2位  超过之后会四舍五入

##定点数类型 
- decimal 16字节
- decimal(6,2)  此类型默认为整型（10，0）小数后保留一位，超过之后会报错

##日期与时间类型
- year 1字节 以YYYY形式存储 范围从1901-2155超过之后会为0000,
- time 3字节 以HH:MM:SS表示 范围 -838：59:59--838:59:59,

		insert into time values (now())
- date 4字节 以YYYY-MM-DD 范围 1000-1-1----9999-12-31,使用`CURRENT_DATE`或`NOW()`输出当前日期
- datetime 8字节 YYYY-MM-DD HH:MM:范围SS,1000-1-1 ----9999-12-31
- timetamp 4个字节 范围从1970-01-01 08：00：01到2038-01-19 11：14：07

##字符串类型
- char类型与varchar类型 在创建表时指定类型如CHAR(5)指定之后不可变长，VARCHAR(5)指定之后可变长，最长255
- TEXT 只能保存字符数据  `tinytext` `text` `mediumtext` `longtext`
- ENUM类型 在创建表时以列表形式指定了形式如： `属性名 ENUM('值1'，‘值2’...)`
- SET类型  基本形式如下：`属性名 SET('值 1'，‘值2’....)` set类型的值可以取列表中的一个元素或者多个元素的组合，最多有64个 如 

		create table sets(a SET('a','b','c','d','e'));

##二进制类型
- binary(M) 字节数为M长度固定不足以‘\0’补充，
- varbinary(M) 这与char与varchar类型类似

		create table vb (b binary(4),vb varbinary(4));  
		insert into vb values('d','d')	
		select length(b),length(vb) from vb;
	
- bit（M）M的最大值是65 创建表指定了最大长度 也是二进制类型
- blob类型是一种特殊的二进制类型，可以保存数据量很大的数据如图片等，包括`tinyblob`,`blob`,`mediumblob`,`longblob`. 它和text类型相似
##布尔类型
在sql中存在bool boolean类型，最后存储为tinyint(1)

#mysql存储路径
mysql中，`char`,`varchar`,`text`等字符串类型都可以存储路径。但是，如果路径中使用‘\’符号时，这个符号会被过滤。解决办法是，路径中用‘/’或者‘\\’来代替‘\’
#mysql存储引擎
- innoDB存储引擎（默认） 提供了事务，回滚，崩溃修复能力，多版本并发控制的事务安全， 支持自动增长列，支持外键， 缺点是读写效率稍差，占用的数据空间大
- MyISAM存储引擎 占用空间小，处理速度快，缺点是不支持事务的完整性和并发性
- Memery存储引擎，特殊存储引擎。其使用存储在内存中的内容来创建表，而且所有数据也放在内存中。这样有利于对数据的快速的处理，提高整个表的处理效率。但表的大小是受限制的，取决于两个参数

查看支持的存储引擎：

	show engines 
    show variables like 'have%'
查看默认存储引擎

	show variables like 'storage_engine';

#mysql的运算符

- 算术运算符  + - * / div  %  mod

	如：mod(a,3)
- 比较运算符  = <> != <=> < >  is null is not null between and  in like regexp
- 逻辑运算符  && and 与  ||  or 或  ！ not 非   xor 异或

- 位运算符 &按位与，|按位或，`按位取反，<<按位左移 左移右边补0，>>按位右移 右移左边补0

#mysql数据库函数
---
##数学函数
- ABS(x) 用来求绝对值
- pi() 获取圆周率
- Sort(x) 求平方根
- mod(x,y) 用来求余数
- ceil(x)和ceiling(x) 返回大于或等于x的最小整数
- floor(x)函数 返回小于或等于x的最大整数
- rand()和rand(x) 返回0-1的随机数 rand()完全随机 rand(x)x相同时返回的值是相同的
- 四舍五入函数 round(x) 返回离x最近的整数，round(x,y)返回x保留到小数点后y位的值，截断时要四舍五入,
- truncate(x,y) 返回x保留到小数点后y位的值，不进行四舍五入
- 符号函数 sign(x)  返回x的符号，x是负数，0，正数分别返回-1，0，1.
- 幂运算函数 pow(x,y)和power(x,y) 这两个函数计算x的y次方，exp(x)函数计算e的x次方
- 对数运算函数 log(x)计算x的自然对数。log10(x)函数计算以10为底的对数。
- 角度与弧度相互转换的函数 radians(x)将角度转换为弧度，degrees(x)将弧度转换为角度
- 正弦函数和反正弦函数 sin(x) asin(x)
- 余弦函数和反余弦函数 cos(x) acos(x) 超出范围返回null
- 正切函数，反正切函数和余切函数 tan(x) atan(x) cot(x)

##字符串函数
- char_length(s)函数 计算字符串s的字符数;
- length(s)函数 计算字符串S的长度
- 合并字符串的函数concat(s1,s2,..)和concat_ws(x,s1,s2,..)第二个函数用参数x隔开
- 替换字符串的函数insert(s1,x,len,s2)将字符串s1中x位置开始长度为len的字符串用s2替换
- 字母大小写转换的函数 upuer(s) ucase（s)变大写。lower(s) lcase(s)变小写
- 获取指定长度的字符串函数left(s,x),right(s,x)获取前几个字符和后几个字符
- 填充字符串的函数lpad(s1,len,s2)将字符串s2填充到s1的开始处，使字符串长度达到len,rpad(s1,len,s2)将字符串s2填充到s1的结尾处，使字符串长度达到len
- 删除空格的函数 ltrim(s)去掉字符串s开始处空格 rtrim(s)去掉字符串结尾处的空格,trim(s)去掉开始处和结尾处的空格
- 删除指定字符串的函数 trim(s1 from s)去掉字符串s中开始处和结尾处的字符串s1
- 重复生成字符串函数 repeat(s,x)将字符串s重复x次
- 空格函数space（n）返回n个空格 replace(s,s1,s2)函数将字符串s2替代字符串s中的s1
- 比较字符串 strcmp(s1,s2)
- 获取子串的函数 substring(s,n,len)和mid（s,n,len)从字符串s的第n个位置开始获取长度
  为len的字符串
- 匹配子串开始位置locate(s1,s).position(s1 in s) instr(s,s1)从字符串s中获取s1的开始位置
- 字符串逆序函数 reverse(s)
- 返回指定位置的字符串的函数 elt(n,s1,s2,...)返回第n个字符串
- 返回指定字符串位置的函数 field(s,s1,s2,..)返回第一个与字符串s匹配的位置
- 返回子串位置的函数 find_in_set(s1,s2)返回在字符串s2中与s1匹配的字符串的位置
- 选取字符串的函数 make_set(x,s1,s2,..)函数按x的二进制从s1,s2,..sn中选取字符串。如
12的二进制为1100。这个二进制数从右到左的第三位和第四位是1.所以选取s3和s4.
   

##日期和时间函数
###获取日期和时间函数
- curdate()和current_date() 获取当前日期
- current_time（）获取当前时间
- 获取当前日期和时间的函数 now(),current_timestamp(),localtime()和sysdate()
- unix时间戳函数  unix_timestamp()函数以unix时间戳形式返回当前时间
- unix_timestamp(d)函数 将时间d以unix时间戳形式返回。
- from_unixtime(d)函数 把unix时间戳的时间转换为普通格式的时间
- unix_timestamp(d)函数和from_unixtime(d)互为反函数
- 返回utc日期的函数和返回utc时间的函数  utc_date()返回utc日期 utc_time()返回
  utc时间。
- 获取月份的函数month(d)和monthname(d)前者是数字，后者是英文单词
- 获取星期的函数 dayname(d)显示其英文名 dayofweek(d)1表示星期日，2为星期一,weekday(d) 0表示星期一，1表示星期二,获取星期数的函数 week(d)和weekofyear(d);
- 获取天数的函数dayofyear(d)本年第几天 和dayofmonth(d)本月的第几天;
- year(d)返回年份值
- quarter（d）函数返回日期d是本年第几季度
- hour(t)返回时间中的 t 的小时值
- minute(t)函数返回时间t时间T中的分钟值
- second(d）返回秒钟值
- 获取日期的指定值extract(type from d)
- 时间和秒钟转换的函数 time_to_sec(t） sec_to_time(s)
###计算日期和时间的函数
- to_days(d)把日期转换成天数
- from_days(n)天数转换成日期
- datediff(d1,d2)两个日期差
- adddate(d,n)给日期d加n天
- subdate(d,n)给日期d减去n天,
- addtime(t,n)给时间t加n秒 
- subtime(t,n)给时间减去n秒
- adddate(d,interval expr type)和date_add(d,interval expr type)
	计算一年一个月后的日期和时间
   	
		select dt,adddate(dt,interval '1 1'year_month)from t4;
###日期和时间格式化函数
- date_format(d,f)
- time_format(t,f)
- get_format(type,s)   参见mysql日期格式

##流程函数
>这类函数主要用于在SQL语句中控制条件选择。包括if语句，case语句，when语句等

- if(expr,v1,v2)表达式expr成立，满足返回v1,否则返回v2
- ifnull(v1,v2)如果v1不为空，就显示v1否则显示v2
- case expr when expr1 then v1 [when expr2 then v2 ...][else vn] end

如

		select id,grade, case when grade>60 then 'good' when grade=60 then 'pass'
		   else 'fail' end level from t6;
##系统信息函数
>获取mysql数据库的系统信息。其中包括获取数据库名的函数，获取当前用户的函数。获取数据库
版本的函数。
###数据库信息函数
- version()函数返回数据库的版本号
- connection_id()返回服务器的连接数，也就是到现在为止mysql服务的连接次数
- database()和schema()返回当前数据库名
- last_insert_id()函数返回最后生成的auto_increment值
###获取用户名的函数
- user(),system_user(),session_user(),current_user(),current_user返回当前用户名称
###获取字符串的字符集和排序方式的函数 
- charset(str)返回字符串str的字符集
- collation(str)返回字符串str的字符排列方式

##加密函数
>对字符串进行加密解密。包括字符串加密函数，字符串解密函数等。
 
- password(str)对字符串str进行加密
- md5(str)可以对字符串str进行加密
- encode(str,pswd_str)可以使用字符串pswd_str来加密字符串str.加密的结果是一个二进制
数，必须使用blob类型的字段来保存它。
- decode(crypt_str,pswd_str)可以使用字符串pswd_str来为crypt_str解密。
- format(x,n)函数可以将数字x进行格式化，将x保留到小数点后n位，这个过程要四舍五入

##不同进制的数字转换的函数
- ascii(s)返回字符串S的第一个字符的ASCII码
- Bin(x)返回X的二进制编码
- hex(x)返回x的十六进制编码
- oct(x)返回x的八进制编码
- conv(x,f1,f2)将x从f1进制数变成f2进制数

##ip地址与数字相互转换的函数
- inet_aton(ip)将ip地址转换为数字表示
- inet_ntoa(n)将数字n转换成ip的形式，其中ip值要加引号，互为反函数

##加锁函数和解锁函数
- get_lock(naem,time)定义一个名称为name，持续时间长度为time秒的锁。如果锁定成功返回1,如果尝试失败返回0，如果遇到错误，返回null
- release_lock(name)解除名称为name的锁，如果解锁成功返回1，如果尝试超时，返回0，如果解锁失败返回null，
- is_free_lock(name)判断是否使用名为name的锁。如果使用返回0，否则返回1

##重复执行指定操作的函数
- benchmark(count,expr)函数将表达式expr重复执行count次，然后返回执行时间。该函数用来判断mysql处理表达式的速度

##改变字符集的函数
- convert(s using cs)将字符串s的字符集变成cs

##改变字段数据类型的函数
- cast(x as type) convert(x,type)将x变成type类型这两个函数只对binary,char,date,datetime,time,signed integer, unsignde integer这些类型起作用，但两种方法只是改变了输出值的数据类型，并没有改变表中字段的类型

#mysql用户管理
>mysql用户包括普通用户和root用户。这两种用户的权限不一样。root 用户是超级管理员，拥有所有的权限。root用户的权限包括创建用户，删除用户。修改普通用户的密码等管理权限。而普通用户只拥有创建该用户时赋予它的权限。用户管理包括管理用户的账户，权限等。

权限表介绍
 安装mysql时会自动安装一个mysql的数据库。mysql数据库下面存储的都是权限表。这些权限表 中最重要的是user表，，db表和host表。除此之外，还有tables_priv表，columns_priv表 proc_priv表等
 
- user表 基本包括四种列 用户列，权限列，安全列，资源控制列    
- db表和host表也是权限表。db表存储了某个用户对一个数据库的权限。db表比较常用，而host表少用到。 基本包括 用户列 和 权限列
- tables_priv表可以对单个表进行权限设置。tables_priv包含8个字段。分别是Host.Db,User.Table_name,Table_priv,Colunm_priv,Timestamp和Grantor。前四个字段分别表示主机名，数据库名，用户名和表名。
- columns_priv表可对单个列进行权限设置
- table_priv表示对表进行操作的权限。这些权限包括select,insert,update,delete,create,drop,grant,references,index和alter.
- column_priv表示对表中的数据列进行操作的权限。这些权限包括select,insert,update和references。timestamp表示修改权限的时间。
- grantor表示权限是谁设置的。