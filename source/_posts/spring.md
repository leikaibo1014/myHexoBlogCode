---
title: spring整理
date: 2020-06-09 20:26:32
tags: [java,面试，阿里]
categories: 面试整理
copyright: true
toc: true
---



### 1. spring两个核心容器对象的区别

1. ApplicationContext

它在构建核心容器的时候，采用的创建对象的策略是立即加载的方式，也就是说，只要一读取配置文件马上就创建配置文件中配置的对象、



 <!-- more -->



2. BeanFactory

它在构建核心容器时，创建对象采用的策略是采用延迟加载的方式，也就是说，什么时候根据id获取对象了，什么时候才真正的创建对象。



### 2. spring对bean的管理细节



#### 创建bean的三种方式

1. 第一种方式，使用默认构造函数创建

   在spring的配置文件中使用bean标签，配以id 和class属性之后，且没有其他属性和标签时，采用的就是默认构造函数创建bean对象，此类如果类中没有默认构造函数，则对象无法创建

   ```java
   <!--这是第一种方式，直接使用默认构造函数的方式进行创建-->
       <!--<bean id="userService" class="lei.study.service.impl.userServiceImpl">
   ```

   

2. 使用普通工厂中的方法创建对象，（使用某个类中的方法创建对象，并存入spring容器中  ）

创建需要实例化的工厂类

```java
public IUserService getUserServiceInstance(){
        return new userServiceImpl();
    }
```

spring 容器中进行配置

  ```java
<bean id="factoryInstance" class="lei.study.factory.factoryInstance"></bean>
    <bean id="userService" factory-bean="factoryInstance" factory-method="getUserServiceInstance"></bean>
  ```



3. （工厂中有可能是一个普通方法还可能是一个静态方法，这是时候使用）使用工厂中的静态方法创建对象，使用某个类中的静态方法创建对象，并存入spring容器中

静态方法中需要创建对象

```java
public class factoryStaticInstance {
    public static IUserService getUserServiceInstance(){
        return new userServiceImpl();
    }
}
```



```java
<!--刚才的第二种创建中，可能不是一个普通类，而是一个静态的类-->
    <bean id="userService" class="lei.study.factory.factoryStaticInstance" factory-method="getUserServiceInstance"></bean>

```



#### 3. bean对象的作用范围

<bean标签的作用范围

bean标签的scope属性：



作用：用于指定bean的作用范围

取值：（常用的就是singleton和protopyte）

singleton：为默认值，是单例的

protopyte： 多例的

request：作用于web应用的请求范围

session：作用于web应用的会话范围

global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境是作用就是session



#### 4. bean的生命周期



当创建对象配置bean标签中的内容的时候，我们 可以指定bean的生命周期，当配置的是单例模式的时候，我们使ApplicationContext创建容器的时候，对象就创建了，当我们关闭对象的时候，对象销毁。



当配置的是单例模式的时候，我们使BeanFactory创建容器的时候，对象不会创建，只有当我们使用的时候，才会创建对象，，什么时候对象销毁呢，针对多例模式，spring容器不提供销毁对象的方法，把对象的销毁交给java的垃圾回收机制进行回收。





**单例对象：**

出生：当容器创建时，对象出生

活着：当容器还在，对象一直活着

死亡：当容器销毁，对象消亡

总结：单例对象的声明周期和容器相同



**多例对象：**

 出生：当我们使用对象时，spring对象为我们创建

活着：对象只要是在使用过程中，就一直活着

死亡：当对象长时间不用，且没有别的对象引用时，由java的垃圾回收器进行回收



### 5. spring的依赖注入



IOC的作用，就是降低程序间的耦合关系，也叫做依赖关系



依赖关系的管理：以后都交给spring来进行维护

在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中进行说明。



依赖关系的维护就称之为依赖注入



依赖注入：



能注入的数据，有三类

（1） 基本类型和string

（2）其他bean类型（在配置文件中或者是注解中配置过的bean）

（3）其他类型/集合类型



**依赖注入的方式有三种：**

**（1）第一种：使用构造函数提供**

**（2）第二种：使用set方法提供**

