---
title: springMVC
date: 2020-06-07 20:28:32
tags: [java,面试]
categories: 面试整理
copyright: true
toc: true
---



#### springMVC框架时基于组件的方式执行流程



  <!-- more -->

### springMVC的执行流程：

（1） 首先是浏览器发出请求，发送到web.xml文件中我们配置过的前端控制器DispatcherServlet响应用户请求，DispatcherServlet根据请求的模式决定是否去找对应的映射器，DispatcherServlet是整个流程控制的中心，由它调用其他组件处理用户的请求，DispatcherServlet的存在降低了组件之间的耦合性。

（2）DispatcherServlet请求查找Handler，然后到达处理器映射器HandlerMapping，HandlerMapping让Controller类中的方法执行。springMVC提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式

（3）我们在对应的Controller方法中执行完对应的逻辑之后，或者把数据封装进Model中之后返回。



在Controller中

@RequesMappding注解中的参数

​		value：用于指定请求的URL，它和path属性的作用是一样的

​	    method：用于指定请求的方式

​		params：用于指定限制请求参数的条件，它支持简单的表达式。要么请求参数的key和value必须和配置的一模一样

​			例如：  param  ={“accountName”}  表示请求参数必须有accountName

​						  param={“money！=100”}  表示请求参数money不能是100

​							param={“money=100”} 表示请求参数money必须是100

​		headers：用于指定限制请求消息头的条件

​			注意：以上四个属性只要出现2个或者以上时，他们的关系是与的关系





### 请求参数的绑定



SpringMVC的参数绑定过程是把表单提交的请求参数，作为控制器中方法的参数进行绑定的

要求：提交表单的name和参数的名称是相同的

支持的数据类型：

（1） 基本数据类型和字符串类型

（2）实体类型  JavaBean

（3）集合数据类型（List、Map集合等）



## 常用注解






























































































