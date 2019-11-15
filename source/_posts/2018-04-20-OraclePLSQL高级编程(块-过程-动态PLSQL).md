---
title: OraclePL/SQL高级编程(块 过程 动态PL/SQL)
date: 2018-04-20 14:56:32
tags: [数据库, Oracle]
categories: Oracle数据库
copyright: true
toc: true
---

# 1.PLsql是什么？
   PL/SQL(procdure language/sql)  : 是对oracle标准的一个sql语言的扩展，是在oracle数据库上进行编程的语言。
  <!--more-->
# 2.为什么要用PL/SQL？
   （1）SQL语句不能进行模块化编程，  例如淘宝：如果仅用sql语句的话，那么下一个订单可能需要发送好几条SQL语句
    这样不方便， 写好一个模块，专门完成下订单的功能，当需要下订单的时候，只需要传入参数，调用模块就可以了
     （2）执行速度上：传统的sql，涉及到sql语句的网络传输时间，还有一个是dbms对sql语句的一个编译的时间
          效率不高    PLsql：sql语句是写在数据库中的只需要编译一次，并且还是省去了sql语句的网络传输时间，效率比较高
   （3）安全性的问题：sql语句如果直接写在程序或者客户端中，在网络传输的过程中会产生一些不安全因素
      比如sql注入等。PLSQL：sql语句是写在数据库里面的，不需要网络传输，避免了这个问题
 （4）传统sql浪费带宽，而plsql节省带宽
     
# PLSQL的缺点

 PL/SQL的一致性不好。（oracle的存储过程，放到其他数据库中不能用，需要重新编写）

# 4.快速入门：
   问题：编写一个过程，往emp表中中插入一条记录


```sql
create procedure prol is
      begin 
      insert into emp(empno,ename,sal,deptno) values(1011,'lili',3000,20);
      end;    
      如何调用：exec prol;  
```

## 总结; 
  **(1)语法**   
  注意过程中，语句后面需要加分号   语句最后 / 结束

```
create procedure 过程名(参数1 参数类型1，参数2 参数类型2) is 
变量定义
begin
执行部分
 exception
    异常处理部分
  end;
```

（2）过程调用的方法

```
     exec  过程名（参数1，参数2...）
     
     问题：创建一个过程：输入一个员工编号，删除emp表中对象的员工记录
     create procedure pro2(no number) is
     begin
       delete from emp where empno=no;
     end;   
```

 --注意：数据类型不能加长度    --=====  show error  可以查看错误是什么
 --出现编译错误  show  error 可以查看错误的详细信息  也可以把错误的编号复制到百度上
       
# --5.PL/SQL可以做什么事情？  创建 过程  函数，包（包体） 触发器==>>基础都是块编程
6.PL/SQL块编程
（1）注释：单行注释：--    
                多行注释  /*  */
                
（2）标识符的命名规范：
            

 - [ ] 当定义变量的时候，以v_  比如v_ename
 - [ ] 当定义常量的时候  以c_开头
 - [ ] 当定义游标的时候，用_cursor结尾
 - [ ] 当定义例外（异常）的时候，用e_开头

   ​             

 （3）块结构
      

      declare(可选)
                声明变量，常量，游标，例外和复杂的数据类型
                begin
                  执行部分 ：要执行的PL/SQL语句和SQL语句
                exception
                  异常处理部分：主要处理各种错误
                end;
   案例（只包括执行部分的SQL块）输出一个Hello Worder！


```sql
 begin 
         dbms_output.put_line('Hello Word');
       end;  
       set serveroutput on 
```
       注意（1）dbms_output.put_line(内容)；Oracle 中的输出语句
              --  dbms_output是Oracle所提供的包（类似于JAVA的包）该包中包含了一些过程put_line是dbms_output
              --的一个过程
       --包含定义部分和执行部分的PL/SQL块

案例：根据用户的输入的雇员编号，输出该雇员的名字
       


