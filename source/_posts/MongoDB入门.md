---
title: MongoDB基本配置
date: 2019-03-02 12:20:32
tags: [MongoDB,数据库]
categories: 数据库
toc: true
---

MongDB是一个文档型数据库，里面包含一些术语和概念，我们先来了解一下MongoDB的基本概念，再看一下中间的基本操作

<!--more-->



## 1.1 基础概念
在mongoDB中是通过数据库、集合、文档、的方式来管理数据、先看一下mongoDB与关系型数据库的一些概念对比：

SQL术语/概念 | MongoDB术语/概念 | 解释说明  
----- | --- |---
database|database|数据库
table|collection|数据库表/集合
row|document| 数据库行/文档
column|field|数据字段/域
index|index|索引
table joins||表连接（MongDB不支持表连接）
primary key| primary| 主键，MongoDB自动在每个集合中添加_id的主键

- 1. 一个mongodb实例可以创建多个数据库
- 2. 一个数据库可以创建多个集合
- 3. 一个集合可以包含多个文档



## 1.2 连接mongodb
mongodb的使用方式是客户服务器模式，即使用一个客户端连接mongodb数据库（服务端）。
1. 命令模式
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][? options]]

mongodb:// 固定前缀   
username：账号，可不填  
password：密码，可不填   
host：主机名或ip地址，只有host主机名为必填项。  
port：端口，可不填，默认27017   
/database：连接某一个数据库   
?options：连接参数，key/value对

例如：
	mongodb://localhost 连接本地数据库27017端口   
	mongodb://root:123456@localhost 使用用户名root密码为123456连接本地数据库27017端口   
	mongodb://localhost,localhost:27018,localhost:27019，连接三台主从服务器，端口为27017、27018、2701


我们直接在命令行输入mongo  就可以使用输入命令来操作mongodb数据库了



## 1.3 数据库使用

1. 查询数据库

    show dbs   查询全部的数据库  
    db 	显示当前数据库

2. 创建数据库  
命令格式  

    use DATABASE_NAME

	例子：  
	use  test02  
	执行这一行命令之后，如果有test02数据库则切换到此数据库中，如果没有则创建  
	这里我们需要注意的是，新创建的数据库不会显示，因为这个数据库中至少需要包含一种集合。

3. 删除数据库

	命令格式

    db.dropDatabase()

	eg：    
	删除test02数据库  
	先切换到数据库中  use  test02  
	在执行删除  db.dropDatabase()  


## 1.4 集合

MongoDB中的集合相当于数据库中的表。一个数据库可以创建多个集合，一个集合是将相同类型的文档管理起来。

1. 创建集合

	db.createCollection(name,options)
	name:新创建的集合名称
	options：创建参数

2. 删除集合
	
	db.collection.drop()
	例子：
	db.student.drop() 删除student集合


## 1.5 文档

### 1.5.1 插入文档

	mongodb中的文档格式是json格式吗，下边就是一个文档，包括两个key：_id  主键和name
	
	{
	"_id" : ObjectId
	("5b2cc4bfa6a44812707739b5"),      
	"name" : "郑州大学"
	}

