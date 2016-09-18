title: xml基础要点
date: 2016/9/18 星期日 PM 3:35:02 
updated: 2016/9/18 星期日 PM 3:35:07 
tag:
- xml
- 技术
- 描述语言

categories: 技术
---
1.	XML核心技术
	1.	文档描述，验证，约束技术----DTD/Schema
	2.	文档转换技术—XSL
	3.	数据查询技术-XPath
	4.	链接技术-XLink/XPointer
	5.	编程接口—DOM/SAX
2.	每个xml文件有且只有一个根元素，它只描述文档的内容和语义。
	![](/images/xml比较.png) 
3.	对于XML文档来说 <?处理指令必须顶格写，XML元素可以具有属性，属性的形式为：属性名=“属性值”，比如gender=”male”,多个属性之间使用空格分开
4.	通过样式可以实现，内容与展示分开`<?xml-stylesheet type=”text/css” href=”hi.css”?>` 为XML文档编写样式有两种语言可选CSS和XSL(Extensible Style Language).XSL是当前最强大和灵活的样式语言，是特别为应用XML而设计的。XSL比CSS更为复杂
5.	XML文档声明
	1.	Xml声明一般是xml文档的第一行
	2.	Xml声明由以下几个部分组成：
		1.	Version 文档符合xml1.0规范，现在只有1.0
		2.	Encoding 文档字符编码，默认为utf-8
		3.	Standalone 文档定义是否在一个文件内
6.	XML实体  引用实体必须以分号结束
 	![](/images/xml实体.png)
 > 右侧为内置实体，定义的实体就相当于Java中的一个变量，在其它地方的引用可以达到改变实体的值达到所有引用地方都变动

如何定义和使用实体
 
	<?xml version="1.0"?> 
	<!DOCTYPE company[ 
	<!ENTITY tim "蒂姆"> 
	<!ENTITY address "北京"> 
	]> 
	<company> 
	<name>&tim;</name> 
	<address>&address;</address> 
	</company>

7.	有效的（valid）xml文档。首先xml文档是个格式正规的xml文档，然后又需要满足DTD的要求，这样的xml文档成为有效的xml文档。
8.	DTD  文档类型定义 Document Type Definition 一个DTD文档包含：
	1.	元素（ELEMENT）的定义规则
	2.	元素之间的关系规则
	3.	属性（ATTLIST）的定义规则
	4.	可使用的实体（ENTITY）或符号（NOTATION）规则
9.	如何生成一个DTD文档
	1.	DTD文档的声明及引用
		1.	内部DTD文档<!DOCTYPE 根元素[定义内容]>
		2.	外部DTD文档  <!DOCTYPE 根元素 SYSTEM “DTD文件路径”>
		3.	内外部DTD文档结合<!DOCTYPE 根元素  SYSTEM “DTD文件路径”[定义内容]>
10.	DTD元素定义  <!ELEMENT NAME CONTENT>
	1.	元素类型有如下：
		1.	EMPTY 该元素不能包含子元素和文本，但可以有属性（空元素）
		2.	ANY 该元素可以包含任何在DTD中定义的元素内容
		3.	#PCDATA 可以包含任何字符数据，但是不能在其中包含任何子元素
		4.	纯元素类型 只包含子元素，并且这些子元素外没有文本
		5.	混合类型-包含子元素和文本数据的混合体
	  ![](/images/xml修饰符号.png) 
11.	DTD属性定义
<!ATTLIST 元素名称  属性名称  类型  属性特点>
	1.	类型:
		1.	CDATA   什么字符串都可以
			1.	NMTOKEN是CDATA的一个子集，表示属性必须是英文字母，数字，句号，存折号，下划线或冒号，属性值不能含有空格
			2.	NMTOKENS与NMTOKEN类似，包含多个由空格分隔的字符
			2.	ID  表明属性的取值必须是唯一的
			3.	IDREF/IDREFS  IDREF属性的值指向文档中其它地方声明的ID类型的值，IDREF，可以具有由空格分开的多个引用
			4.	Enumerated  （单身|离婚|离异）
			5.	ENTITY/ENTITIES
			6.	NOTATION 
			7.	NOTATIONS 
	2.	属性特点：
		1.	#REQUIRED   必须出现的
		2.	#IMPLIED   可有可无的
		3.	#FIXED value
		4.	Default value
12.	实体定义   
	1.	内部实体  <!ENTITY 实体名  “实体值”>
	2.	外部实体  <!ENTITY 实体名  SYSTEM “URI/URL”> 
		![](/images/xml外部实体.png)
	3.	普通实体是在DTD中定义，xml中使用，使用的时候格式为：&address;    参数实体是在DTD中定义，dtd中使用，定义的时候使用%，使用的时候也需要使用%，%address;
	4.	外部普通实体的定义方式是：<!ENTITY address SYSTEM http://www.baidu.com>
使用&address，表示使用网址的内容来替换address，外部普通参数实体类似
13.	Schema 非常重要  是一套预先规定的xml元素和属性创建的，这些元素和属性定义了xml文档的结构和内容模式
	1.	Schema的元素类型
		1.	Schema   任何一个schema文档的根元素是<xs:schema>  属性 xmlns,targetNamespace
		2.	Element  属性 name,type,ref,minOccurs,maxOccurs(不限制用unbounded),substitutionGroup,fixed,default
		3.	Attribute  声明一个属性 name/ref/type/use(required,optional)
		4.	Group  把一组元素声明组合在一起，以便它们能一起被复合类型使用  属性name/ref
		5.	attributeGroup  把一组属性声明组合在一起，以便可以被复合类型应用，属性：name/ref
		6.	simpleType 定义一个简单类型，它决定元素和属性值的约束和相关信息，属性name, 内容：应用已经存在的简单类型，三种方式：restrict 限定一个范围，list 从列表中选择，union 包含一个值的集合，从几个集合的合集中选择一个
		7.	simpleContext  应用于complexType 对它的内容进行约束和扩展，表示该complextType元素下不包括子元素,同时该complexType需要有属性
		8.	complexType  定义一个复合类型，决定一组元素和属性值相关信息。SimpleType类型的元素中不能包含子元素和属性，当需要定义的元素包含了子元素或属性时，必须要使用complexType.  当需要基于内置的基本数据类型定义一个新的数据类型时，用simpleType.
		9.	Choice  允许唯一的一个元素从一个组中被选择，属性minOccurs/maxOccurs
		10.	list
		11.	union
		12.	unique
		13.	sequence
		14.	restriction