```sql
 declare
       V_ename varchar2(10);
       begin
         select ename into v_ename from emp where empno=&no;
         dbms_output.put_line('姓名是'||v_ename);
       end;
```

       --注意：变量的定义：变量名  变量类型 （长度）;
        --select ename into e_ename from emp where empno=&no
        --select....into...from   表示是对变量e_ename赋值
        --empno=&no  符号表示需要从键盘接收一个empno，

案例：将上述PL/SQL块改为一个过程
        
```sql
        create (replace) procedure pro3 (v_empno number) is      --(replace)有就替换，没有就创建
        v_ename emp.ename%type;
        begin
          select ename into v_ename from emp where empno=v_empno;
          dbms_output.put_line('姓名是'||v_ename);
        end;
```
        --总结：过程中，变量定义是出现在is和begin之间的，没有declare
       --emp.ename%type  表示v_ename 的类型和emp表的ename的数据类型和长度完全相同

   案例：包含定义部分，执行部分和例外处理部分的PL/SQL块 
        问题：如果在上述案例输入了不存在的员工编号，这个时候会报错，怎么处理？


```sql
 declare
        v_ename emp.ename%type;
        begin
          select ename into v_ename from emp where empno=&no;
          dbms_output.put_line('姓名是'||v_ename);
          exception
            when no_data_found then
              dbms_output.put_line('你输入的编号不存在');
        end;
```

        --异常处理的结构基本语法：
        exception
           when 异常名称 then
             /*
             对异常处理的代码
             */
           when 异常名称 then
              /*
             对异常处理的代码
             */
       --有些时候，异常还用来做逻辑跳转
案例：输入员工编号，显示员工姓名，如果我们输入一个不存在的员工编号，就在emp表中插入一条记录
              --(1002,'马大哈')
              
        

```sql
  declare
              v_ename emp,ename%type;
              begin
                select ename into v_ename from emp where empno=&no;
                dbms_output.put_line('姓名是'||v_ename);
                exception
                  when no_data_found then
                    insert into emp(empno,ename)values(1002,'马大哈');
               end;

 declare
              v_ename emp.ename%type;
              v_ename emp.empno%type:=&no;
              begin
                select ename into v_ename from emp where empno=v_empno;
                dbms_output.put_line('姓名是'||v_ename);
                exception
                  when no_data_found then
                    insert into emp(empno,ename)values(1002,'马大哈');
               end;
```

  变量的赋值：在PL/SQL中变量的赋值是用 ：=  例如v_ename emp.ename%type:='JONE';
               


```sql
        declare
               v_emp emp%rowtype;
               begin 
                 select *into v_emp from emp where empno=7839;
                 dbms_output.put_line('姓名是'||v_emp.ename||'工资是'||v_emp.sal);
               end;
```

            --总结一下：%rowtype表示记录类型，v_ename emp%rowtype 
              --使用：v_emp.ename(字段名)

# 7.过程的进一步讲解
--（1）过程中，不但可以指定输入参数，也可以指定输出参数
      create procedure 过程名(参数1 参数类型1，参数2 参数类型2) is 
      变量定义
      begin
        执行部分
      exception
        异常处理部分
      end;      
      
       --注意1：过程可以指定多个输入和输出参数，分别是参数 in 参数类型：参数 out 参数类型
       --注意2：如果没有指明的情况下，默认的是in（即输入参数）

  编写一个过程，可以输入员工的姓名，新的工作，可以修改雇员的工资

```sql
    create procedure pro4(name in varchar2,new_sal in number) is
       begin
         update emp set sal=new_sal where ename=name;
       end;     
```

一下是我在学习的过程中，具体的案例，结合案例对PLSQL的流程控制做进一步的说明

# PL/SQL的流程控制


--案例1：编写一个存储过程，输入一个雇员姓名，如果该员工工资低于2000，给该员工工资增加10%