插入命令：

	db.COLLECTION_NAME.insert(document)`

每个文档默认以_id作为主键，主键默认类型为ObjectId（对象类型），mongodb会自动生成主键值。  

例子  

    db.student.insert({"name":"郑州大学","age":10})


注意：同一个集合中的文档的key可以不相同！但是建议设置为相同的。 


### 1.5.2 更新文档

命令格式：

    db.collection.update(
    <query>,
    <update>,    
    <options> 
    ) 
    
    query:查询条件，相当于sql语句的where  
    update：更新文档内容 
    options：选项


1. 替换文档

	将符合条件  “name” “郑州大学” 的第一个文档替换为  {“name”：“郑州大学学生”，“age”：10}
	
    	db.student.update({"name":"郑州大学"}，{“name”：“郑州大学学生”，“age”：10})


2. $set修改器
	使用$set修改器指定要更新的key，key不存在则创建，存在则更新。 

	将符合条件 "name":"郑州大学"的所有文档更新name和age的值。
	
		db.student.update({"name":"郑州大学"},{$set:{"name":"郑州大学学生","age":10}},{multi:true})   
		multi：false表示更新第一个匹配的文档，true表示更新所有匹配的文档


### 1.5.3 删除文档

命令格式：

    db.student.remove(<query>) 
    query：删除条件，相当于sql语句中的where


1. 删除所有文档

		db.student.remove({})

2. 删除符合条件的文档

	`db.student.remove({"name":" 郑州大学"})`


### 1.5.4 查询文档

命令格式：

    db.collection.find(query, projection) 
    query：查询条件，可不填 
    projection：投影查询key，可不写

1. 查询全部

    db.student.find()

2. 查询符合条件的记录
	查询name等于 “郑州大学”的文档
		db.student.find({"name","郑州大学"})

3. 投影查询

	只显示name和age两个key，_id主键不显示
	
	db.student.find({"name"：“郑州大学”},{name：1，age：1，_id:0})


## 1.6 用户

### 1.6.1 创建用户

    mongo>db.createUser( 
    { 
    user: "<name>",   
    pwd: "<cleartext password>",   
    customData: { <any information> },  
    roles: [     
    	{ role: "<role>", db: "<database>" } | "<role>",   
    	  ...   
     ]} 
    )


例子：  
创建root用户，角色为root
    
    use admin 
    db.createUser(     
    {        
    user : "root",
    pwd : "123456",
    roles:[{role:"root",db:"admin"}]      
    }   
    )

内置角色如下：

	1. 数据库用户角色：read、readWrite; 
	2. 数据库管理角色：dbAdmin、dbOwner、userAdmin
	3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager； 
	4. 备份恢复角色：backup、restore； 
	5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、 dbAdminAnyDatabase 
	6. 超级用户角色：root



### 1.6.2 认证登录

	如果不配置认证， 我们登陆mongo数据库的时候，是不需要进行账号密码登陆的。   
	为了安全需要，Mongodb要打开认证开关，即用户连接Mongodb要进行认证。

1、在mono.conf中设置 auth=true
2、重启Mongodb 
3、使用账号和密码连接数据库

1）mongo.exe连接

mongo.exe ‐u root ‐p 123456 ‐‐authenticationDatabase admin

2）Studio 3T连接

	输入账号密码进行登陆。


Windows下Mongodb最初未开启认证，如何开启authentication
博客分类： mongodb

cmd-> regedit-> HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services->MongoDB

（mongodb注册的名称，我的是MongoDB)

在它的ImgPath中，我们修改一下，加入 --auth

如下

"E:\develop Software\Mongodb\mongodb-win32-x86_64-2.2.3\mongodb-win32-x86_64-2.2.3\bin\mongod.exe" -dbpath "E:\develop Software\Mongodb\mongodb-win32-x86_64-2.2.3\mongodb-win32-x86_64-2.2.3\data\db" --logpath "E:\develop Software\Mongodb\mongodb-win32-x86_64-2.2.3\mongodb-win32-x86_64-2.2.3\data\log\mongodb.log" --auth --service



### 1.6.3 查询所有用户

查询当前库下的所有用户

	show users

### 1.6.4 删除用户

db.dropUser("用户名")   

    例子：   
    删除 test1用户
    db.dropUser("test1")

### 1.6.5 修改用户

	语法格式
	
	db.updateUser( 
	“<username>”
	{     
	customData : { <any information> },     
	roles : [              
	 	{ role: "<role>", db: "<database>" } | "<role>",            
	  	 ...             
		],     
	 pwd: "<cleartext password>"     
	  },     
	  writeConcern: { <write concern> })

先创建test1用户

    db.createUser(     
     {        
    user:"test1",        
    pwd:"test1",        
    roles:[{role:"root",db:"admin"}]      
    }   )


修改test1用户的角色为readWriteAnyDatabase

    use admin 
    db.updateUser("test1",{roles:[{role:"readWriteAnyDatabase",db:"admin"}]})


### 1.6.6 修改密码

	语法格式
	
	db.changeUserPassword("username","newPasswd")


例子：  
修改test1用户的密码为123

    use admin  
    db.changeUserPassword("test1","123"）




每一个集合中的不同文档是可以有不同的域，这是比较灵活的，但是有时候为了保证性能，同一个集合中文档，域设置成一样的。


