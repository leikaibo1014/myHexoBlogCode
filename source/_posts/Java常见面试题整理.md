---
title: Java常见面试题整理
date: 2020-06-09 20:26:32
tags: [java,面试]
categories: 面试整理
copyright: true
toc: true

---

java常见面试题整理
<!-- more -->

<!-- more-->

##  java相关：

 

### Java中线程安全的集合类？

java中的线程安全是什么：

就是线程同步的意思，就是当一个程序对一个线程安全的方法或者语句进行访问的时候，其他的不能再对他进行操作了，必须等到这次访问结束以后才能对这个线程安全的方法进行访问

什么叫线程安全：

如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。 

或者说:一个类或者程序所提供的接口对于线程来说是原子操作或者多个线程之间的切换不会导致该接口的执行结果存在二义性,也就是说我们不用考虑同步的问题。

线程安全问题都是由全局变量及静态变量引起的。

若每个线程中对全局变量、静态变量只有读操作，而无写操作，一般来说，这个全局变量是线程安全的；若有多个线程同时执行写操作，一般都需要考虑线程同步，否则就可能影响线程安全。

 

看过vector源码的同学就会知道他的许多操作都是加了synchronized修饰的比如他的添加元素。

而HashMap的所有操作都没有加synchronized修饰 

 

### HashMap和hashtable的区别？

 

（1）HashMap是基于哈希表实现的，每一个元素是一个key-value对，其内部通过单链表解决冲突问题，容量不足（超过了阀值）时，同样会自动增长。

 HashMap是非线程安全的，只是用于单线程环境下，多线程环境下可以采用concurrent并发包下的concurrentHashMap。

 

#####  HashMap存数据的过程是：

 

   HashMap内部维护了一个存储数据的Entry数组，HashMap采用链表解决冲突，每一个Entry本质上是一个单向链表。当准备添加一个key-value对时，首先通过hash(key)方法计算hash值，然后通过indexFor(hash,length)求该key-value对的存储位置，计算方法是先用hash&0x7FFFFFFF后，再对length取模，这就保证每一个key-value对都能存入HashMap中，当计算出的位置相同时，由于存入位置是一个链表，则把这个key-value对插入链表头。

 

   HashMap中key和value都允许为null。key为null的键值对永远都放在以table[0]为头结点的链表中。

 

 Hashtable同样是基于哈希表实现的，同样每个元素是一个key-value对，其内部也是通过单链表解决冲突问题，容量不足（超过了阀值）时，同样会自动增长。

 

Hashtable也是JDK1.0引入的类，是线程安全的，能用于多线程环境中。

 

   Hashtable同样实现了Serializable接口，它支持序列化，实现了Cloneable接口，能被克隆。

 

 

###### 区别：

hashtable是线程安全的，hashMap是非线程安全的集合类

Hashtable中，key和value都不允许出现null值。但是如果在Hashtable中有类似put(null,null)的操作，编译同样可以通过，因为key和value都是Object类型，但运行时会抛出NullPointerException异常，这是JDK的规范规定的。

HashMap中，null可以作为键，这样的键只有一个；可以有一个或多个键所对应的值为null。当get()方法返回null值时，可能是 HashMap中没有该键，也可能使该键所对应的值为null。因此，在HashMap中不能由get()方法来判断HashMap中是否存在某个键， 而应该用containsKey()方法来判断。

 

 

### 3.Java继承中子类父类函数的调用问题？

 

 

在调用子类构造器之前，会先调用父类构造器，当子类构造器中没有使用"super(参数或无参数)"指定调用父类构造器时，是默认调用父类的无参构造器，如果父类中包含有参构造器，却没有无参构造器，则在子类构造器中一定要使用“super(参数)”指定调用父类的有参构造器，不然就会报错。

 

 

### 4. 类和对象的区别\****  ***\*？\*******\*请说明类和对象的区别\****

 

类是现实世界或思维世界中的实体在计算机中的反映，它将数据以及这些数据上的操作封装在一起。

对象是具有类类型的变量。类和对象是面向对象编程技术中的最基本的概念。

类是一个静态的概念，类本身不携带任何数据。当没有为类创建任何数据时，类本身不存在于内存空间  

 

对象是一个动e68a847a6431333366306464态的概念，每一个对象都存在着有别于其它对象的属于自己的独特的属性和行为，属性可以随着它自己的行为而发生改变。

 

 

### 5. Overload和Override的区别。Overloaded的方法是否可...



