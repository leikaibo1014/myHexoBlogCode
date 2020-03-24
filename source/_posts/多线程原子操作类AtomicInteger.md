---
title: 多线程原子操作类AtomicInteger
date: 2020-03-21 20:28:32
tags: [java,多线程]
categories: java学习
copyright: true
toc: true

---

# 什么是原子操作类AtomicInteger

我们知道java并发机制中主要有三个特性需要我们去考虑，原子性、可见性和有序性。synchronized关键字可以保证可见性和有序性却无法保证原子性。而这个AtomicInteger的作用就是为了保证原子性。

AtomicInteger，一个提供原子操作的Integer的类。在Java语言中，++i和i++操作并不是线程安全的，在使用的时候，不可避免的会用到synchronized关键字。而AtomicInteger则通过一种线程安全的加减操作接口。

<!-- more -->

对于Java中的运算操作，例如自增或自减，若没有进行额外的同步操作，在多线程环境下就是线程不安全的。num++解析为num=num+1，明显，这个操作不具备原子性，多线程并发共享这个变量时必然会出现问题。


下面我们测试一下：
	
	```java
	public class AtomicintegerDemo {
	    private static  int count = 0;
	
	    public static void main(String[] args) {
	        Thread[] threads = new Thread[10];
	        for (int i = 0; i < 10; i++) {
	            threads[i] = new Thread(new Runnable() {
	                @Override
	                public void run() {
	                    for (int j = 0; j < 5; j++) {
	                        System.out.println(count++);
	
	                    }
	                }
	            });
	            threads[i].start();
	        }
	
	    }
	
	}
	
	```

我们定义了是10个线程，每个线程做五次加操作，如果程序正常并发执行的话，结果应该是0--50个数字，中间没有重复数字出现，但是运行结果显然不是，这个时候就出现了多线程的数据安全问题。

分析出现上述安全问题的原因，可能是我们没有对多线程共享的变量进行保证在多线程间的可见性，所以我们可以使用volatile关键字修饰

###  换成volatile修饰count变量


顺带说下volatile关键字很重要的两个特性：

1、保证变量在线程间可见，对volatile变量所有的写操作都能立即反应到其他线程中，换句话说，volatile变量在各个线程中是一致的（得益于java内存模型—"先行发生原则"）；


那么换成volatile修饰count变量后，会有什么效果呢？ 试一试:

还是上述的代码，我们对变量count使用volatile关键字进行修饰

	```java
		private static volatile int count = 0;
	```

输出结果好像还是与预期的不一致，这是为什么呢？并不能得出"基于volatile变量的运算在并发下是安全的"这个结论，，，此时，这个核心点在于在java的运算中，比如count++,++count操作并不是原子性的。


对于count++的操作，其实可以分解为3个步骤。

（1）从主存中读取count的值

（2）对count进行加1操作

（3）把count重新刷新到主存

这三个步骤在单线程中一点问题都没有，但是到了多线程就出现了问题了。比如说有的线程已经把count进行了加1操作，但是还没来得及重新刷入到主存，其他的线程就重新读取了旧值。因为才造成了错误。如何去解决呢？方法当然很多，但是为了和我们今天的主题对应上，很自然的联想到使用AtomicInteger。下面我们使用AtomicInteger重新来测试一遍：


我们对上面的类继续进行改造：


把上面的代码改造成AtomicInteger原子类型，先看看效果

	
	```java
	
	import java.util.concurrent.atomic.AtomicInteger;
	
	public class AtomicintegerDemo {
	    //private static  int count = 0;
	    private static AtomicInteger count = new AtomicInteger(0);
	
	    public static void main(String[] args) {
	        Thread[] threads = new Thread[10];
	        for (int i = 0; i < 10; i++) {
	            threads[i] = new Thread(new Runnable() {
	                @Override
	                public void run() {
	                    for (int j = 0; j < 5; j++) {
	                        System.out.println(count.incrementAndGet());
	
	                    }
	                }
	            });
	            threads[i].start();
	        }
	
	    }
	
	}
	
	
	```

最后通过运行结果我们发现，输出结果中并没有重复的数字，程序能够在多线程的状况下并发的执行，并能够正确的输出结果，这都归功于AtomicInteger.incrementAndGet()方法的原子性。