**（3）第三种：使用注解 提供**



##### 1. 使用构造函数提供注入



构造函数注入，需要在bean标签内，使用constructor-arg标签，标签出现的位置是bean标签的内部，、



标签中的属性：

​		type：用户指定要注入的数据的数据类型，该数据类型也是构造函数中某个或者是某些参数的类型

​		index：用于指定要注入的数据给构造函数中指定索引位置的参数赋值，索引的位置是从0开始的

​		name：用于指定给构造函数中指定名称的参数赋值

=============最常用的是使用name的方式，给指定的参数进行赋值，=============以上三种都是用于指定给构造函数找那个的那个参数进行赋值

​	    value：用户提供基本类型和string类型的数据

​		ref： 用于指定其他的bean类型，指的就是在spring的IOC核心容器中，出现过的bean对象。



构造函数的特点与优势：

​	在获取bean对象时，注入数据是必须的操作，否则对象是无法创建成功的。当我们使用的某个类的时候，就想使用某些数据的时候，我们就可以使用构造函数注入的方式，使用这种方式更简单。



弊端：

​		改变了bean对象的实例化方式，使我们在创建对象的时候，如果用不到这些数据，也必须提供。当我们在开发中除非非要采用这种方式进行创建，一般的时候我们是不使用的。

例如：

我们在userServiceImpl中写生需要注入的对象

```java
public class userServiceImpl implements IUserService {

    private String name;
    private Integer age;
    private Date brithday;

    public userServiceImpl(String name, Integer age, Date brithday) {
        this.name = name;
        this.age = age;
        this.brithday = brithday;
    }

    public void saveUser() {
        System.out.println("userServiceImpl中的save方法执行了"+name+","+age+","+brithday);
    }
}
```



然后在spring的配置文件中，进行依赖的注入

```xml
    <!--依赖注入的方式-->
    <bean id="userService2" class="lei.study.service.impl.userServiceImpl">
        <constructor-arg name="name" value="第一个"/>
        <constructor-arg name="age" value="19"/>
        <constructor-arg name="brithday" ref="dataNow"/>
    </bean>
    <bean id="dataNow" class="java.util.Date"></bean>
```

编写测试代码

```java
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        System.out.println(ac);
        IUserService userService = (IUserService) ac.getBean("userService2");
        userService.saveUser();
    }
```



执行结果：

userServiceImpl中的save方法执行了第一个,19,Sun May 17 15:23:41 CST 2020

说明注入成功



引入：

在进行spring配置文件的编写的时候，需要在配置文件中引入约束，spring容器中相对完整的约束如下：

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd
            http://www.springframework.org/schema/mvc
            http://www.springframework.org/schema/mvc/spring-mvc.xsd
            http://www.springframework.org/schema/tx
            http://www.springframework.org/schema/tx/spring-tx.xsd
            http://www.springframework.org/schema/aop
            http://www.springframework.org/schema/aop/spring-aop.xsd "
       default-autowire="byName">




```







###  2. set方法注入(经常使用的方式)



set方式注入，设计的标签：property

出现的位置，bean标签的内部

标签的属性：

​		name：用于指定注入时所调用的set方法名称

​	    value：用户提供基本类型和string类型的数据

​		ref： 用于指定其他的bean类型，指的就是在spring的IOC核心容器中，出现过的bean对象。



**set注入的优点：**

​		创建的对象的时候没有明确的限制，可以直接使用默认构造函数，然后通过property 找到对应的set方法名称，进行一些取值的注入即可

**set注入的缺点：**

​	如果有某个成员必须有值，则获取对象时，有可能set方法没有执行。



例如：

```java
public class userServiceImpl implements IUserService {

    private String name;
    private Integer age;
    private Date brithday;

    /**
     * setter方式注入的方式
     */

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public void setBrithday(Date brithday) {
        this.brithday = brithday;
    }


    public void saveUser() {
        System.out.println("userServiceImpl中的save方法执行了"+name+","+age+","+brithday);
    }
}
```



在spring中的配置文件中

```xml
    <!--setter方式注入的方式-->
    <bean id="userService3" class="lei.study.service.impl.userServiceImpl">
        <property name="name" value="哈哈哈"/>
        <property name="age" value="18"/>
        <property name="brithday" ref="dataNow"/>
    </bean>