```sql
create or replace procedure updateSal(spName varchar2) is
v_sal emp.sal%type;
begin
  select sal into v_sal from emp where ename=spName;
  if v_sal<2000 then
    update emp set sal=sal*1.1 where ename=spName;
  end if;
end;
exec updateSal('SMITH');
```

    总结：if...then...end if;如果if后面的条件满足，则执行then后面的语句

 

--案例2：编写一个存储过程，输入一个雇员姓名，若奖金非空，在原基础上加100，若奖金为0或空，把奖金设为200

```sql
create or replace procedure updateComm(spName varchar2) is
v_comm emp.comm%type;
begin
  select nvl(comm,0) into v_comm from emp where ename=spName;
  if v_comm=0 then
    update emp set comm=200 where ename=spName;
  else
    update emp set comm=comm+100 where ename=spName;
  end if;
end;
```

    总结：if...then...else...end if;如果if后条件成立，执行then后面语句，否则执行else后面语句

 

--案例3：编写一个存储过程，如果职位是president,工资+1000，manager，+500,其它岗位+200

```sql
create or replace procedure updateSal1(spName varchar2) is
v_job emp.job%type;
begin
  select job into v_job from emp where ename=spName;
  if v_job='PERSIDENT' then
    update emp set sal=sal+1000 where ename=spName;
  elsif v_job='MANAGER' then
    update emp set sal=sal+500 where ename=spName;
  else
    update emp set sal=sal+200 where ename=spName;
  end if;
end;
```
     总结：if 条件1 then 执行语句1 elsif 条件2 then 执行语句2 else 执行语句3 end if;

--案例4：创建一张表users，循环插入10条记录

```sql
create table users(id number(3),name varchar2(10));
create or replace procedure insertTable(spName varchar2) is
v_num number:=1;
begin
  loop
    insert into users values(v_num,spName);
    v_num:=v_num+1;
    exit when v_num=11;
  end loop;
end;
```

    总结：loop...end loop:首先定义一个循环变量，其次在loop和end loop之间一定要写退出循环的条件，否则就是死循环。

--案例5:编写一个存储过程，可以输入用户名，并循环往users表中添加10条记录

```sql
create or replace procedure insertTable1(spName varchar2) is
v_num number:=11;
begin
  while v_num<=20 loop
    insert into users values(v_num,spName);
    v_num:=v_num+1;
  end loop;
end;
```

    --总结：while 循环条件 loop 执行内容;循环控制语句;end loop;  注意：必须有退出循环的条件，否则是死循环

 

--案例6：使用for循环实现案例5功能

```sql
create or replace procedure insertTable2(spName varchar2) is
begin
  for i in 21..30 loop
    insert into users values(i,spName);
  end loop;
end;
```

     总结：for 循环变量 in 21..30(序列，表示21到30的一个序列)loop...end loop;

--案例7：用三种循环语句实现1+2+...+100

```sql
declare
v_counter number:=1;
v_sum number:=0;
begin
  loop
    v_sum:=v_sum+v_counter;
    v_counter:=v_counter+1;
    exit when v_counter=101;
  end loop;
  dbms_output.put_line('和是：'||v_sum);
end;
```

 

```sql
declare
v_counter number:=1;
v_sum number:=0;
begin
  while v_counter<=100 loop
    v_sum:=v_sum+v_counter;
    v_counter:=v_counter+1;
  end loop;
  dbms_output.put_line('和是：'||v_sum);
end;
```

 

```sql
declare
v_sum number:=0;
begin
  for i in 1..100 loop
    v_sum:=v_sum+i;
  end loop;
  dbms_output.put_line('和是：'||v_sum);
end;
```


--案例8：case语句，从键盘接受一个输入，如果输入A，输出优秀，输入B，输出良好，输入C输出中等，其它情况，输出不及格

