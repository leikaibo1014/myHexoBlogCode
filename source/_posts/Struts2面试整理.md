---
title: Struts2面试整理
date: 2020-06-07 18:26:32
tags: [java,面试]
categories: 面试整理
copyright: true
toc: true

---



## 1. 什么是Struts2？

 

Struts2是一个用Java构建Web应用程序的开源框架。Struts2基于OpenSymphony WebWork框架。它从Struts1中得到了很大的改进，使其更加灵活，易于使用和扩展。Struts2的核心组件是Action，Interceptors和Result页面。



Struts2提供了许多方法来创建Action类并通过struts.xml或注释来配置它们。我们可以为常见任务创建自己的拦截器。Struts2附带了很多标签，并使用OGNL表达式语言。我们可以创建自己的类型转换器来渲染结果页面。结果页面可以是JSP和FreeMarker模板。

 

 <!-- more -->



## 2. Struts2的核心组件是什么？

1、前端控制器 

2、OGNL/ VS：OGNL/ ValueStack 

3、Action：动作

4、Result：结果 

5、Interceptor：拦截器 

6、Tags：标签库

 

## 3. Struts2的工作流程

 

（1）所有请求提交给前端控制器。

（2）根据配置信息确定要调用的 Action。

（3）创建一个 ValueStack 对象（每个请求都有一个独立的 VS 对象）。

（4）创建 Action 对象，把 Action 对象放到 VS 的栈顶，将 VS 对象存入到 request 中，存储的 key 为“ struts.valueStack”。

（5）控制器调用 Action 对象接收请求参数， 并在方法中根据输入属性算输出属性。

（6）在调用 Action 之前或之后调用一系列 Interceptor。

（7）根据 Action 返回的字符串确定 Result（ 10 种类型）。

（8）调用 Result 对象，将 VS 中的数据按照特定的格式输出。

（9）很多情况下， Result 将转发到 JSP， JSP 页面用 Tags 取出数据并显示。

（10）请求处理完后，将 ValueStack 对象和 Action 对象销毁。

 

 

## 4. OGNL技术



##### 1.什么是OGNL?

OGNL是 Object-Graph Navigation Language（对象图形导航语言）的缩写，它是一种功能强大的表达式语言。是一种表达式语言。使用这种表达式语言，你可以通过某种表达式语法，存取Java对象树中的任意属性、调用Java对象树的方法、同时能够自动实现必要的类型转化。

 

OGNL简介：　

　　（1）OGNL是Object Graphic Navigation Language（对象图导航语言）的缩写，它是一个开源项目。

　　　　 struts2框架默认就支持Ognl表达式语言（所以struts必须引用的包：ognl.jar）；

　　（2）struts2的ognl的作用：页面取值使用；

　　（3）OGNL和EL的区别:

　　　　　EL表达式语言:用于页面取值，jsp页面取值的标准（默认可以直接使用，应用范围更加广泛）；

　　　　  OGNL表达式语言：struts2标签默认支持的表达式语言，必须配置struts标签使用，不能离开struts2标签直接使用；

　　（4）OGNL 有一个上下文（Context）概念,[OgnlContext对象：OgnlContext对象是ognl表达式语言的核心];

　　　　 其实上下文就是一个MAP结构，它实现了 java.utils.Map 的接口。

　　（5）ognl表达式语言取值，取根元素的值，不用带#符号；ognl表达式语言取值，取非根元素的值，需要带#符号；

 

6：ValueStack, 即值栈对象（根元素的值存储在root中，非根元素的值存储在context中）。

　 （1）：值栈对象是整个struts数据存储的核心，或者叫中转站。

　 （2）：用户每次访问struts的action，都会创建一个Action对象、值栈对象、ActionContext对象；  然后把Action对象放入值栈中； 最后再把值栈对象放入request中，传入jsp页面。

　 （3）：(key: struts.valueStack)； 开发者只需要通过ActionContext对象就可以访问struts的其他的关键对象。 （ActionContext是给开发者用的，便于学习与使用。）

 

 