## 原理分析


在上面的例子中，我们使用AtomicInteger 中的incrementAndGet 函数来进行自增操作，代替直接使用count++来进行自增，保证了操作的原子性。

AtomicInteger类为我们提供了很多函数。

我们先看一下我们在上面使用的increasementAndGet函数，我们直接直接查看源码

	```java
	
	    /**
	     * Atomically increments by one the current value.
	     *
	     * @return the updated value
	     */
	    public final int incrementAndGet() {
	        return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
	    }
	
	```

在这里我们会看到，底层使用的是unsafe的getAndAddInt方法。这里你可能有一个疑问了，这个unsafe是个什么鬼，而且还有一个valueOffset参数又是什么，想要看明白，我们从源码的开头开始看起。

![](https://img-blog.csdnimg.cn/20200320160113766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xlbzExMjAxNzg1MTg=,size_16,color_FFFFFF,t_70)


开头在Unsafe的上面会发现，有一行注释叫做Unsafe.compareAndSwapInt。这又是什么？


（1） compareAndSwapInt的含义

compareAndSwapInt又叫做CAS，CAS 即比较并替换，实现并发算法时常用到的一种技术。CAS操作包含三个操作数——内存位置、预期原值及新值。执行CAS操作的时候，将内存位置的值与预期原值比较，如果相匹配，那么处理器会自动将该位置值更新为新值，否则，处理器不做任何操作。


对于CAS的解释我不准备长篇大论讲解。因为里面涉及到的知识点还是挺多的。在这里你理解了其含义就好。

（2）Unsafe的含义

在上面我们主要是讲解了CAS的含义，CAS修饰在Unsafe上面。那这个Unsafe是什么意思呢？

Unsafe是位于sun.misc包下的一个类，Unsafe类使Java语言拥有了类似C语言指针一样操作内存空间的能力，这无疑也增加了程序发生相关指针问题的风险。在程序中过度、不正确使用Unsafe类会使得程序出错的概率变大，使得Java这种安全的语言变得不再“安全”，因此对Unsafe的使用一定要慎重。

这里说一句题外话，在jdk1.9中，对Usafe进行了删除，所以因为这，那些基于Usafe开发的框架慢慢的都死掉了。

我们回到上面的源码中，继续进行说明，在这里也就是说，Usafe再进行getAndAddInt的时候，首先是先加1，然后对底层对象的地址做出了更改。这个地址是什么呢？这就是涉及到我们的第三个疑问参数了。

（3）valueOffset的含义

这个valueOffset是long类型的，代表的含义就是对象的地址的偏移量。下面我们重新解释一下这行代码。

unsafe.getAndAddInt(this, valueOffset, 1) + 1。这行代码的含义是，usafe通过getAndAddInt方法，对原先对象的地址进行了加1操作。现在应该明白了。我们return的时候，也是直接返回的最新的值。这一点我们对比另外一个方法incrementAndGet就能看出。

![](https://img-blog.csdnimg.cn/20200320160712960.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xlbzExMjAxNzg1MTg=,size_16,color_FFFFFF,t_70)


在这个方法的源代码中我们可以看到最后的+1操作没有了，也就是说，直接返回的是旧地址的值，然后再进行自增操作。如何去拿的地址的偏移量呢？是通过下面这个代码。

![](https://img-blog.csdnimg.cn/2020032016094369.png)

OK，到了这一步相信你已经知道了，usafe对a的值使用getAndAddInt方法进行了加1操作。然后返回最新的值。


对于AtomicInteger的原理就是这，主要是通过Usafe的方式来完成的。Usafe又是通过CAS机制来实现的，因此想要弄清整个原子系列的真正实现，就是要搞清楚CAS机制。


## 总结


对于jdk1.8的并发包来说，底层基本上就是通过Usafe和CAS机制来实现的。有好处也肯定有一个坏处。从好的方面来讲，就是上面AtomicInteger类可以保持其原子性。但是从坏的方面来看，Usafe因为直接操作的底层地址，肯定不是那么安全，而且CAS机制也伴随着大量的问题，比如说有名的ABA问题等等。






