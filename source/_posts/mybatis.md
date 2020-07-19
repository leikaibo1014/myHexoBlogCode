---
title: mybatis整理
date: 2020-06-09 20:26:32
tags: [java,面试，阿里]
categories: 面试整理
copyright: true
toc: true
---



## 1. mybatis的入门

mybatis的环境搭建

​			第一步：创建maven工程并导入坐标

​			第二步：创建实体类和dao的接口

​			第三步：创建Mybatis的主配置文件sqlMapConfig.xml

​			第四步：创建映射配置文件   IUseDao.xml

 <!-- more -->



mybatis的文件约束

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
```



mybatis的配置文件

```xml

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--Mybatis的主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="mysql">
        <!--配置MySQL的环境-->
        <environment id="mysql">
            <!--配置事务的类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源，连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>


    <!--指定映射数据源文件的位置-->
    <mappers>
        <mapper resource="config/userDao/userDao.xml"></mapper>
    </mappers>

</configuration>

```



我们编写IUserDao的接口



```java
import java.util.List;

public interface IUserDao {
    List<User> findAll();
}
```



编写User的实体类，注意实体类中的属性名称要和数据库中的相同



编写映射器

映射的约束文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```



```xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="lei.study.dao.IUserDao">
    <select id="findAll" resultType="lei.study.domain.User">
        select * from user
    </select>
</mapper>


```



![1590024681000](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1590024681000.png)



编写测试类

```java

import lei.study.dao.IUserDao;
import lei.study.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class testDemo {
    public static void main(String[] args) throws Exception {
        //加载配置文件
        InputStream is = Resources.getResourceAsStream("sqlMapConfig.xml");
        //2.创建SqlSessionFactry工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sessionFactory = builder.build(is);

        //3. 使用工厂生产SQLSSession对象
        SqlSession sqlSession = sessionFactory.openSession();
        //4. 使用sqlsession创建Dao接口的代理对象
        IUserDao userDao = sqlSession.getMapper(IUserDao.class);
        List<User> users = userDao.findAll();
        for (User user: users
             ) {
            System.out.println(user);
        }
        sqlSession.close();
        is.close();

    }
}


```



分析上面的代码，我们发现



1. 创建工厂的时候，我们使用了建造者模式  SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();

   SqlSessionFactory sessionFactory = builder.build(is);

   我们只需要往建造者中传入字节流就可以

   构建者模式：把对象的创建细节隐藏，使使用者直接调用方法即可以拿到对象

   

2. 生产sqlsession使用了工厂模式（解耦）

   SqlSession sqlSession = sessionFactory.openSession();





3. 代理模式

    //4. 使用sqlsession创建Dao接口的代理对象
           IUserDao userDao = sqlSession.getMapper(IUserDao.class);

   优势：就是在不改变源码的基础上对原有方法增强





除了使用上面的xml文件形式的配置，我们还可以使用注解的方式完成数据库的查询操作

使用注解：

我们需要在接口中的方法上使用@Select() 注解，指定SQL语句，同时需要在sqlMapConfig.xml中的mapper配置时，使用class属性指定dao接口的全限定类名





在主配置文件中指定接口全限定类名

```xml
    <!--指使用注解的方式-->
    <mappers>
        <mapper class="lei.study.dao.IUserDao"></mapper>
    </mappers>
```



在接口中

```java
public interface IUserDao {
    @Select("select * from user")
    List<User> findAll();
}
```



注意：我们在实际开发中，都是越简便越好，所以都是采用不写dao实现类的方式，不管使用XML还是注解配置，但是mybatis它是支持写dao实现类的。



## 2. 自定义mybatis框架分析



通过快速入门示例，我们发现使用 mybatis 是非常容易的一件事情，因为只需要编写 Dao 接口并且按照 

mybatis 要求编写两个配置文件，就可以实现功能。远比我们之前的 jdbc 方便多了。（我们使用注解之后，将变得 

更为简单，只需要编写一个 mybatis 配置文件就够了。） 

但是，这里面包含了许多细节，比如为什么会有工厂对象（SqlSessionFactory）,为什么有了工厂之后还 要有构建者对象（SqlSessionFactoryBuilder），为什么 IUserDao.xml 在创建时有位置和文件名的要求等等。 这些问题我们在自定义 mybatis 框架进行讲解。 

请注意：我们讲解自定义 Mybatis 框架，不是让大家回去自己去写个 mybatis，而是让我们能更好了了解 mybatis 内部是怎么执行的，在以后的开发中能更好的使用 mybatis 框架，同时对它的设计理念（设计模式）有 一个认识。



## mybatis中的连接池

1. 连接池，我们在实际开发中都会使用连接池，因为它可以减少我们开发的时间。



2. mybatis中的连接池 ：

​		mybatis连接池提供了3中方式的配置 ：

​					配置的位置：主配置文件SqlMapConfig.xml中的dataSource标签，type属性就是表示采用何种连接池方式

​				type属性 的取值：

​								POOLED：采用传统的javac.sql.DataSource规范中的连接池，mybatis中有针对规范的实现

​								UNPOOLED：采用传统的获取连接的方式，虽然也实现了javax.sql.DataSource接口，但是并没有使用池的思想