```sql
declare
v_grade char(2):=&grade;
begin
  case v_grade
    when 'A' then
      dbms_output.put_line('优秀');
    when 'B' then
      dbms_output.put_line('良好');
    when 'C' then
      dbms_output.put_line('中等');
    else
      dbms_output.put_line('不及格');
   end case;
end;
```

--总结：case 变量 when 值1 then 操作1；when 值2 then 操作2;...else 操作; end case;

# 2.动态SQL:
动态SQL：编译期间SQL 语句是不确定的，并且在运行时允许发生变化
--应用场合：要执行一个DDL语句时;需要增加程序的灵活性时;使用包DBMS_SQL动态执行SQL语句时
--案例1：编写pl/sql块，创建一张test001(id number(2),name varchar2(10));

```sql
begin
  execute immediate 'create table test001(id number(2),name varchar2(10))';
end;
```

总结：PLSQL块中不能直接执行DDL语句，所以可以用动态sql去执行
--execute immediate sql语句

```sql
declare
stmt varchar2(200):='create table test002(id number(2),name varchar2(10))';
begin
  execute immediate stmt;
end;
```


--案例2：编写一个pl/sql块，往test001中插入一条记录，要求值是从键盘输入

```sql
declare
v_id test001.id%type:=&id;
v_name test001.name%type:=&name;
begin
  execute immediate 'insert into test001 values(:1,:2)' using v_id,v_name;
end;
```

--总结：execute immediate sql语句 using 值1，值2，...;
--:1,:2：需要用到变量的地方用:1,:2来代替

--案例3：查询test001，从键盘接收id，输出其姓名

```sql
declare
v_id test001.id%type:=&id;
v_name test001.name%type;
begin
  execute immediate 'select name from test001 where id=:1' into v_name using v_id;
  dbms_output.put_line('姓名是：'||v_name);
end;
```
     总结：execute immediate sql语句 into 变量1 using 变量2;
    
    (1)sql语句中没有select...into...
    (2)where id=:1:1是占位符，表示这个地方在运行的时候需要有一个值替代

--案例4：从键盘输入一个员工编号，查询该员工的姓名和工资，如果工资小于2000元，那么给他增加500，返回增加后的工资。

```sql
declare
v_empno emp.empno%type:=&no;
v_ename emp.ename%type;
v_sal   emp.sal%type;
begin
  select ename,sal into v_ename,v_sal from emp where empno=v_empno;
  if v_sal<2000 then
    execute immediate 'update emp set sal=sal+500 where empno=:1 returning sal into :2' using v_empno returning into v_Sal;
  end if;
  dbms_output.put_line('新工资是：'||v_Sal);
end;
```

    总结：execute immediate sql语句 using 变量1 returning into 变量2;
    update emp set sal=sal+500 where empno=:1 returning sal into :2 意思是更新sal,返回更新后的工资。

--案例5：从键盘接受一个员工编号，删除该员工信息

    declare
    v_empno emp.empno%type:=&no;
    begin
      execute immediate 'delete from emp where empno=:num' using v_empno;
    end;

总结：占位符在这里可以是:num形式

# 3.异常处理
--案例1：编写一个PL/SQL块，查询emp表中员工的工资

```sql
declare
v_sal emp.sal%type;
begin
  select sal into v_sal from emp;
exception
  when too_many_rows then
    dbms_output.put_line('找到多条记录！');
  when others then
    dbms_output.put_line('未知异常！');
end;
```
    用户抛出异常。
案例2：从键盘输入一个empno,查询该员工的工资，如果工资小于2000，报异常。

```sql
declare
v_sal emp.sal%type;
v_empno emp.empno%type:=&no;
myexp exception;
begin
  select sal into v_sal from emp where empno=v_empno;
  if v_sal<2000 then
    raise myexp;
  end if;
exception
  when myexp then
    dbms_output.put_line('工资太少了，该加工资了！');
end;
```

    总结：（1）declare中预定义一个异常;(2)在执行部分，触发异常raise myexp;（3）在exception中处理异常。






​      
​      
​      