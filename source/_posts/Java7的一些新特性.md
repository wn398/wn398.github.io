title: Java7的一些新特性
date: 2015/4/13 9:33:41 
update: 2015/4/13 9:33:46 
tags:
- 技术
- Java新特性
categories: JavaSE
---
#现代并发：
在代码块中被同步的是指在不同线程中表示被锁定对象的内存块。

##volatile
一个volatile域需遵循如下规则：

1. 线程所见的值在使用之前总会从主内存中再读出来
2. 线程所写的值总会在指令完成之前被刷回到主内存中。

volatile变量是真正线程安全的，但只有写入时不依赖当前状态（读取的状态）的变量才应该声明为volatile变量。对于要关注当前状态的变量，只能借助线程锁保证其绝对安全性。

##java.util.concurrent.lock
java.util.concurrent.locklock接口，有两个实现类

1. ReentrantLock  ------本质上跟用在同步块上那种锁是一样的，但它要稍微灵活点儿
2. ReentrantReadWriteLock ----在需要读取很多线程而写入很少线程时，用它性能会更好

##CountDownLatch
是一种简单的同步模式，这种模式允许线程在通过同步屏障之前做些少量准备工作。在构建新的ContDownLatch实例时要给它提供一个int值，此外，还有两个用来控制锁存器的方法：`countDown()`和`await()`.前者对计数器减1，而后者让调用线程在计数器到0之前一直等待。如果计数器为0或更小，则它什么也不做。

##ConcurrentHashMap
实现了ConcurrentMap接口，有些提供了原子操作的新方法。

1. putIfAbsent()----如果还没有对应键，则把键值对添加到HashMap中
2. remove()----如果对应键存在，且值也与当前状态相等，则用原子方式移除键值对
3. replace()----API为HashMap中原子替换的操作方法提供了两种不同的形式。

##CopyOnWriteArrayList
它通过增加写时复制语义来实现线程安全性，也就是修改列表的任何操作都会创建一个列表底层数组的新副本。

##Queue
队列经常用来在线程之间传递工作单元，这个模式通常适合用Queue最简单的并发扩展BlockingQueue来实现

1. BlockingQueue

	它有两个特性：在向队列中put()时，如果队列已满，它会让放入线程等待队列腾出空间,在从队列中take()时，如果队列为空，会导致取出线程阻塞。
	Java提供了BlockingQueue接口的两个实现：`LinkedBlockingQueue`和`ArrayBlockingQueue`。在已知队列的大小而能确定合适的边界时，用`ArrayBlockingQueue`非常高效，而`LinkedBlockingQueue`在某些情况下会快一点。

2. TransferQueue---Java7新贵

	它本质上是多了一项`transfer()`操作的BlockingQueue.如果接收线程处于等待状态，该操作会马上把工作项传给它。否则就会阻塞直到取走工作项的线程出现。也就是利用等待线程做一些事情，免得线程一直处于等待状态。
	用限定大小的阻塞队列也能达到这种调控效果，但TransferQueue接口更灵活。此外，用TransferQueue取代BlockingQueue的代码性能表现可能会更好。因为编写TransferQueue的实现已经将现代编译器和处理器的特性考虑在内，执行起来效率更高。Java7只给出TransferQueue的一种实现 链表式 `LinkedTransferQueue`

#分支合并框架
1. 引入了一种新的执行者服务，称为ForkJoinPool
2. ForkJoinPool 服务处理一种比线程“更小”的并发单元ForkJoinTask
3. ForJoinTask是一种由ForkJoinPool以更轻量化的方式所调度的对象
通常使用两种任务（尽管两种都表示为ForkJoinTask实例）：
"小型"任务是那种无需处理器耗费太多时间就可以执行的任务
"大型" 任务是那种需要在直接执行前进行分解（还可能不止一次）的任务

ForJoinTask,它从动作中返回结果的泛型类型。ForkJoinTasks由ForkJoinPool高度安排，ForkJoinPool是专为这些轻量任务设计的新型执行者。该服务维护每个线程的任务列表，并且当某个任务完成时，它能把挂在满负荷线程上的任务重新安排到空闲线程上去。  采用这种“工作窃取”的算法是为了解决大小不同的任务所导致的调度问题。

这里是一些可以用分支合并方法解决的问题：

1. 模拟大量简单对象的运动，比如粒子效果
2. 日志文件分析
3. 从输入中计数的数据操作，比如mapreduce操作

可以用下面的列表检查问题及其子任务是不是适合应用分支合并框架

1. 问题的子任务是否无需与其他子任务有显式的协作或同步也可以工作
2. 子任务是不是不会对数据进行修改，只是经过计算得出些结果
3. 对于子任务来说，分而治之是不是很自然的事？子任务是不是会创建更多的子任务，而且它们要比派生出它们的任务粒度更细？

#使用方法句柄
它是Java7中取得与反射API相同效果的新办法。 `java.lang.invoke`包，可以看作反射的现代化方式

1. MethodHandle
它是对可直接执行的方法（或域，构造方法等）的类型化引用，它是一个有能力安全调用方法的对象
2. MethodType
它是表示方法签名类型的不可变对象。每个方法句柄都有一个MethodType实例，用来指明方法的返回类型和参数类型
3. 查找方法句柄
	 
		public MethodHandle getToStringMH(){
	    MethodHandle mh;
	    MethodType mt = MethodType.methodType(String.class);
	    MethodHandles.Lookup lk = MethodHandles.lookup():
	
	    try{
	        mh = lk.findVirtual(getClass(),"toString",mt);
	    }catch(NoSuchMethodException|IllegalAccessException mhx){
	        throw ..
	    }
	    return mh;
		}