***\*请说明Overload和Override的区别，Overloaded的方法是否可以改变返回值的类型?\****

Overload是重载的意思，Override是覆盖的意思，也就是重写。

 

重载Overload表示同一个类中可以有多个名称相同的方法，但这些方法的参数列表各不相同（即参数个数或类型不同）。

 

重写Override表示子类中的方法可以与父类中的某个方法的名称和参数完全相同，通过子类创建的实例对象调用这个方法时，将调用子类中的定义方法，这相当于把父类中定义的那个完全相同的方法给覆盖了，这也是面向对象编程的多态性的一种表现。

 

### 6. 用两个栈实现队列\*******\*？\*******\*用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

 

```java

package niu;

import java.util.Stack;

public class doubleStack {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();

    //先写入栈函数，如果想往栈中放入什么函数，直接调用放入函数就可以了
    public void push(int a){
        stack1.push(a);
    }

    //写出栈函数，因为是两个栈，所以入栈stack1之后，如果要出栈，就需要借助stack2,
    //先把数值从stack1中出栈放入stack2中
    int result =0;
    int temp = 0;
    public int pop(){
        if (!stack2.empty()) {
            result = stack2.pop();
        }else {
            //如果栈2为空，就需要把数据一个一个取出来先放入栈2中
            while(!stack1.empty()){
                temp = stack1.pop();
                stack2.push(temp);
            }

            //重新取出栈2中的值
            if (!stack2.empty()) {
                result = stack2.pop();
            }
        }
        return result;
    }

}

```



 

## # 7. 介绍一下volatile？请你介绍一下volatile？

 

volatile作为java中的关键词之一，用以声明变量的值可能随时会别的线程修改，使用volatile修饰的变量会强制将修改的值立即写入主存，主存中值的更新会使缓存中的值失效(非volatile变量不具备这样的特性，非volatile变量的值会被缓存，线程A更新了这个值，线程B读取这个变量的值时可能读到的并不是是线程A更新后的值)。volatile会禁止指令重排 volatile具有可见性、有序性，不具备原子性。 注意，volatile不具备原子性，这是volatile与java中的synchronized、java.util.concurrent.locks.Lock最大的功能差异

 

 

### 8. 编程题

 

栈的压入、弹出序列

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

 

 

### 9. GCRoot可以是哪些\**** ***\*请问GC Root可以是哪些？

 

GcRoot是垃圾回收器算法中判断一个对象是否可以回收的一种算法。

就是对象到达GcRoot的路径是否还有可达，即是否有可引用链，如果有，这表明对象还存在着引用，如果没有，则表明该对象没有引用，在下一次垃圾回收时就会被回收

GcRoot的种类

 

（1）虚拟机栈：栈帧中的本地变量表引用的对象

（2）native方法引用的对象

（3）方法区中的静态变量和常量引用的对象

 

### 10. 编程题：\*******\*二叉树的深度\*******\*？\*******\*输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

 

 

```java

public int TreeDepth(TreeNode root){

  if(root == null)

   return 0;

  int leftDepth = TreeDepth(root.left);

  int rightDepth = TreeDepth(root.right);

  int result = 1+((leftDepth>rightDepth)?leftDepth:rightDepth);

  return result;

 

}

```



 

 

## 11. springIOC原理？自己实现IOC要怎么做，哪些步骤？

***\*请说明一下springIOC原理是什么？如果你要实现IOC需要怎么做？请简单描述一下实现步骤？\****

 

 

 

### 12. JDK和JRE有什么区别？

JDK是Java开发工具包，是Sun Microsystems针对Java开发员的产品。JDK中包含JRE，在JDK的安装目录下有一个名为jre的目录，里面有两个文件夹bin和lib，在这里可以认为bin里的就是jvm，lib中则是jvm工作所需要的类库，而jvm和 lib和起来就称为jre。JDK是整个JAVA的核心，包括了Java运行环境JRE（Java Runtime Envirnment）、一堆Java工具（javac/java/jdb等）和Java基础的类库（即Java API 包括rt.jar）。

 

JRE,是运行基于Java语言编写的程序所不可缺少的运行环境。也是通过它，Java的开发者才得以将自己开发的程序发布到用户手中，让用户使用。JRE中包含了Java virtual machine（JVM），runtime class libraries和Java application launcher，这些是运行Java程序的必要组件。与大家熟知的JDK不同，JRE是Java运行环境，并不是一个开发环境，所以没有包含任何开发工具（如编译器和调试器），只是针对于使用Java程序的用户。

