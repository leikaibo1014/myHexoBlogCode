---
title: Oracle数据库 sqlplus常用命令
date: 2018-02-10 13:56:32
tags: [数据库, Oracle]
categories: Oracle数据库
copyright: true
toc: true
---

<!--more-->

## Oracle学习思维导图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191030164918181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xlbzExMjAxNzg1MTg=,size_16,color_FFFFFF,t_70)



## sql*plus常用命令
（1）conn[ect]:切换用户
（2）disc[onnect]:断开某个用户和数据库的连接
（3）passw[ord]:修改用户密码
（4）show user:查看当前用户
（5）exit:退出数据库
（6）desc[ribe] 表名：查看表结构
### 交互式命令：

（1）&:可以替代变量，而在变量执行的时候，需要用户输入
                   Select * from emp where deptno=’&deptno’;
                   注意：Oracle中字符串用单引号
         （2）ed[it]:编辑指定的.sql文件的   edit C:\a.sql
         （3）spool:可以将sqlplus屏幕上的内容输出到指定的文件中
                    Spool d:\a.sql         
                    Select * from emp;
 。。。查询结果
Spool off

**解锁用户和修改密码：**
（1）sqlplus连接到数据库：sqlplus sys/123456 as sysdba
（2）输入解锁语句：alter user scott account unlock;
（3）修改密码：alter user scott identified by 654321;
    	passw【ord】用户名:可以去修改其他用户的密码。
     	passw:表示修改自己的密码。