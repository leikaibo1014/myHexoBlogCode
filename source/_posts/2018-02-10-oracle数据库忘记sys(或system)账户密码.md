---
title: oracle数据库忘记sys(或system)账户密码
date: 2018-01-12 13:56:32
tags: [数据库, Oracle]
categories: Oracle数据库
copyright: true
toc: true
---

## 一、忘记除SYS、SYSTEM用户之外的用户的登录密码。

 用SYS (或SYSTEM)用户登录: sqlplus sys/password as sysdba
 使用如下语句修改用户的密码: alter user user_name identified by newpassword;
 注意：密码不能全是数字。并且不能是数字开头。否则会出现：ORA-00988: 口令缺失或无效

<!--more-->

## 二、忘记SYS用户，或者是SYSTEM用户的密码。

 如果是忘记SYSTEM用户的密码，可以用SYS用户登录。然后用ALTER USER 命令修改密码：
 CONN SYS/PASS_WORD AS SYSDBA;
 ALTER USER SYSTEM IDENTIFIED BY "newpass";
 如果是忘记SYS用户的密码，可以用SYSTEM用户登录。然后用ALTER USER 命令修改密码。
 CONN SYSTEM/PASS_WORD ;
 ALTER USER SYSTEM IDENTIFIED BY newpass;
## 三、如果SYS,SYSTEM用户的密码都忘记或是丢失。

 可以使用ORAPWD.EXE 工具修改密码。
 开始菜单->运行->输入‘CMD’,打开命令提示符窗口，输入如下命令：
 orapwd file=D:\oracle10g\database\pwdctcsys.ora password=newpass
 这个命令重新生成了数据库的密码文件。密码文件的位置在ORACLE_HOME目录下的\database目录下。
 这个密码是修改sys用户的密码。除sys其他用户的密码不会改变。
不过Oracle提供了两种验证方式，一种是OS验证，另一种密码文件验证方式，如果是第一种方式用以下方法修改密码：
　　sqlplus /nolog;
　　connect / as sysdba
　　alter user sys identified by ;
　　alter user system identified by ;
　　如果是第二种方法就用上述方式修改，也可以下方法修改密码：
　　orapwd file=pwdxxx.ora password=你设定的新密码 entries=10
　　设定完后，重新启动服务，再次登陆就可以了。
## oracle 11g

在本机安装完Oracle以后，不记得sys用户的密码了，采用如下方法可以修改密码：
1.打开cmd，输入sqlplus /nolog，回车；输入“conn / as sysdba”;输入“alter user sys identified by 新密码”，注意：新密码最好以字母开头，否则可能出现错误Ora-00988。有了这个方法后，只要自己对oracle服务器有管理员权限，不记得密码的时候就可以随意修改密码了。
2.在命令行执行如下命令：sqlplus “/@服务名 as sysdba”,然后在sqlplus中alter user sys identified by 新密码;
alter user system identified by 新密码;
3.运行到C盘根目录
1)输入：SET ORACLE_SID = 你的SID名称
2)输入：sqlplus/nolog
3)输入：connect/as sysdba
4)输入：alert user sys identified by sys
5)输入：alert user system identified by system
6)更改完成，密码是Oracle数据库初始密码
4.首先,在CMD下输入SQLPLUS/NOLOG然后再在出来的界面中打入CONN/AS SYSDBA,这样就会以本地系统登录的用户为信任用户进入数据库的操作.解决这个问题的方式有两种,一个是：ALTER USER （USERNAME） IDENTIFIED BY “密码”;这个是可以改变USERNAME的密码.当然这个USERNAME必须已经存在的
另一种是：CREATE USER (USERNAME) IDENTIFIED BY “密码”;改变用户权限的命令是：GRANT ROLES TO （USERNAME）;以上2种方法都是针对ORACLE 9I的版本 。
5.用orapwd.exe命令，可以修改命令。
orapwd file=’/oracle/pwdsid.ora’ password=123456这个命令是修改sys用户的密码。你把生成的文件覆盖原来的密码文件。除sys其他用户的密码不会改变。
6.su - oracle
sqlplus /nolog
conn / as sysdba
startup (如果数据库不是处于启动状态则启动)
alter user sys identified by 123456


然后就可以使用sys用户密码登陆了
操作系统认证方式
conn / as sysdba
alter user sys identified by xxx

### 一、遇到的问题：

1.、忘记除SYS、SYSTEM用户之外的用户的登录密码。

2.、忘记SYS用户，或者是SYSTEM用户的密码。

3.、如果SYS,SYSTEM用户的密码都忘记或是丢失。

 

### 二、解决方案：

1. 忘记除SYS、SYSTEM用户之外的用户的登录密码。

用SYS (或SYSTEM)用户登录，使用如下语句修改用户的密码。

CONN SYS/PASS_WORD AS SYSDBA;

ALTER USER SYSTEM IDENTIFIED BY newpass;


注意：密码不能全是数字。并且不能是数字开头。否则会出现：ORA-00988: 口令缺失或无效

 

2. 忘记SYS用户，或者是SYSTEM用户的密码。

（1）如果是忘记SYSTEM用户的密码，可以用SYS用户登录。然后用ALTER USER 密令修改密码。


```
CONN SYS//PASS_WORD AS SYSDBA;
ALTER USER SYSTEM IDENTIFIED BY newpass;
```
（2）如果是忘记SYS用户的密码，可以用SYSTEM用户登录。然后用ALTER USER 密令修改密码。

```
CONN SYSTEM//PASS_WORD ;
ALTER USER SYSTEM IDENTIFIED BY newpass;
```

3. 如果SYS,SYSTEM用户的密码都忘记或是丢失。这一项尤其重要。可以使用ORAPWD.EXE 工具修改密码。

开始菜单->运行->输入‘CMD',打开命令提示符窗口，输入如下命令：
```
orapwd file=D:\oracle\product\10.2.0\db_1\database\pwdctcsys.orapassword=newpass
```

这个命令重新生成了数据库的密码文件。密码文件的位置在ORACLE_HOME目录下的\database目录下。这个密码是修改sys用户的密码。除sys和system其他用户的密码不会改变。