```



在测试文件中

```java
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        System.out.println(ac);
        IUserService userService = (IUserService) ac.getBean("userService3");
        userService.saveUser();
    }
```



##### 使用set注入 复杂数据类型/集合类型的数据结构



用于给List结构集合注入的标签有：list  array    set

用户给Map结构集合注入的标签：map   props



注意：结构相同，可以互换



###### =========================================================



## Spring基于注解的IOC以及IOC的案例

注意： 在本节中我们学习使用注解进行依赖的主注入，在上面的案例中，我们使用的是配置文件的方式进行配置的。

我们学习了---------

用于创建对象

用于注入数据

用于改变作用范围的

和生命周期相关

等等相关的配置



**当我们使用注解之后，**

**用户创建对象的注解**：他们的作用就和在XML配置文件中，编写一个<bean 标签实现的功能是一样的

**用于注入数据的**：他们的作用就和在xml配置文件中的bean 标签中写一个property标签的作用是一样的

**用于改变作用范围的**：他们的作用就和在bean标签中使用scope属性实现的功能是一样的

**和生命周期相关**：他们的作用就和在bean标签中使用  init-method 和destory-method的作用是一样的



**(1) 创建对象的注解：**

**①    @Component    作用在类上**

作用：就是把当前类对象存入spring容器中

属性：value：用于指定bean的id。当我们不写时，它的默认值是当前类名，且首字母小写



**注意：使用这个注解之前，我们需要在spring的配置文件中，加上一个说明，告知spring在创建容器的时候，需要扫描的包，配置所需要的标签不是在beans的约束中，而是一个名称为context名称空间和约束中**

```xml
<!--告诉spring容器需要扫描的包-->
    <context:component-scan base-package="lei.study"/>
```

```java
@Component(value = "haha")
public class userServiceImpl2 implements IUserService {

    public void saveUser() {
        System.out.println("userServiceImpl中的save方法执行了");
    }
}
```

测试

```java
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        IUserService userService = (IUserService) ac.getBean("haha");
        userService.saveUser();
    }
```



**②   @ Controller        @Service      @Repository   三个注解**

以上三个注解他们的作用和属性与Component是一模一样的。他们三个是spring框架，为我们明确提供的三层对象使用的注解，使三层对象更加清晰

 **@ Controller**  一般使用在表现层

 **@Service**      一般使用的service层

**@Repository**  一般使用在持久层

 

 **（2）注入数据的注解**

**@Autowired :** 

出现位置：可以使变量上，也可以是方法上

细节：在使用注解注入数据的时候，set方法就不是必须的了



作用就是，自动按照类型注入（例如   @Autowired   在这上面注解 private  IUserDao  userDao = null，那么就会按照IUserDao  类型 ，去spring容器中进行寻找对象取值），只要容器中有唯一的一个bean 对象类型和要注入的变量类型匹配，就可以注入成功。如果IoC容器中没有任何bean的类型和要注入的变量类型匹配，则报错。

如果有多个类型相互匹配的时候，就会自动按照名称(userDao 这个名称)进行找对象bean对象，如果名称不一致或者是不存在，就会报错。

例如我们定义了两个userDaoImpl方法实现了同一个接口，并在每一个方法上面使用@Component注解生成对象到容器中

第一个

```java
@Component(value = "userDao1")
public class userDaoImpl implements IUserDao {
    public void saveUser() {
        System.out.println("saveUser1方法执行了,保存了用户1111111");
    }
}
```

第二个

```java
@Component(value = "userDao2")
public class userDaoImpl2 implements IUserDao {
    public void saveUser() {
        System.out.println("saveUser1方法执行了,保存了用户222222");
    }
}
```



，当我们在service层取值调用bean的时候

```java
 	@Autowired
    private IUserDao userDao;
    public void saveUser() {
        userDao.saveUser();
    }
