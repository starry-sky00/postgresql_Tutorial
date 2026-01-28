# postgresql_Tutorial
postgresql学习笔记  

认识：postgresql是[对象关系型数据库](https://blog.csdn.net/lixin5456985/article/details/143727160)，关系型数据库使用表（tables）来存储数据，这些表之间通过关系（keys）连接。
[对象关系数据库](https://docs.pingcode.com/ask/102215.html)（Object-Relational Database, ORDB），是在关系数据库的基础上，增加了对“对象”的支持，具有如下特性：  

**对象关系数据库支持自定义的复合数据类型。**  

**对象关系数据库允许随着数据类型的定义一并提供操作这些数据的函数和过程。**  

**对象关系数据库支持继承，这意味着数据表可以像面向对象编程中的类一样，继承另一个数据表的结构和方法。**  

开发相关：在地理信息系统（GIS）中，对象关系数据库因其能够有效处理空间数据和地理信息。    
[安装教程](https://jishuzhan.net/article/1945437962933678081)  
[pg数据库的数据类型](https://postgresql.ac.cn/docs/current/datatype.html)  

建数据库：create database mydb;  
删除数据库：drop database mydb;  
建表：  
create table ppol(
id Serial not null primary key,
name varchar(10)
);
create table tb_1
(
id Serial not null primary key,
name varchar(25),
deptId int,
salary float,
constraint fk_emp_dept1 foreign key (deptId) references ppol(id)
);
**注意：由于外键的关系，ppol被叫做父表，tb_1被叫做子表**
primary key、foreign key、not null、unique、default和mysql的一样。  
修改表名：alter table ppol rename to tb_emp2;  
修改字段类型：alter table ppol alter column name type varchar(30);  
修改字段名：alter table ppol rename name to 姓名;    
添加字段： alter table ppol add column sex varchar(2) not null default 'm';
删除字段：alter table ppol drop sex;
删除约束：alter table tb_1 drop constraint fk_emp_dept1;
删除表：drop table if exists ppol,tb_1;
删除表数据：delete from ppol;
删除父表保留子表：思路（先删除两表之间的联系，然后删除父表）  