##### 2. Struts2标签的使用

 

Struts2 的很多标记就是通过访问 ValueStack 获得数据的。使用前要在页面中要引入： <%@taglib uri="/struts-tags" prefix="s"%>， prefix：表示前缀

 

 

## 5. 什么是Struts2中的拦截器？

 

它提供了一种机制可以使开发者可以定义在一个action执行的前后执行的代码，也可以在一个action执行前阻止其执行，同时也提供了一种可以提取action中可重用部分的方式。在AOP（Aspect-Oriented Programming）中拦截器用于在某个方法或字段被访问之前，进行拦截然后在之前或之后加入某些操作。Struts2内置了很多拦截器，每个拦截器完成相对独立的功能，多个拦截器的组合体成为拦截器栈。最为重要的拦截器栈是系统默认拦截器栈DefaultStack。struts2默认定义了35种拦截器，默认执行20种。

 

 

1）拦截器（Interceptor）是Struts2的核心组成部分，是XWork体系的重要组件。

2）Struts2很多功能都是构建在拦截器基础之上的，例如文件的上传下载、国际化、数据类型转换和数据校验等等。

3）Struts2拦截器在访问某个Action方法之前和之后实施拦截

4）Struts2拦截器是可插拔的，拦截器是AOP（面向切面编程）的一种实现。

5）拦截器栈（Interceptor Stack）：将拦截器按一定的顺序联结成一条链，在访问被拦截的方法时，Struts2拦截器链中的拦截器就会按其之前定义的顺序依次调用。

 

 

struts2中的的功能（参数处理、文件上传、字符编码等）都是通过系统拦截器实现的。如果业务需要，当然我们也可以自定义拦截器,进行可插拔配置，在执行  Action  的方法前后、加入相关逻辑完成业务。

 

使用场景：

（1）用户登录判断，在执行 Action的前面判断是否已经登录，如果没有登录的跳转到登录页面。

（2）用户权限判断，在执行 Action的前面判断是否具有，如果没有权限就给出提示信息。

（3）操作日志......等等

 

 

## 6. 在Struts2中创建Action类有哪些不同的方法？

 

（1）方式一：直接创建一个类，可以是POJO，即原生Java类，没有继承任何类，也没有实现任何接口。这种方式使得strust2框架的代码侵入性更低，但是这种方式是理想状态，开发中并不常用。

（2）方式二：实现一个接口com.opensymphony.xwork2.Action。接口里面有execute方法，提供了action方法的规范，Action接口预置了一些字符串，可以在返回结果时使用。为了方便。这种方式也不常用。

（3）方式三：继承一个类，ActionSupport。该方式帮我们实现了一些接口 Action, Validateable, ValidationAware, TextProvider, LocaleProvider, Serializable  如果我们需要用到这些接口的时候，就不需要自己实现了

 

## 7. 什么是ValueStack和OGNL？\****

 

ValueStack是Struts2存储应用程序数据以处理客户端请求的存储区域。数据存储在ActionContext使用ThreadLocal的对象中，以具有特定于特定请求线程的值。

 

对象图导航语言（OGNL）是一种功能强大的表达式语言，用于处理存储在ValueStack上的数据。正如您在架构图中看到的，拦截器和结果页面都可以使用OGNL访问存储在ValueStack上的数据。

 

## 8. 列举Struts2中引入的一些有用的注释？\****

 

Struts2中引入的一些重要注释是：

@Action创建动作类

@Actions为多个动作配置单个类

@Namespace和@Namespaces用于创建不同的模块

@Result用于结果页面

@ResultPath用于配置结果页面位置

 

 

## 9. 项目中继承ActionSupport并实现ModelDriven接口？\****

 

struts可以继承ActionSupport类，也可以不继承，继承的好处简单来说就是更方便实现验证，国际化等功能，与struts2的功能结合紧密，方便我们开发。

 