```

这个时候按照类型IUserDao寻找bean的时候就会找到两个bean（因为@Autowired是按照类型进行寻找的），程序会报错，这个时候我们就可以和@Qualifer注解进行配合使用





 **这个时候出现了@Qualifer注解**

**@Qualifer**

作用：在按照类中注入的基础之上再按照名称进行注入，它在给类成员注入时不能单独使用，但是在给方法参数注入时可以单独使用。

属性：value 用于指定注入bean的id

例如：

```java
    @Autowired
    @Qualifier(value = "userDao2")
    private IUserDao userDao;
    public void saveUser() {
        userDao.saveUser();
    }
```



**还有一个注解：**

**@Resource**

@Recource 作用是，直接按照bean的id注入，它可以独立使用

属性：name   ：用于指定bean的id



```java
   @Resource(name = "userDao2")
    private IUserDao userDao;
    public void saveUser() {
        userDao.saveUser();
    }
```



**需要注意的是：以上三种注解都只能注入其他bean类型的数据，而基本类型和String类型无法使用上述注解实现。另外，集合类型的注入只能通过XML来实现。**



**@Value注解**

作用：用于注入基本类型和String类型的数据

属性：value---用于指定数据的值，它可以使用spring中的spEl（也就是spring中的EL表达式）

​			spEl的写法 ：   ${表达式}





**（3）用于改变作用范围的注解**



他们的作用就和在bean标签中使用scope属性实现的功能是一样的

Scope：作用---用于指定bean的作用范围

属性：value：指定范围的取值，常用取值  single    prototype   分别对应单例和多例



**（4）和生命周期相关的注解**

 他们的作用就和在bean标签中使用init-method和destory-method的作用是一样的

@PreDestory   作用：用于指定销毁方法

@PostConstruct    作用：用于指定初始化方法





### ==================================================



  # Spring的新注解



@Configuration   

  				作用》：指定当前类是一个配置类	

​				细节：当配置类作为AnnotationConfigApplicationContext对象创建的参数时，该注解可以不写

@ComponentScan 

​				作用》：用于通过注解指定spring在创建容器时要扫描的包

​				属性：   value：它和basePackages的作用是一样的，都是用于指定创建容器时要扫描的包，我们使用这个注解就相当于在spring的配置文件xml中使用了  <context:component-scan base-package="lei.study"/>  进行了一个包扫描的作用



@Bean注解

​			作用》：用于把当前方法的返回值作为bean对象存入spring的Ioc容器中

​			属性：  name  用于指定bean的id，当不写时，默认值是当前方法的名称

​			细节：当我们使用注解配置方法时，如果方法有参数，spring框架会去容器中查找有没有可用的bean对象

​						查找的方式和Autowired注解的作用是一样的



@Import 
				作用》：用于导入其他的配置类

​				属性：value   用于指定其他配置类的字节码			

​							当我们使用Import的注解之后，有Import注解的类是父配置类，而导入的都是子配置类



@PropertySource   

​				作用：用于指定properties文件的位置

​							关键字 ： classpath   表示类路径下





## ==========================================

### Spring中的AOP配置



spring中基于XML的AOP配置步骤：

1. 把通知Bean也交给spring容器来进行管理

2. 使用aop:config标签表明开始AOP的配置

3. 使用aop：aspect标签表明配置切面

   ​					id属性：是给切面提供一个唯一标识

   ​					ref属性：是指定通知类bean的id

4. 在aop：aspect标签的内部使用对应标签来配置通知的类型

   ​				我们现在是让我们定义的切面方法在切入点方法执行之前执行，所以是前置通知

   ​				aop：before：表示配置前置通知

   ​			    method属性：用于指定Logger类中哪个方法是前置通知

   ​				pointcut属性：用于指定切入点表达式，改表达式的含义指的是对业务层中哪些方法进行增强

   ​		切入点表达式的写法：

   ​				关键字：execution（表达式）

   ​				表达式：

   ​								访问修饰符   返回值    包名.包名.包名.....类名.方法名（参数列表）

   ​				标准的表达式写法  execution(public void lei.study.service.impl.userServiceImpl.saveUser()

   

   例如我们建立一个切入方法，就是打印日志信息，建立一个日志类，然后写一些方法

   例如：

   ```java
   /**
    * 需要注入切入点的方法，也就是在切入点之前或者之后需要执行的方法
    */
   public class Logger {
       public void printLogger(){
           System.out.println("打印日志的方法，开始打印日志。。。");
       }
   }
   ```

   

   把上面的方法注入到userservice中

   userServiceImpl.java

   ```java
   public class userServiceImpl implements IUserService {
       public void saveUser() {
           System.out.println("userServiceImpl保存用户的操作执行了");
       }
   }
   ```

我们想把日志信息注入到saveUser（）方法之前或者是之后执行，我们就可以在spring的配置文件中，进行AOP切面的一些配置，就可以使用AOP完成相关的切面编程

在spring的配置文件中：

```xml
<bean id="userService" class="lei.study.service.impl.userServiceImpl"></bean>

