---
title: 函数式编程思想&Lambda表达式
date: 2020-03-22 20:26:32
tags: [java学习,Lambda表达式]
categories: java学习
copyright: true
toc: true

---

# 函数式编程的思想

在数学中，**函数**就是有输入量、输出量的一套计算方案，也就是“拿什么东西做什么事情”。相对而言，面向对象过分强调“必须通过对象的形式来做事情”，而函数式思想则尽量忽略面向对象的复杂语法——**强调做什么，而不是以什么形式做**。

面向对象的思想:

​	做一件事情,找一个能解决这个事情的对象,调用对象的方法,完成事情.

函数式编程思想:

​	只要能获取到结果,谁去做的,怎么做的都不重要,重视的是结果,不重视过程

<!-- more -->

### 传统写法

当需要启动一个线程去完成任务时，通常会通过`java.lang.Runnable`接口来定义任务内容，并使用`java.lang.Thread`类来启动该线程。代码如下：

```java
	public class Demo01Runnable {
		public static void main(String[] args) {
	    	// 匿名内部类
			Runnable task = new Runnable() {
				@Override
				public void run() { // 覆盖重写抽象方法
					System.out.println("多线程任务执行！");
				}
			};
			new Thread(task).start(); // 启动线程
		}
	}
```



### 代码分析

对于`Runnable`的匿名内部类用法，可以分析出几点内容：

- `Thread`类需要`Runnable`接口作为参数，其中的抽象`run`方法是用来指定线程任务内容的核心；
- 为了指定`run`的方法体，**不得不**需要`Runnable`接口的实现类；
- 为了省去定义一个`RunnableImpl`实现类的麻烦，**不得不**使用匿名内部类；
- 必须覆盖重写抽象`run`方法，所以方法名称、方法参数、方法返回值**不得不**再写一遍，且不能写错；
- 而实际上，**似乎只有方法体才是关键所在**。


## 3.3 编程思想转换

### 做什么，而不是怎么做

我们真的希望创建一个匿名内部类对象吗？不。我们只是为了做这件事情而**不得不**创建一个对象。我们真正希望做的事情是：将`run`方法体内的代码传递给`Thread`类知晓。

**传递一段代码**——这才是我们真正的目的。而创建对象只是受限于面向对象语法而不得不采取的一种手段方式。那，有没有更加简单的办法？如果我们将关注点从“怎么做”回归到“做什么”的本质上，就会发现只要能够更好地达到目的，过程与形式其实并不重要。



## 3.4 体验Lambda的更优写法

借助Java 8的全新语法，上述`Runnable`接口的匿名内部类写法可以通过更简单的Lambda表达式达到等效：

```java
public class Demo02LambdaRunnable {
	public static void main(String[] args) {
		new Thread(() -> System.out.println("多线程任务执行！")).start(); // 启动线程
	}
}
```

这段代码和刚才的执行效果是完全一样的，可以在1.8或更高的编译级别下通过。从代码的语义中可以看出：我们启动了一个线程，而线程任务的内容以一种更加简洁的形式被指定。

不再有“不得不创建接口对象”的束缚，不再有“抽象方法覆盖重写”的负担，就是这么简单！



## Lambda表达式的标准格式


由三部分组成：
		a. 一些参数
		b. 一个箭头
		c. 一段代码

格式 ：
		（参数列表） -> {一些重要的方法的代码}

解释说明：  （）表达是接口中抽象方法的参数列表，没有参数就空着，有参数就写出参数，各个参数之间使用逗号分隔，箭头 -> 表示  把参数传递给方法体中()    。。大括号{} 中重写接口的抽象方法的方法体



## 带有参数和返回值的Lambda表达式

我们封装一个Person类，Person类中定义姓名和年龄属性，并封装get  set方法

我们进行重写Compartor接口中compare方法来对Person类按照年龄排序。


如果不使用Lambda表达式，我们使用匿名内部类的方式


```java

package LambdaExpress;

import java.util.Arrays;
import java.util.Comparator;

public class LambdaExp {
    public static void main(String[] args) {
        Person[] arr = new Person[]{
                new Person("小明",20),
                new Person("小华",18),
                new Person("小红",29),
        };

        /**
         * 按照从小到大进行排序
         * 升序排列 是前面的减去后边的
         */
        Arrays.sort(arr, new Comparator<Person>() {
            @Override
            public int compare(Person o1, Person o2) {
                return o1.getAge() - o2.getAge();
            }
        });

        for (Person person : arr) {
            System.out.println(person);
        }

    }

}


```


下面我们使用Lambda表达式


```java

package LambdaExpress;

import java.util.Arrays;
import java.util.Comparator;

public class LambdaExp {
    public static void main(String[] args) {
        Person[] arr = new Person[]{
                new Person("小明",20),
                new Person("小华",18),
                new Person("小红",29),
        };

        /**
         * 按照从小到大进行排序
         * 升序排列 是前面的减去后边的
         */

        Arrays.sort(arr,(Person o1, Person o2) -> {
            return o1.getAge() - o2.getAge();
        });

        for (Person person : arr) {
            System.out.println(person);
        }

    }

}


```


Lambda表达式：是可推到，可以省略。。
凡是根据上下文推导出来的内容，都可以省略书写
可以省略的内容：
1. （参数列表）：括号中参数列表的数据类型，可以省略不写
2. （参数列表）：括号中的参数如果只有一个，那么类型和() 都可以省略
3. （一些代码）：如果{}中的代码只有一行，无论是否有返回值，都可以盛烈（{}，return，分号）    需要注意的是：要省略{} return ，分号必须一起省略








## Lambda的使用前提


Lambda的语法非常简洁，完全没有面向对象复杂的束缚。但是使用时有几个问题需要特别注意：

1. 使用Lambda必须具有接口，且要求**接口中有且仅有一个抽象方法**。
   无论是JDK内置的`Runnable`、`Comparator`接口还是自定义的接口，只有当接口中的抽象方法存在且唯一时，才可以使用Lambda。
2. 使用Lambda必须具有**上下文推断**。
   也就是方法的参数或局部变量类型必须为Lambda对应的接口类型，才能使用Lambda作为该接口的实例。

> 备注：有且仅有一个抽象方法的接口，称为“**函数式接口**”。






## 函数式接口

函数式接口在Java中是指：有且仅有一个抽象方法的接口。

函数式接口，即适用于函数式编程场景的接口。。而Java中的函数式编程体现就是Lambda，所以函数式接口就是可以适用于Lambda使用的接口，只有确保接口中有且仅有一个抽象方法，Java中的Lambda才能顺利地进行推导