ActionSupport类的作用：

   此类实现了很多实用的接口，提供了很多默认的方法，这些默认方法包括国际化信息，默认的处理用户请求的方法等，可以大大简化action的开发，在继承ActionSupport的情况下，必须有无参构造函数。

  struts2不要求我们自己设计的action类继承任何的struts基类或struts接口，但是我们为了方便实现我们自己的action，大多数情况下都会继承com.opensymphony.xwork2.ActionSupport类，并重写此类里的public String execute() throws Exception方法。因为此类中实现了很多的实用借口，提供了很多默认方法，这些默认方法包括国际化信息的方法、默认的处理用户请求的方法等，这样可以大大的简化Acion的开发。

 

实现了ModelDriven

可以使用VO，就是提交过来的参抄数（这个参数就百是表单的数据）

可以直接驱动到action的setXXX（）里面，意思就是说

实现了ModelDriven就必须百实现getModel这个方法，该方法返回的是你的度ModelDrivern泛型中设定的实体类。其实是为了实现代码的重用，不要再定义问一次你在数据访问层定义的属性了，直接用那答个类就行了。不过有时候也回不是很需要这个东西，答比如只有一两个表单域的时候。

 

 

## 10. Struts国际化？\****

 

国际化（i18n）是规划和实施的产品和服务，使他们能很容易地适应特定的本地语言和文化的过程中，这个过程被称为本地化。国际化的过程有时也被称为翻译或本地化启用。国际化是缩写i18n，因为我和两端用n字打头，并有18个字符之间的第i个和最后n。

 

 

## 11. 如何在Struts2应用程序中上传文件？\****

 

文件上传是Web应用程序中的常见任务之一。 Struts2通过FileUploadInterceptor提供对文件上传的内置支持。 此拦截器在struts-default包中配置，并提供选项以设置文件的最大大小和可以上载到服务器的文件类型。

 

## 12. Struts2实现文件上传的步骤？\****

 

基于表单的文件上传需要下载所需要的jar包文件***\*Commons-FileUpload组件\*******\*、\*******\*需要下载Common-FileUplaod\**** ***\*当然MyEclipce中Struts2支持里自带有这两个包\****

 

基于表单的上传，在form标签中有一个必须的属性值设置：enctype 

如果要做文件上传功能必须将enctype值设为multipart/form-data

 

修改struts2配置文件

配置struts.xml文件，重置fileUpload拦截器

 

<interceptor-ref name=**"fileUpload"**>

​      <param name=**"maximumSize"**>10485760</param> 

<!-- 10M -->

 </interceptor-ref>

 

编写相应的action类

 

 

## 13. Struts2的核心是什么? 体现了什么思想？\****

 

Struts2的核心是拦截器,基本上核心功能都是由拦截器完成,拦截器的实现体现了AOP(面向切面编程)思想

 

## 14. 为何继承ActionSupport

 

因为ActionSupport实现了Action接口，提供了国际化，校验功能。

ActionSupport实现了国际化功能：因为它提供了一个getText(String key)方法实现国际化,该方法从资源文件上获取国际化信息。

Action接口提供了五个常量(success,error,login,input,none)。

 

## 15. Struts2 如何定位action中的方法

 

在xml配置文件中通过配置多个action，使用action的method指定方法。

使用通配符(*)匹配方法。

 

 

## 16. 模型驱动与属性驱动是什么 模型驱动使用时注意什么问题

 

模型驱动与属性驱动都是用来封装数据的。

（1）模型驱动：在实现类中实现ModelDriven<T>接口使用泛型把属性类封装起来，重写getModel()方法，然后在实现类里创建一个属性类的实例，

通过这个实例拿到封装进来的值，拿返回值的时候使用工具进行值拷贝。

（2）属性驱动：在实现类里定义属性，生成get与set方法，通过属性来拿值。

注意：模型驱动使用时注意的是在使用前先把属性类实例化，否则会出现空指针错误，拿返回对象的值需要用拷贝内存因为地址发生改变。