<bean id="printLogger" class="lei.study.utils.Logger"></bean>

<aop:config>
    <aop:aspect id="logAdvice" ref="printLogger">
        <!--建立切入点方法的关联-->
        <aop:before method="printLogger" pointcut="execution(public void lei.study.service.impl.userServiceImpl.saveUser())"></aop:before>
    </aop:aspect>
</aop:config>
```



在这里我们需要注意切入点表达式的写法

表达式：

​			访问修饰符   返回值    包名.包名.包名.....类名.方法名（参数列表）

​			标准的表达式写法  execution(public void lei.study.service.impl.userServiceImpl.saveUser()



全通配写法

​			表达式的全通配写法    

```xml
 表达式的全通配写法
* *..*.*(..) 
```



解释说明：在上面的方法中，我们只对userService类中一个saveUser（）方法进行了增强，如果我们需要使用这个日之类对所有的userService中的方法都需要增强，我们不需要一个一个方法的进行编写，只需要写一个表达式的全通配写法   表达式的全通配写法           



**注意：**

​		在标准写法中，

**访问修饰符可以省略** ：  public void lei.study.service.impl.userServiceImpl.saveUser() 中的public可以省略，写成  void lei.study.service.impl.userServiceImpl.saveUser()

**返回值可以使用通配符**：表示任意返回值类型

```xml
 * lei.study.service.impl.userServiceImpl.saveUser()
```



**包名可以使用通配符：**表示任意包。但是有几级包，就需要写几个*

```java
 * *.*.*.*.userServiceImpl.saveUser()
```



**包名可以使用..表示当前包及其子包**：

```java
 * *..userServiceImpl.saveUser()
```



**包名和方法名都可以使用*来实现通配：**

```java
 * *..*.*()
```



**参数列表：**

可以直接写数据类型

​			基本类型直接写名称   int

​			引用类型写包.类名的方式   java.lang.String		

可以使用通配符表示任意类型，但是必须有参数

可以使用..表示有无参数均可，有参数可以使任意类型

全通配写法：

```java
 表达式的全通配写法
* *..*.*(..) 
```



**实际开发中：**

​	实际开发中切入点表达式的通常写法：

​			我们可以切到业务层实现类下的所有方法

```java
 * lei.study.service.impl.*.*(..)
```





在上面的例子中我们可以使用如下的写法：

```java
    <!--
        表达式的全通配写法
                    * *..*.*(..)
    -->
    <aop:config>
        <aop:aspect id="logAdvice" ref="printLogger">
            <!--建立切入点方法的关联-->
            <aop:before method="printLogger" pointcut="execution(* *..*.*(..))"></aop:before>
        </aop:aspect>
    </aop:config>
```





**通知的类型：**



**前置通知**：在切入点方法执行之前执行

**后置通知**：在切入点方法正常执行之后执行，也和异常通知永远只能执行一个 

**异常通知**：在切入点方法执行产生异常之后执行，它和后置通知永远只能执行一个

**最终通知**：无论切入点方法是否正常执行它都会在其后面执行。



**环绕通知：**当我们配置了环绕通知之后，切入点方法没有执行，而通知方法执行了。







切面的配置：



需要注入切入点的方法

```java
public class Logger {
    public void beforePrint(){
        System.out.println("前置通知，打印日志的方法，开始打印日志。。。在切入点方法执行之前执行");
    }
    public void afterPrint(){
        System.out.println("后置通知，结束打印日志。。。");
    }