JVM就是我们常说的java虚拟机，它是整个java实现跨平台的最核心的部分，所有的java程序会首先被编译为.class的类文件，这种类文件可以在虚拟机上执行。

 

### 13. . ==和equels有什么区别？

equals源自于Object类，Object中equals方法默认比较的内容还是==比较的结果。

如果没有重写equals的话，equals和==是一样的。都是比较内存地址。

 

### 14. 说说final在java中的作用？

final关键字可以用来修饰引用、方法、和类。

（1）如果修饰的引用为基本数据类型，则该引用为常量，该值无法修改

（2）如果修饰的引用为引用数据类型，比如对象、数组、则该对象、数组本身可以修改，但是指向该对象或者数组的地址的引用不能修改。

（3）如果引用是类的成员变量，则必须当场赋值，否则编译会报错。

当用final修饰一个方法的时候，当使用final修饰方法时，这个方法将成为最终方法，无法被子类重写。但是，该方法仍然可以被继承。

当使用final修饰一个类的时候，当用final修改类时，该类成为最终类，无法被继承。简称为“断子绝孙类”。

 

### 15.String类的常用方法有哪些?

 

（1）equals：字符串是否相同

（2）indexof：目标字符或者是目标字符串在源字符串中的位置下标

（3）valueOf：其他类型转字符串

（4）isEmpty：字符串的长度是否为零

（5）contains：是否包含目标字符串

 

 

### 16. java中操作字符串的类有哪些？他们之间有什么区别？

（1）String

（2）StringBuilder

（3）StringBuffer

 

从类的继承关系上来开的话,String和StringBuffer,StringBuilder是没有任何关系的,但是StringBuffer和StringBuilder的继承关系时一样的。

java中操作字符串的类,我知道的有三个类,分别是String,StringBuffer和StringBuilder.这三个类都是以char[]的形式保存的字符串,但是String类型的字符串是不可变的,对String类型的字符床做修改操作都是相当于重新创建对象.而对StringBuffer和StringBuilder进行增删操作都是对同一个对象做操作.StringBuffer中的方法大部分都使用synchronized关键字修饰,所以StringBuffer是线程安全的,StringBuilder中的方法则没有,线程不安全,但是StringBuilder因为没有使用使用synchronized关键字修饰,所以性能更高,在单线程环境下我会选择使用StringBuilder,多线程环境下使用StringBuffer.如果生命的这个字符串几乎不做修改操作,那么我就直接使用String,因为不调用new关键字声明String类型的变量的话它不会在堆内存中创建对象,直接指向String的常量池,并且可以复用.效率更高

 

### 17. 如何将字符串反转？

 

使用StringBuffer或者StringBuilder中的reverse()方法。

 

 

### 18. . 普通类和抽象类有什么区别？

（1）抽象类不能被实例化

（2）抽象类可以有抽象方法，抽象方法只需申明，无需实现

（3）含有抽象方法的类必须申明为抽象类

（4）抽象类的子类必须实现抽象类中所有抽象方法，否则这个子类也是抽象类

（5）抽象方法不能被声明为静态

（6）抽象方法不能用 private 修饰

（7）抽象方法不能用 final 修饰

 

 

### 19. 接口和抽象类有什么区别？

他们都不能实例化对象，都可以包含抽象方法，而且抽象方法必须被继承的类全部实现。

区别：

1、抽象类和接口都不能直接实例化，如果要实例化，抽象类变量必须指向实现所有抽象方法的子类对象，接口变量必须指向实现所有接口方法的类对象。

2、抽象类要被子类继承，接口要被类实现。

3、接口只能做方法申明，抽象类中可以做方法申明，也可以做方法实现

4、接口里定义的变量只能是公共的静态的常量，抽象类中的变量是普通变量。

5、抽象类里的抽象方法必须全部被子类所实现，如果子类不能全部实现父类抽象方法，那么该子类只能是抽象类。同样，一个实现接口的时候，如不能全部实现接口方法，那么该类也只能为抽象类。

6、抽象方法只能申明，不能实现，接口是设计的结果 ，抽象类是重构的结果

7、抽象类里可以没有抽象方法

8、如果一个类里有抽象方法，那么这个类只能是抽象类

9、抽象方法要被实现，所以不能是静态的，也不能是私有的。

10、接口可继承接口，并可多继承接口，但类只能单根继承。

 

 

### 20.  java中的IO流分为几种？

java 中 IO 流分为几种?

 

按照流的流向分，可以分为输入流和输出流；

