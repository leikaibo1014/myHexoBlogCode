---
title: CGLib代理
date: 2019-11-17 20:26:32
tags: [动态代理,CGLib代理]
categories: 设计模式
copyright: true
toc: true

---

上篇博客介绍了JDK的动态代理，但是JDK的动态代理机制只能代理实现了接口的类，也就是说如果一个类必须要有接口，这样我们才能使用JDK动态代理对这个类进行代理，而不能实现接口的类就不能实现JDK的动态代理，这样就存在一定的局限性，如果一个类没有实现接口，但是一些业务需要需要进行代理怎么办呢？对于这种情况，我们可以采用CGLIB代理来实现。

<!-- more-->



 cglib是针对类来实现代理的，其实现原理：CGLIB的底层采用ASM字节码生成框架，使用字节码技术生成代理，比使用反射生成代理的效果要高，是对指定的目标类生成一个子类，并覆盖其中方法实现增强。但是也有一点点不足，因为采用的是继承，所以不能对final修饰的类进行代理。 



在这一章节中，我们还是使用以前的代码，依然还是简单的三步来实现。

第一：建立一个普普通通的业务类；

第二：写CGLIB代理类；

第三：写测试代码或者客户端调用。

这里的不同是第一步中，我们不需要在建接口了，只是一个普普通通的java类。

编写需要进行代理的类：

UserInfo.java  这只是一个简单的未实现任何接口的类



\```java

```java
package com.proxyMoShi.CGLibProxy;

public class UserInfo {

    public void update() {
        System.out.println("这是更新操作");
    }


    public void query() {
        System.out.println("这是查询的方法");
    }
}

```

\```

我们编写cglib代理类

由于Cglib是第三方的API，所以需要导入相应的Jar包，因为自己是在学习Spring的时候，学习的Cglib代理，所以导入了Spring的核心jar文件，也就包括了Cglib代理的相关Jar文件。

\```java

```
import org.springframework.cglib.proxy.Enhancer;import org.springframework.cglib.proxy.MethodInterceptor;import org.springframework.cglib.proxy.MethodProxy;
```

\```



CGLib代理类



```java
package com.proxyMoShi.CGLibProxy;

import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class CglibProxy implements MethodInterceptor {
    //委托对象，运行时定类型
    private Object target;
    public Object getInstance(Object target) {
        this.target = target;
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(this.target.getClass());
        // 回调方法
        enhancer.setCallback(this);
        // 创建代理对象
        return enhancer.create();
    }

    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println("执行操作之前的日志");
        Object result = methodProxy.invokeSuper(o, objects);
        System.out.println("执行操作之后的日志操作");

        return result;
    }
}

```



通过以上代码，我们发现proxy.invokeSuper（obj,arg)是执行的关键。

​         使用CGLIB,需要实现 CGLib 给我们提供的 MethodInterceptor 实现类，并重写 intercept() 方法。方法中最后一个 MethodProxy 类型的参数 proxy，我们需要特别注意一下。CGLib 给我们提供的是方法级别的代理，也可以理解为对方法的拦截。

我们直接调用 proxy 的 invokeSuper() 方法，将被代理的对象 obj 以及方法参数 args 传入其中即可。 所以到这为止，我们的CGLIB代理类对需要代理的类进行代理的功能也就实现了。



 在代理类中，Enhancer、MethodInterceptor、MethodProxy这三个类和接口是我们实现Cglib动态代理的核心。

首先Enhancer类用于创建代理实例。负责整个代理对象的生命周期。其中setSuperclass方法用于设置被代理对象。也就是代理对象的父类。

setCallback方法用于设置回调。也就是对我们代理的方法转发到这个回调上。且所有回调类必须实现MethodInterceptor接口，并且实现intercept方法。在intercept方法中，传递了4个参数：

（1）Object obj ：被代理的源对象

（2）Method method ：被代理的对象调用的方法

（3）Object [] args：被调用方法的参数集合

（4）MethodProxy proxy：被调用方法的代理，可以和method完成相同的事情，但是由于内部使用了FastClass机制，而不是反射，所以效率更高。



最后一步是调用create方法,创建代理对象。



下面我们编写一个类对上面编写的类进行测试：

Client.java



```java

package com.proxyMoShi.CGLibProxy;

public class Client {
    public static void main(String[] args) {
        CglibProxy cglibProxy = new CglibProxy();
        UserInfo userinfo = (UserInfo) cglibProxy.getInstance(new UserInfo());
        userinfo.query();
    }
}


```

运行结果：

执行操作之前的日志

这是查询的方法

执行操作之后的日志操作

Process finished with exit code 0

从运行结果中，我么可以看到写的代理类是可以正常对类进行代理的。



\### 总结：



CGLIB是一个强大的高性能的代码生成包，它可以在运行期扩展Java类与实现Java接口。它广泛的被许多AOP的框架使用，例如Spring AOP，为他们提供方法的interception（拦截）。CGLIB包的底层是通过使用一个小而快的字节码处理框架ASM，来转换字节码并生成新的类。不鼓励直接使用ASM，因为它要求你必须对JVM内部结构包括class文件的格式和指令集都很熟悉。



Cglib代理构建的注意事项：我们在构建的过程中首先需要引入cglib – jar文件， 但是spring的核心包中已经包括了cglib功能，所以直接引入spring-core-3.2.5.jar即可。引入功能包后，就可以在内存中动态构建子类，代理的类不能为final， 否则报错。目标对象的方法如果为final/static, 那么就不会被拦截，即不会执行目标对象额外的业务方法。