​								JNDI：采用服务器提供的JNDI技术实现，来获取DataSource对象，不同的服务器所能拿到的DataSource是不一样的。

​										注意：如果不是web或者maven的war工程，是不能使用的，我们使用的是Tomcat服务器，采用的连接池就是dbcp连接池

​							



### mybatis中的事务

什么是事务

事务的四大特性

不考虑隔离性会产生的3个问题

解决办法：四种隔离级别



mybatis中是通过sqlsession对象的commit方法和rollback方法实现事务的提交和回滚



## mybatis中的多表查询



一对一的查询操作

我们使用user表和account表关联

在account表中新建主表属性  private  User  user



```java
private int id;
    private int uid;
    private Double money;

    private User user;
```





在account的配置文件中，我们编写映射文件



```xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="lei.study.dao.IAccountDao">

    <resultMap id="accountMap" type="lei.study.domain.Account">
        <id property="id" column="id"></id>
        <!--非主键字段的对应-->
        <result property="uid" column="uid"></result>
        <result property="money" column="money"></result>
        <association property="user" column="uid" javaType="lei.study.domain.User">
            <id property="userId" column="id"></id>
            <result property="userName" column="username"></result>
            <result property="userBirthday" column="birthday"></result>
            <result property="userSex" column="sex"></result>
            <result property="userAddress" column="address"></result>
        </association>
    </resultMap>
    <select id="findAllAccount" resultMap="accountMap">
        SELECT u.*,a.id AS aid,a.uid, a.money from account a, user u WHERE u.id = a.id
    </select>

</mapper>
```



一对多的关系

一个用户可能关联多张账户表

在User实体中，新建Account属性集合

```java
    private int userId;
    private String userName;
    private Date userBirthday;
    private String userSex;
    private String userAddress;

    private List<Account> accounts;
```



UserDao的映射文件中



```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="lei.study.dao.IUserDao">
    <!--配置查询结果的列名和实体类的属性名的对应关系-->
    <resultMap id="userAccountMap" type="lei.study.domain.User">
        <id property="userId" column="id"></id>
        <!--非主键字段的对应-->
        <result property="userName" column="username"></result>
        <result property="userBirthday" column="birthday"></result>
        <result property="userSex" column="sex"></result>
        <result property="userAddress" column="address"></result>
        <collection property="accounts" ofType="lei.study.domain.Account">
            <id property="id" column="id"></id>
            <result property="uid" column="uid"></result>
            <result property="money" column="money"></result>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="userAccountMap">
        SELECT * FROM user u LEFT OUTER JOIN account a on u.id = a.uid;
    </select>
```



### 多对多的关系

​	用户可以有多个角色

​	一个角色可以赋予多个用户

​	用户和角色之间是多对多





## 1. mybatis中的延迟加载

什么是延迟加载？

问题：在一对多中，当我们有一个用户，它有100个账户

​			在查询用户的时候，要不要把关联的账户查出来

​			在查询账户的时候，要不要把关联的用户查出来？

​			

​		在查询用户时，用户下的账户信息是什么时候使用，什么时候查询的

​		在查询账户时，账户的所属用户信息应该是随着账户查询时一起查询出来。



**什么是延迟加载**：在真正使用数据时才发起查询，不用的时候不用查询，，按需加载（懒加载）

**什么是立即加载？**：不管用不用，只要一调用方法，马上发起查询



在对应的四种表关系中：一对一，多对一，一对一，多对多

**一对多，多对多：**通常情况下我们都是采用延迟加载

**多对一，一对一：**通常情况下，我们都是采用立即加载



我们可以在mybatis全部配置中或者是每个映射器中配置相关的属性信息，然后进行配置。



## 2. mybatis中的缓存



什么是缓存？

​		存在于内存中的临时数据



为什么使用缓存

​		减少和数据库的交互次数，提高执行效率



什么样的数据使用缓存，什么样的数据不能使用

​			适用于缓存：

​					经常查询，并且不经常改变的

​					数据的正确与否对最终结果影响不大的

​			不适用与缓存：

​					经常改变的数据

​					数据的正确与否对最终的结果影响很大的



**mybatis中一级缓存和二级缓存**



**一级缓存：**

​			它指的是Mybatis中Sqlsession对象的缓存

​			当我们执行查询之后，查询的结果会同时存入到Sqlsession为我们提供的一块区域中，该区域的结构是一个Map，当我们再次查询同样的数据，mybatis会先去sqlsession中查询是否有，有的话直接拿出来用。

​		当SqlSession对象消失的时候，mybatis的一级缓存也就消失了。

一级缓存是sqlsession范围的缓存，当调用sqlsession的修改，添加，删除，commit（） close()等方法时，就会清空一级缓存。



**二级缓存：**



​		它指的是mybatis中的sqlsessionFacory对象的缓存，由同一个sqlsessFactory对象创建的SqlSession共享其缓存。

​		二级缓存的使用步骤：

​					第一步：让Mybatis框架支持二级缓存，（在SQlMapConfig.xml中配置）

​					第二步：让当前的映射文件支持二级缓存（在IUserDao.xml中配置）

​					第三步：让当前的操作支持二级缓存（在select标签中进行相关类的配置）





