模型驱动不可以使用局部类型转换器。

 

## 17. Struts2是怎样进行值封装的？

 

struts2的值封装实际上是采用了ognl表达式

struts2的拦截器经过模型驱动时会先判断action是否实现了ModelDriven，如果是，则拿到模型的实例放在了栈的顶部，

到属性驱动的时候会从栈里面把栈顶的实例给取出来，从页面传进来的值放在一个map集合当中，

通过map集合进行迭代会通过ognl技术把值封装到实例中。

 

 

## 18. OGNL是什么你在项目中如何使用它\*******\*？

 

OGNL是：对象图形导航语言。

原ognl是单个对象的，在struts2的应用是多个对象的，struts2的类型转换也是通过ognl来实现的。

Struts2中默认的表达式语言就是ognl，struts2的取值设值都是通过ognl来实现的,struts2要依靠它的标签才可以使用ognl。

 

相对于jstl或者其他表达式语言，它有如下优势：

 

（1）能够调用对象实例的方法

（2）能够调用类的静态方法

（3）操作集合对象

（4）访问Ognl上下文

 

取值时ognl有三个参数：

（1）表达式

（2）ognl的上下文(map)

（3）对象实例(值栈)。

如果表达式带#号是从上下文中(map)拿值，否则是从值栈中拿值。

 

设值时有四个参数：

（1）表达式，（2）ognl的上下文(map)，（3）对象实例，（4）设入的值

如果表达式带#号是把值设入上下文中(map)，否则是把值设入值栈中。

 

在struts2中 ognl的实现关系:ActionConetxt。

 

ognl 3个常用的符号 # $ %

 

\# ：构造map，list集合。取ognl上下文的值。用来过滤集合。

 

$ ：在校验框架中取资源文件中的值。可以在配置文件中传递参数。

 

% ：使用百分号可以进行转义。

 

 

## 19. Strust2如何访问Servlet API\*******\*？

 

答：

（1）通过ActionContext可以获得request，application，session三个作用域(struts2实际上是通过适配器来把servlet的api转换为一个map，

并把这些map放在ActionContext里面)。

（2）通过ActionContext的子类ServletActionContext去获取原滋原味的API。

（3）可以通过实现ServletRequestAware接口，重写里面的setServletRequest方法可以获得request，实际上是通过set的依赖注入。

 

 

## 20. 什么是拦截器 说说Struts2用拦截器来干什么 说出6个拦截器来\****

 

在访问类的某个方法或者属性之前执行，拦截的是Action的请求，进行拦截然后在方法的执行前或者之后加入某些操作，如：国际化，权限，效验等与源代码无关的操作。

 

拦截器：国际化，表单重复提交，类型转换，自动装配，数据封装,异常处理，模型驱动，请求参数，处理类型转换错误，日志拦截器。

 

 

***\*（\*******\*项目中遇到什么问题\*******\*）\****

 

3.使用模型驱动拿返回值的时候地址内存不一样，会拿不到值。

原因：因为struts2最开始是把创建action的实例放到ActionContext的栈顶，后来把创建模型驱动的实例放到栈顶，

而通过struts2的标签去取值会取栈顶的实例，而此时拿到的还是最开始的创建的那个实例，所以为空。

解决方式：利用原型进行对象拷贝，使用拷贝工具，BeanUtils或者是propertyUtils。

 

## 21. ActionContext是用来干什么用的？

 

答：ActionContext是Action的上下文。

通过ActionContext这个类来可以获得:request，application，session，Locale、ValueStack,ConversionErrors

 

（1）可以获得servlet的api：request，application，session。

（2）进行国际化：获得语言环境 Locale。

（3）收集错误信息：ConversionErrors。

（4）有个值栈来装action实例与创建的模型驱动的实例:ValueStack。

（5）ActionContext是线程安全的-用本地线程实现的。

 

 