按照操作单元划分，可以划分为字节流和字符流；

按照流的角色划分为节点流和处理流。

 

### 21. List Set  Map 之间有什么区别？

 

List和set是实现了collection接口的

 

#### List：

- 1.可以允许重复的对象。

- 2.可以插入多个null元素。

- 3.是一个有序容器，保持了每个元素的插入顺序，输出的顺序就是插入的顺序。

- 4.常用的实现类有 ArrayList、LinkedList 和 Vector。ArrayList 最为流行，它提供了使用索引的随意访问，而 LinkedList 则对于经常需要从 List 中添加或删除元素的场合更为合适。

 

 

#### Set： 

- 1.不允许重复对象
- 无序容器，你无法保证每个元素的存储顺序，TreeSet通过 Comparator  或者 Comparable 维护了一个排序顺序。
- 只允许一个 null 元素
- Set 接口最流行的几个实现类是 HashSet、LinkedHashSet 以及 TreeSet。最流行的是基于 HashMap 实现的 HashSet；TreeSet 还实现了 SortedSet 接口，因此 TreeSet 是一个根据其 compare() 和 compareTo() 的定义进行排序的有序容器。

 

 

#### Map：

- 1. Map不是collection的子接口或者实现类。Map是一个接口。
  2. Map 的 每个 Entry 都持有两个对象，也就是一个键一个值，Map 可能会持有相同的值对象但键对象必须是唯一的。
  3. TreeMap 也通过 Comparator  或者 Comparable 维护了一个排序顺序。
  4. Map 里你可以拥有随意个 null 值但最多只能有一个 null 键。
  5. 5.Map 接口最流行的几个实现类是 HashMap、LinkedHashMap、Hashtable 和 TreeMap。（HashMap、TreeMap最常用）

 

#### 什么场景下使用list，set，map呢？

（为什么这里要用list、或者set、map，这里回答它们的优缺点就可以了）

 

- （1）如果你经常会使用索引来对容器中的元素进行访问，那么 List 是你的正确的选择。如果你已经知道索引了的话，那么 List 的实现类比如 ArrayList 可以提供更快速的访问,如果经常添加删除元素的，那么肯定要选择LinkedList。

- （2）如果你想容器中的元素能够按照它们插入的次序进行有序存储，那么还是 List，因为 List 是一个有序容器，它按照插入顺序进行存储。

- （3）如果你想保证插入元素的唯一性，也就是你不想有重复值的出现，那么可以选择一个 Set 的实现类，比如 HashSet、LinkedHashSet 或者 TreeSet。所有 Set 的实现类都遵循了统一约束比如唯一性，而且还提供了额外的特性比如 TreeSet 还是一个 SortedSet，所有存储于 TreeSet 中的元素可以使用 Java 里的 Comparator 或者 Comparable 进行排序。LinkedHashSet 也按照元素的插入顺序对它们进行存储。

- （4）如果你以键和值的形式进行数据存储那么 Map 是你正确的选择。你可以根据你的后续需要从 Hashtable、HashMap、TreeMap 中进行选择。

 

 

### 1.JDK和JRE有什么区别？

JDK是Java开发工具包，是Sun Microsystems针对Java开发员的产品。JDK中包含JRE，在JDK的安装目录下有一个名为jre的目录，里面有两个文件夹bin和lib，在这里可以认为bin里的就是jvm，lib中则是jvm工作所需要的类库，而jvm和 lib和起来就称为jre。JDK是整个JAVA的核心，包括了Java运行环境JRE（Java Runtime Envirnment）、一堆Java工具（javac/java/jdb等）和Java基础的类库（即Java API 包括rt.jar）。

 

JRE,是运行基于Java语言编写的程序所不可缺少的运行环境。也是通过它，Java的开发者才得以将自己开发的程序发布到用户手中，让用户使用。JRE中包含了Java virtual machine（JVM），runtime class libraries和Java application launcher，这些是运行Java程序的必要组件。与大家熟知的JDK不同，JRE是Java运行环境，并不是一个开发环境，所以没有包含任何开发工具（如编译器和调试器），只是针对于使用Java程序的用户。

JVM就是我们常说的java虚拟机，它是整个java实现跨平台的最核心的部分，所有的java程序会首先被编译为.class的类文件，这种类文件可以在虚拟机上执行。

 

### ==和equels有什么区别？\****

equals源自于Object类，Object中equals方法默认比较的内容还是==比较的结果。

如果没有重写equals的话，equals和==是一样的。都是比较内存地址。

 