    public void printThrow(){
        System.out.println("切入点方式产生异常之后，执行的异常通知");
    }

    public void afterLogger(){
        System.out.println("这是最终通知， 无论切入点方法是否正常执行都会在切入点之后执行的方法");
    }
}

```





```xml
    <bean id="userService" class="lei.study.service.impl.userServiceImpl"></bean>

    <bean id="printLogger" class="lei.study.utils.Logger"></bean>

    <!--
        表达式的全通配写法
                    * *..*.*(..)
    -->
    <aop:config>
        <aop:aspect id="logAdvice" ref="printLogger">
            <!--建立切入点方法的关联-->
            <aop:before method="beforePrint" pointcut="execution(* lei.study.service.impl.*.*(..))"/>
            <aop:after-returning method="afterPrint" pointcut="execution(* lei.study.service.impl.*.*(..))"/>
            <aop:after-throwing method="printThrow" pointcut="execution(* lei.study.service.impl.*.*(..))"/>
            <aop:after method="afterLogger" pointcut="execution(* lei.study.service.impl.*.*(..))"/>
        </aop:aspect>
    </aop:config>
```



## spring中的事物控制

### spring基于XML的声明式事务控制



在spring中我们不用再编写关于事务的一些操作方法，我们可以直接使用xml配置的方式，对数据库连接操作中的方法进行相关事务的配置。



```
spring中基于XML的声明式事务控制配置步骤 
1. 配置事务管理器 
2. 配置事务的通知           
		此时我们需要导入事务的约束 tx的名称和约束  同时也需要aop的           
		使用 <tx:advice> 配置事务的通知                    
			id :给事务通知起一个唯一标识                    
			transtration-manager：给事务通知提供一个事务管理器引用 
3，配置AOP中的通用切入点表达式 
4. 建立事务通知和切入点表达式的对应关系 
5. 配置事务的属性        
			是在事务的通知  tx:advice 标签的内部进行配置
```



1. **在spring中配置事务管理器**

   DataSourceTransactionManager可以配置JDBC或者是ibatis关于事务的配置

   HiberateTransactionManager 可以配置整合Hiberate中关于事务的配置

```xml
<!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
```



2. **配置事务的通知**  （以及配置事务的属性）

在配置事务的通知中，我们需要配置事务的一些属性



```xml

<!--配置事务的通知-->
    <tx:advice id="txAdvicce" transaction-manager="transactionManager">
        <!--配置事务的属性
            isolation=""  用于指定事务的隔离级别   默认值是default ，表示使用数据库的默认隔离级别
            propagation=""  用于指定事务的传播行为，默认值是REQUIRED 表示一定有事务，增删改的选择，查询方法可以选择SUPPORTS
            read-only=""   用于指定事务是否只读，只有查询方法才能设置为true。默认值是false，表示读写
            rollback-for=""  用于指定一个异常，当产生该异常时，事务回滚，产生其他异常时，事务不回滚，没有默认值，表示任何异常都回滚
            no-rollback-for=""  用于指定一个异常，当产生该异常时，事务不回滚，产生其他异常事务回滚，没有默认值，表示任何异常都回滚
            timeout=""     用于指定事务的超时时间，默认值是-1 表示永不超时，如果指定了数值，以秒为单位

        -->
        <tx:attributes>
            <tx:method name="*" propagation="REQUIRED" read-only="false"/>
            <tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
        </tx:attributes>
    </tx:advice>
```



3. **配置事务的切入点表达式以及建立切入点表达式和事务通知的对应关系**



```xml
<!--配置AOP-->
    <aop:config>
        <!--配置切入点表达式-->
        <aop:pointcut id="pt1" expression="execution(* lei.study.service.impl.*.*(..))"></aop:pointcut>
        <!--建立切入点表达式和事务通知的对应关系-->
        <aop:advisor advice-ref="txAdvicce" pointcut-ref="pt1"></aop:advisor>
    </aop:config>
```







### spring基于注解的声明式事务控制







































