## 22. Struts2是如何实现mvc的：struts2的底层视图默认是freemaker。什么mvc？\****

 

M：模型，V：视图，C：控制器

在struts2中分别对应什么？

M(模型)：除核心控制器外都属于模型，实现业务的类(service层与dao层)。

V(视图)：result,type,dispatcher默认的(jsp，freemaker，velocity，源代码等)。

C(控制器)：核心控制器与业务控制器。

mvc解决什么问题？

解决了数据封装，类型转换，数据效验，国际化，异常处理等问题。

 

 

## 23. 开发项目时struts2在页面怎样拿值？

 

从值栈中或者是ognl的上下文中取值

 

## 24. 怎么样用Struts2进行文件的上传或者下载\

 

上传：Struts2里面已经提供了默认的拦截器来处理上传,只要在jsp用了s:file标签,把s:from的enctype 属性设置为 multipart/form-data,然后在action里面定义三个字段 File file ,String [file]ContentType,String [file]?FileName即可,如果有多个文件上传,那么就用List<File>来接受,然后把接受到的File构建成FileInputStream,转化成硬盘上面的文件即可(用输出流)

下载：用了resust为stream的类型,然后在action定义四个属性,默认的有个是InputStream 的类型叫inputStream的,从硬盘上面读取文件到这个流赋值即可.

 

 

## 25. 简单讲下struts里面的标签,说不少于5个\****

 

s:iterater ?s:property s:form s:url s:if 等等

 

## 26.默认struts2里面的标签取值都是从那里取值的\****

 

默认都是从OGNL值栈里面取值的

 

## 27. ValueStack分为那两部分,组成部分是什么,分别怎么访问\****

 

分为对象栈和ContextMap ,对象栈里面放入的是Action及其用户定义的对象,在前台可以直接访问,在contextMap里面放入的是Servlet对象转化后map,譬如requestMap,sessionMap,访问的时候前面加#即可。

 

 

## 28. struts2的请求处理流程\

 

（1）客户端发送请求。

（2）经过一系列的过滤器（如:ActionContextCleanUp、SiteMesh等）到达核心控制器（FilterDispatcher）。

（3）核心控制器通过ActionMapper决定调用哪个Action，如果不是调用Action就直接跳转到jsp页面

（4）如果ActionMapper决定调用了某个Action，核心控制器就把请求的处理交给一个代理类（ActionProxy）。

（5）代理类通过配置管理器(Configuration Manager)找到配置文件（struts.xml）找到需要调用的Action类。

（6）代理类还要创建一个Action的调度器（ActionInvocation）。

（7）由调度器去调用Action，当然这里还涉及到一些相关的拦截器的调用。

（8）Action执行完后，这个调度器还会创建一个Result返回结果集，返回结果前还可以做一些操作（结果集前的监听器）。

 

 

## 29. struts2源码与设计模式

 

命令模式(发生.do请求封装成action调用execute方法)，代理模式(访问action时核心控制器把请求交给代理类(ActionProxy)去访问Action就是代理模式)，

责任链模式(访问action时经过一系列的拦截器就使用了责任链模式)，适配器(把Servlet的API通过适配器转换成Map，把Map放入ActionContext中)，

组合模式(拦截器栈和拦截器就使用到组和模式)。

 

源码：

服务器启动的时候容器会创建FilterDispatcher实例，进入dispatcher.init()方法，在init方法里面会初始化sturts2的一系列的配置文件，然后向服务器发送请求，进入doFilter方法，在里面通过调用dispatcher.serviceAction方法,进入到Dispatcher里面的serviceAction方法，在这里就会把servlet的api转换为map，并把它一个个的放在ActionContext中，在这里还会创建ActionProxy的实例去调用execute方法，到StrutsActionProxy的execute方法，通过DefaultActionInvocation的实例去调用invoke方法，在invoke方法里面会进行判断拦截器是否存在，如果存在就会调用intercept方法，在拦截器类(Intercept)中会通过DefaultActionInvocation实例调用里面的invock方法,它们会形成递归调用，当拦截器不存在的时候就会进入invokeActionOnly方法，判断有没有返回结果前的监听器preResultListener如果有则进去一个个执行，最后调用 executeResult()方法，创建result返回，执行execute方法，并返回视图。

 

 

