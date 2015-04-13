title: Groovy笔记
date: 2015/4/13 9:56:57 
updated: 2015/4/13 9:57:02 
tags:
- 技术
- groovy
categoreis: Groovy
---
#groovy默认导入如下包：
	groovy.lang.*;
	groovy.util.*
	java.lang.*
	java.io.*
	java.math.BigDecimal
	java.math.BigInteger
	java.net.*
	java.util.*

#Groovy有大量可以省略的语法
- 语句结束处的分号
- 返回语句（return）groovy会自动返回最后一个表达式的计算结果
- 方法参数两边的括号，如果Groovy里的方法调用至少有一个参数，并且没有二义性，则可以省略括号
- public 访问限定符

跟Java不同，groovy不区分已检查异常和未检查异常，Groovy编译器会忽略方法签名中的所有throws语句

Groovy把==当做Java中的equals()方法。 检查真实的对象是否相等，需要使用Groovy内置的is()函数，

#Groovy具备的Java没有的一些新特性
- GroovyBean  更简单的bean;  
    它很像JavaBean,不过省略了显式声明的获取和设置方法，提供了自动构造方法，并允许你用点号引用成员变量，如果需要把某个获取方法或设置方法高为private,或者希望改变默认的行为，可以显式声明那个方法，并按你的想法修改它。
- 用操作符`?.`实现null对象的安全访问
- 更短的`if/else`结构（`？ ：`）
    可以去掉重复的变量类似于`String status = agentStatus? agentStatus:"Inactive"`可以简化为：`String status = agentStatus?:"Inactive";`   
- Groovy字符串，更强的字符串抽象
- 函数字面值（即闭包），把函数当做值传递
- 对正则表达式的支持
- 更简单的XML处理

##（函数字面量）闭包：
>函数字面值表示一个可以当做值传递的代码块，也可以像操作任何值一样操作，可以当参数传给方法，可以给变量赋值，等等。

##Groovy中的部分集合函数
- each   遍历集合，对其中每一项应用 函数字面值
- collect  收集在集合中每一项上应用函数字面值的返回结果（相当于其他语言map/reduce中的map函数）
- inject  用函数字面值处理集合并构建返回值（相当于其他语言map/reduce中的reduce函数）
- findAll   找到集合中所有与函数字面值匹配的元素
- max    返回集合中的最大值
- min     返回集合中的最小值

##对正则表达式的支持：
Groovy把正则表达式当做语言的一部分，用Groovy处理文本要比Java简单得多
Groovy正则表达式语法 ：

1. `~`  创建一个模式（创建一个编译的Java Pattern对象）
2. `=~`  创建一个匹配器（创建一个Java Matcher对象）
3. `==~`  计算字符串（相当于在Pattern上调用Java match()方法）

##简单XML处理
Groovy有构建器的概念，用Groovy语法可以处理任何树形结构的数据，包括HTML,XML,和JSON。
创建简单的xml:

	def writer = new StringWriter();
	def xml = new groovy.xml.MarkupBuilder(writer);
	xml.persion(id:2,tag:3){
	    name "hello"
	    age 333
	    xml.school(id:8){
	        name "niub school"
	        place "datong"
	    }
	}
	println writer.toString();

#Groovy与Java的合作
1. 从Groovy调用 Java: 只要把JAR放到Classpath中，然后用标准的import语句就可以
2. 从Java调用groovy: 需要把Groovy及其相关的JAR放到这个程序的CLASSPATH下，因为它们都是运行时的依赖项。

##下面是几种从Java调用Groovy代码的办法：
###使用GroovyShell
使用Bean Scripting Framework(BSF) 即JSR223
在临时性快速调用Groovy并计算表达式或类似于脚本的代码时，可用GroovyShell,如下：

	import groovy.lang.Binding;
	import groovy.lang.GroovyShell;
	public class GroovyShellTest {
	 public static void main(String[] args) {
	  Binding binding = new Binding();
	  binding.setVariable("x1", 234);
	  binding.setVariable("x2", 43434);
	  GroovyShell groovyShell = new GroovyShell(binding);
	  Object value = groovyShell.evaluate("x1+x2");
	  System.out.println((int)value);
	 }
	}

用GroovyShell只能应付快速执行小段Groovy代码的情况

###使用GroovyClassLoader
从开发人员角度看，GroovyClassLoader的表现很像Java的ClassLoader，找到类和想要调用的方法，然后调用就行了。

下面的代码中有一个简单的CalculateMax类，其中有个getMax方法，会使用Groovy内置的max函数，要在Java中通过GroovyClassLoader运行这个方法，需要用下面的代码创建一个Groovy文件（CalculateMax.groovy）:

	    class CalculateMax{
	    def Integer getMax(List values){
	        values.max();
	    }
	}
现在我们有了要执行的Groovy脚本，可以从Java调用 它

	public static void main(String[] args) throws Exception {
	  GroovyClassLoader groovyClassLoader = new GroovyClassLoader();
	  try{
	   Class<?> groovyClass = groovyClassLoader.parseClass(new File("CalculateMax.groovy"));
	   
	   GroovyObject groovyObject = (GroovyObject)groovyClass.newInstance();
	   
	   ArrayList<Integer> numbers = new ArrayList<Integer>();
	   numbers.add(12);
	   numbers.add(45);
	   numbers.add(-98);
	   
	   Object[] arguments = {numbers};
	   Object value = groovyObject.invokeMethod("getMax", arguments);
	   System.out.println((int)value);
	  }catch(Exception e){
	   e.printStackTrace();
	  }finally{
	   groovyClassLoader.close();
	  }
	 }
这种技术在调用几个Groovy实用类时可能会有用，但如果用大量的Groovy代码，我们推荐使用完整的GroovyScriptEngine

###使用GroovyScriptEngine;
使用GroovyScriptEngine要指明Groovy代码的URL或所在目录。Groovy脚本引擎会加载那些脚本，并在必要时进行编译，包括的其中的依赖脚本。比如你修改了脚本B,而脚本A依赖于B,则引擎会全重新编译它们。

假设一个Groovy脚本（Hello.groovy）定义了一个简单的“Hello”语句，后面跟着一个名字（要从Java应用 程序中传入的参数）`def helloStatement = "Hello ${name}"`
然后Java程序会通过GroovyScriptEngine使用Hello.groovy，并输出一句问候，代码如下：

	public static void main(String[] args) throws Exception {
	  String[] roots = new String[]{"src/invokeGroovy"};
	  GroovyScriptEngine gse = new GroovyScriptEngine(roots);
	  Binding binding = new Binding();
	  binding.setVariable("name", "Sue");
	 
	  Object output = gse.run("Hello.groovy", binding);
	 
	  System.out.println(output);
	 }
GroovyScriptEngine监控下的任何Groovy脚本都可能被程序员改掉，下次再运行时就会加载修改后的脚本，这样Java应用程序就具备了以前根本不可能出现的动态灵活性。
###使用嵌入式Groovy控制台
