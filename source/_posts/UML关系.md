title: UML关系
date: 2012/10/20 11:52:08
updated: 2012/10/20 11:52:08
tags:
- 技术
- UML
categories: 技术辅助
---
#UML中的关系列表

1. 继承关系(Generalization)；
2. 实现关系(Realization)；
3. 依赖关系(Dependency)；
4. 关联关系(Association)；
5. 有方向的关联(DirectedAssociation)；
6. 聚合关系(Aggregation)；
7. 组合关系(Composition)；
8. 继承关系（Generalization）：

##继承关系（Generalization）
![类继承关系](/images/uml继承关系.jpeg)

Class B继承与Class A

继承指的是一个类（称为子类、子接口）继承另外的一个类（称为父类、父接口）的功能，并可以增加它自己的新功能的能力，继承是类与类或者接口与接口 之间最常见的关系之一；在Java中此类关系通过关键字extends明确标识，在设计时一般没有争议性；

##实现关系（Realization）
![实现关系图](/images/uml实现关系.gif)

Class A实现了Interface A
 
实现指的是一个class类实现interface接口（可以是多个）的功能；实现是类与接 口之间最常见的关系之一；在Java中此类关系通过关键字implements明确标识，在设计时一般没有争议性；

##依赖关系（Dependency）
![依赖关系图](/images/uml依赖关系.jpeg)

ClassA依赖于ClassB
 
可以简单的理解，就是一个类A使用到了另一个类B，而这种使用关系是具有偶然性的、临时性 的、非常弱的，但是B类的变化会影响到A；表现在代码层面，为类B作为参数被类A在某个method方法中使用；
##关联关系（Association）：
![关联关系图](/images/uml关联关系.jpeg)

ClassA与ClassB相互关联
 
这里的关联关系分的比较细，把相互关联和有方向的关联区分开了，相互他体现的是两个类、或者 类与接口之间语义级别的一种强依赖关系，是一种长期的稳定的关系；表现在代码层面，为被关联类以类属性的形式出现在关联类中，也可能是关联类引用了一个类 型为被关联类的全局变量；

##有方向的关联（DirectedAssociation）：
![有方向的关联图](/images/uml有方向的关联.jpeg)

ClassA关联于ClassB
 
是关联的一种特别形式，是单向的；表现在代码层面，为被关联类B以类属性的形式出现在关联类 A中，也可能是关联类A引用了一个类型为被关联类B的全局变量；

##聚合关系（Aggregation）：
![聚合关系图](/images/uml聚合关系.jpeg)

计算机 has-a cpu
 
聚合是关联关系的一种特例，他体现的是整体与部分、拥有的关系，即has-a的关系，此时整 体与部分之间是可分离的，他们可以具有各自的生命周期，部分可以属于多个整体对象，也可以为多个整体对象共享；比如计算机与CPU；表现在代码层面，和关 联关系是一致的，只能从语义级别来区分；

##组合关系（Composition）：
![组合关系、](/images/uml组合关系.jpeg)

孕妇 contains-a 胎儿
 
组合也是关联关系的一种特例，他体现的是一种contains-a的关系，这种关系比聚合更 强，也称为强聚合；他同样体现整体与部分间的关系，但此时整体与部分是不可分的，整体的生命周期结束也就意味着部分的生命周期结束；孕妇死了胎儿自然也就 死了；表现在代码层面，和关联关系是一致的，只能从语义级别来区分