## 30. struts2工作流程

 

1、Struts 2框架本身大致可以分为3个部分：核心控制器FilterDispatcher、业务控制器Action和用户实现的企业业务逻辑组件。

（1）核心控制器FilterDispatcher是Struts 2框架的基础，包含了框架内部的控制流程和处理机制。

（2）业务控制器Action和业务逻辑组件是需要用户来自己实现的。用户在开发Action和业务逻辑组件的同时，还需要编写相关的配置文件，供核心控制器FilterDispatcher来使用。

​    Struts 2的工作流程相对于Struts 1要简单，与WebWork框架基本相同，所以说Struts 2是WebWork的升级版本。

***\*简要执行流程：\****

 

（1）客户端初始化一个指向Servlet容器的请求；

（2）这个请求经过一系列的过滤器（Filter）

​    （这些过滤器中有一个叫做ActionContextCleanUp的可选过滤器，这个过滤器对于Struts2和其他框架的集成很有帮助，例如：SiteMesh Plugin）

（3）接着FilterDispatcher被调用，

​     FilterDispatcher询问ActionMapper来决定这个请是否需要调用某个Action

（4）如果ActionMapper决定需要调用某个Action，

​     FilterDispatcher把请求的处理交给ActionProxy

（5）ActionProxy通过Configuration Manager询问框架的配置文件，找到需要调用的Action类

（6）ActionProxy创建一个ActionInvocation的实例。

（7）ActionInvocation实例使用命名模式来调用，

​     在调用Action的过程前后，涉及到相关拦截器（Intercepter）的调用。

（8）一旦Action执行完毕，ActionInvocation负责根据struts.xml中的配置找到对应的返回结果。返回结果通常是（但不总是，也可 能是另外的一个Action链）一个需要被表示的JSP或者FreeMarker的模版。在表示的过程中可以使用Struts2 框架中继承的标签。    在这个过程中需要涉及到ActionMapper

（9）响应的返回是通过我们在web.xml中配置的过滤器

（10）如果ActionContextCleanUp是当前使用的，则FilterDispatecher将不会清理sreadlocal ActionContext;如果ActionContextCleanUp不使用，则将会去清理sreadlocals。 

 

 

## 31. 拦截器和过滤器的区别？

 

1、拦截器是基于Java反射机制的，而过滤器是基于函数回调的。

2、过滤器依赖于servlet容器，而拦截器不依赖于servlet容器。

3、拦截器只能对Action请求起作用，而过滤器则可以对几乎所有请求起作用。

4、拦截器可以访问Action上下文、值栈里的对象，而过滤器不能。

5、在Action的生命周期中，拦截器可以多次调用，而过滤器只能在容器初始化时被调用一次。

 



## 32. Struts2是如何启动的？\

 

 

1、struts2框架是通过Filter启动的，即StrutsPrepareAndExecuteFilter，此过滤器为struts2的核心过滤器； 

2、StrutsPrepareAndExecuteFilter的init()方法中将会读取类路径下默认的配置文件struts.xml完成初始化操作。struts2读取到struts.xml的内容后，是将内容封装进javabean对象然后存放在内存中，以后用户的每次请求处理将使用内存中的数据，而不是每次请求都读取struts.xml文件。

 

 

## 33. Struts2的核心控制器是什么？它是什么作用？\

 

1、Struts2框架的核心控制器是StrutsPrepareAndExecuteFilter。 

