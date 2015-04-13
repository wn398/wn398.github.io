title: javap,jmap与java字节码
date: 2015/4/13 9:49:13 
updated: 2015/4/13 9:49:27 
tags:
- 技术
- 字节码
categories: JVM
---
##javap
>javap是一个java自带的字节码查看工具

`javap 字节码文件`  默认情况下，会显示访问权限为`public`,`protected`和默认（即包级别`protected`）级别的方法，加上`-p`选项后还可以显示private方法和域

`javap -s 字节码文件名`  可以输出签名的类型描述符

类型描述符对应关系：

	B----byte
	C----char(16位Unicode字符)
	D----double
	F-----float
	I------int
	J------Long
	L<类型名称>   引用类型 如Ljava/lang/String
	S------short
	z--------boolean
	[--------array-of

常量池是为类文件中的其他（常量）元素提供快捷访问方式的区域。

查看常量池可以用 `javap -v 字节码文件名`

用带-c选项的javap可以对类进行反编译  `javap -c 字节码文件名`


#jmap
jmap，它能帮你弄清楚程序运行时的内存使用情况，以及所有内存都用在哪里。它用来显示java进程的内存映射

1. 默认视图
jmap最简单的用法就是查看连接到进程里的本地类库。（`jmap  进程号`）
2. 堆视图
使用 `-heap` 选项时，jmap会抓取进程当前的堆快照。
使用 `-histo:live`选项，可以告诉jmap只处理存活对象，而不是整个堆（jmap默认会处理所有对象，也包括还没被收集的垃圾。）

用jamp产生离线导出文件：

jmap能创建导出文件，像这样：`jmap -dump:live,format=b,file=heap.hprof 19306`
导出结果可以用来做离线分析，可以留给jmap以后自己用，也可以留给Oracle的jhat(Java 堆分析工具)做高级分析。

与GC相关的jvm参数：

	-Xms   堆的初始大小（默认2MB）
	-Xmx   堆的最大大小（默认64MB）
	-Xmn   堆中年轻代的大小
	-XX:-DisableExplicitGC   让调用System.gc()不产生任何作用