### 说说final在java中的作用？

final关键字可以用来修饰引用、方法、和类。

- （1）如果修饰的引用为基本数据类型，则该引用为常量，该值无法修改

- （2）如果修饰的引用为引用数据类型，比如对象、数组、则该对象、数组本身可以修改，但是指向该对象或者数组的地址的引用不能修改。

- （3）如果引用是类的成员变量，则必须当场赋值，否则编译会报错。

当用final修饰一个方法的时候，当使用final修饰方法时，这个方法将成为最终方法，无法被子类重写。但是，该方法仍然可以被继承。

- 当使用final修饰一个类的时候，当用final修改类时，该类成为最终类，无法被继承。简称为“断子绝孙类”。

 

### 4. String类的常用方法有哪些?

 

- （1）equals：字符串是否相同

- （2）indexof：目标字符或者是目标字符串在源字符串中的位置下标

- （3）valueOf：其他类型转字符串

- （4）isEmpty：字符串的长度是否为零

- （5）contains：是否包含目标字符串

 

 

### 5. java中操作字符串的类有哪些？他们之间有什么区别？

（1）String

（2）StringBuilder

（3）StringBuffer

 

从类的继承关系上来开的话,String和StringBuffer,StringBuilder是没有任何关系的,但是StringBuffer和StringBuilder的继承关系时一样的。

java中操作字符串的类,我知道的有三个类,分别是String,StringBuffer和StringBuilder.这三个类都是以char[]的形式保存的字符串,但是String类型的字符串是不可变的,对String类型的字符床做修改操作都是相当于重新创建对象.而对StringBuffer和StringBuilder进行增删操作都是对同一个对象做操作.StringBuffer中的方法大部分都使用synchronized关键字修饰,所以StringBuffer是线程安全的,StringBuilder中的方法则没有,线程不安全,但是StringBuilder因为没有使用使用synchronized关键字修饰,所以性能更高,在单线程环境下我会选择使用StringBuilder,多线程环境下使用StringBuffer.如果生命的这个字符串几乎不做修改操作,那么我就直接使用String,因为不调用new关键字声明String类型的变量的话它不会在堆内存中创建对象,直接指向String的常量池,并且可以复用.效率更高

 

### 6. 如何将字符串反转？

 

使用StringBuffer或者StringBuilder中的reverse()方法。

 

 

### 7. 普通类和抽象类有什么区别？

（1）抽象类不能被实例化

（2）抽象类可以有抽象方法，抽象方法只需申明，无需实现

（3）含有抽象方法的类必须申明为抽象类

（4）抽象类的子类必须实现抽象类中所有抽象方法，否则这个子类也是抽象类

（5）抽象方法不能被声明为静态

（6）抽象方法不能用 private 修饰

（7）抽象方法不能用 final 修饰

 

 

### 8. 接口和抽象类有什么区别？

### 9. java中的IO流分为几种？

### 10. List Set  Map 之间有什么区别？

 

### 11. java有8种基本类型，请问byte、int、long、char、float、double、boolean各占多少个字节？\

byte：8位 一个字节 

int：32位 四个字节 

long：64位 八个字节 

char：16位 两个字节 

float:32位 四个字节 

double：64位 八个字节 

boolean：8位 一个字节

 

### 12. 下面哪个不属于HttpServletResponse接口完成的功能？

 

HttpServletResponse完成：设置http头标，设置cookie，设置返回数据类型，输出返回数据；读取路径信息是HttpServletRequest做的

 

HttpServletResponse 在Servlet中，当服务器响应客户端的一个请求时，就要用到HttpServletResponse接口。

设置响应的类型可以使用setContentType()方法。发送字符数据，可以使用getWriter()返回一个对象。下表是接口HttpServletResponse的常用方法。 

接口HttpServletResponse的常用方法 方 法 说 明 

addCookie(Cookie cookie) 将指定的Cookie加入到当前的响应中 

addHeader(String name,String value) 将指定的名字和值加入到响应的头信息中 

containsHeader(String name) 返回一个布尔值，判断响应的头部是否被设置 

encodeURL(String url) 编码指定的URL 

sendError(int sc) 使用指定状态码发送一个错误到客户端 

sendRedirect(String location) 发送一个临时的响应到客户端 

setDateHeader(String name,long date) 将给出的名字和日期设置响应的头部 

setHeader(String name,String value) 将给出的名字和值设置响应的头部 

setStatus(int sc) 给当前响应设置状态码 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 