2、作用： 

 负责拦截由<url-pattern>/*</url-pattern>指定的所有用户请求，当用户请求到达时，该Filter会过滤用户的请求。默认情况下，如果用户请求的路径 

不带后缀或者后缀以.action结尾，这时请求将被转入struts2框架处理，否则struts2框架将略过该请求的处理。 

可以通过常量"struts.action.extension"修改action的后缀，如： 

<constant name="struts.action.extension" value="do"/> 

如果用户需要指定多个请求后缀，则多个后缀之间以英文逗号（,）隔开。

<constant name="struts.action.extension" value="do,Go"/>  

 

## 34. struts2是如何管理action的？这种管理方式有什么好处？

 

struts2框架中使用包来管理Action，包的作用和java中的类包是非常类似的。 

主要用于管理一组业务功能相关的action。在实际应用中，我们应该把一组业务功能相关的Action放在同一个包下。 

struts2中的默认包struts-default有什么作用？ 

（1）struts-default包是由struts内置的，它定义了struts2内部的众多拦截器和Result类型，而Struts2很多核心的功能都是通过这些内置的拦截器实现，如：从请求中 

把请求参数封装到action、文件上传和数据验证等等都是通过拦截器实现的。当包继承了struts-default包才能使用struts2为我们提供的这些功能。 

（2）struts-default包是在struts-default.xml中定义，struts-default.xml也是Struts2默认配置文件。 Struts2每次都会自动加载 struts-default.xml文件。 

（3）通常每个包都应该继承struts-default包。   

 

 

## 35. 值栈ValueStack的原理与生命周期？

 

（1）ValueStack贯穿整个 Action 的生命周期，保存在request域中，所以ValueStack和request的生命周期一样。当Struts2接受一个请求时，会迅速创建ActionContext， ValueStack，action。然后把action存放进ValueStack，所以action的实例变量可以被OGNL访问。 请求来的时候，action、ValueStack的生命开始，请求结束，action、  ValueStack的生命结束； 

 

（2）action是多例的，和Servlet不一样，Servelt是单例的； 

（3）每个action的都有一个对应的值栈，值栈存放的数据类型是该action的实例，以及该action中的实例变量，Action对象默认保存在栈顶； 

（4）ValueStack本质上就是一个ArrayList； 

（5）关于ContextMap，Struts 会把下面这些映射压入 ContextMap 中： 

parameters ：该 Map 中包含当前请求的请求参数

request ： 该 Map 中包含当前 request 对象中的所有属性 

session ：该 Map 中包含当前 session 对象中的所有属性 

Application：该 Map 中包含当前 application 对象中的所有属性 

attr：该 Map 按如下顺序来检索某个属性: request, session, application     

（6）使用OGNL访问值栈的内容时，不需要#号，而访问request、session、application、attr时，需要加#号； 

（7）注意： Struts2中，OGNL表达式需要配合Struts标签才可以使用。如：<s:property value="name"/> 

（8）在struts2配置文件中引用ognl表达式 ,引用值栈的值 ，此时使用的"$"，而不是#或者%; 

 

## 36. ActionContext、ServletContext、pageContext的区别？

 

（1）ActionContext是当前的Action的上下文环境，通过ActionContext可以获取到request、session、ServletContext等与Action有关的对象的引用； 

（2）ServletContext是域对象，一个web应用中只有一个ServletContext，生命周期伴随整个web应用； 

（3）pageContext是JSP中的最重要的一个内置对象，可以通过pageContext获取其他域对象的应用，同时它是一个域对象，作用范围只针对当前页面，当前页面结束时，pageContext销毁， 

生命周期是JSP四个域对象中最小的。 

 

## 37. struts的工作原理

 

（1）初始化，读取struts-config.xml、web.xml等配置文件（所有配置文件的初始化）

（2）发送HTTP请求,客户端发送以.do结尾的请求

（3）填充FormBean（实例化、复位、填充数据、校验、保存）

（4）将请求转发到Action（调用Action的execute（）方法）

（5）处理业务（可以调用后台类，返回ActionForward对象）

（6）返回目标响应对象（从Action返回到ActionServlet）

（7）转换Http请求到目标响应对象（查找响应，根据返回的Forward keyword）

（8）Http响应，返回到Jsp页面

 



## 38. 用自己的话简要阐述struts2的执行流程。

 

Struts 2框架本身大致可以分为3个部分：核心控制器FilterDispatcher、业务控制器Action和用户实现的企业业务逻辑组件。

核心控制器FilterDispatcher是Struts 2框架的基础，包含了框架内部的控制流程和处理机制。业务控制器Action和业务逻辑组件是需要用户来自己实现的。用户在开发Action和业务逻辑组件的同时，还需要编写相关的配置文件，供核心控制器FilterDispatcher来使用。

Struts 2的工作流程相对于Struts 1要简单，与WebWork框架基本相同，所以说Struts 2是WebWork的升级版本。基本简要流程如下：

（1）客户端浏览器发出HTTP请求。

（2）根据web.xml配置，该请求被FilterDispatcher接收。

（3）根据struts.xml配置，找到需要调用的Action类和方法， 并通过IoC方式，将值注入给Aciton。

（4）Action调用业务逻辑组件处理业务逻辑，这一步包含表单验证。

（5）Action执行完毕，根据struts.xml中的配置找到对应的返回结果result，并跳转到相应页面。

（6）返回HTTP响应到客户端浏览器。

它是以Webwork的设计思想为核心，吸收struts1的优点，可以说 struts2是struts1和Webwork结合的产物。

 

 

### struts2 的工作原理图： 一个请求在Struts2框架中的处理分为以下几个步骤：

 

（1）客户端发出一个指向servlet容器的请求(tomcat)；

（2）这个请求会经过图中的几个过滤器，最后会到达FilterDispatcher过滤器。

（3）过滤器FilterDispatcher是struts2框架的心脏，在处理用户请求时，它和请求一起相互配合访问struts2 的底层框架结构。在web容器启动时，struts2框架会自动加载配置文件里相关参数，并转换成相应的类。 

如：ConfigurationManager、ActionMapper和ObjectFactory。ConfigurationManager 存有配置文件的一 些基本信息，ActionMapper存有action的配置信息。在请求过程中所有的对象（Action，Results， Interceptors，等）都是通过ObjectFactory来创建的。过滤器会通过询问ActionMapper类来查找请求中 需要用到的Action。

（4）如果找到需要调用的Action，过滤器会把请求的处理交给ActionProxy。ActionProxy为Action的代理对象 。ActionProxy通过ConfigurationManager询问框架的配置文件，找到需要调用的Action类。 

（5）ActionProxy创建一个ActionInvocation的实例。ActionInvocation在ActionProxy层之下，它表示了 Action的执行状态,或者说它控制的Action的执行步骤。它持有Action实例和所有的Interceptor。 

（6）ActionInvocation实例使用命名模式来调用，1） ActionInvocation初始化时，根据配置，加载Action相 关的所有Interceptor。2）通过ActionInvocation.invoke方法调用Action实现时，执行Interceptor。在 调用Action的过程前后，涉及到相关拦截器(intercepetor)的调用。

（7）一旦Action执行完毕，ActionInvocation负责根据struts.xml中的配置找到对应的返回结果。返回结果 通常是（但不总是，也可能是另外的一个Action链）一个需要被表示的JSP或者FreeMarker的模版。在表 示的过程中可以使用Struts2 框架中继承的标签。

 

## 39. Struts2 如何访问 HttpServletRequest、HttpSession、ServletContext 三个域对象 ?

 

①. 与 Servlet API 解耦的访问方式

　　> 通过 ActionContext 访问域对象对应的 Map 对象

　　> 通过实现 Aware 接口使 Struts2 注入对应的 Map 对象

 

②. 与 Servlet API 耦合的访问方式

　　> 通过 ServletActionContext 直接获取 Servlet API 对象

　　> 通过实现 ServletXxxAware 接口的方式使 Struts2 注入对应的对象

 

 

 

 

 

 