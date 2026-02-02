# postgresql_Tutorial
postgresql学习笔记

认识：postgresql是[对象关系型数据库](https://blog.csdn.net/lixin5456985/article/details/143727160)，关系型数据库使用表（tables）来存储数据，这些表之间通过关系（keys）连接。  
[对象关系数据库](https://docs.pingcode.com/ask/102215.html)（Object-Relational Database, ORDB），是在关系数据库的基础上，增加了对“对象”的支持，具有如下特性：

**对象关系数据库支持自定义的复合数据类型。**

**对象关系数据库允许随着数据类型的定义一并提供操作这些数据的函数和过程。**

**对象关系数据库支持继承，这意味着数据表可以像面向对象编程中的类一样，继承另一个数据表的结构和方法。**

开发相关：在地理信息系统（GIS）中，对象关系数据库因其能够有效处理空间数据和地理信息。    
[安装教程](https://jishuzhan.net/article/1945437962933678081)  
## 数据库类型
[pg数据库的数据类型](https://postgresql.ac.cn/docs/current/datatype.html)当手册查，用的时候再看也行但是要知道有什么类型    
### 常用类型
#### integer:4字节整数，范围-2147483648 到 +2147483647  
#### serial：**自动递增interger**,范围1 到 2147483647 , 是通过序列实现的，因此即使没有删除任何行，“空洞”或间隙可能会出现在列中的值序列中。
#### numeric：存储大小可变，小数点前最多 131072 位数字；小数点后最多 16383 位数字
NUMERIC(precision, scale)，precision是精度，pgsql范围-1000到1000，sql标准是0到？，
scale是标度.精度表示有效位数，标度表示小数点左移/右移几位。比如NUMERIC(2, -3)表示-99000到99000，从百位四舍五入，个、十、百位一直为0，输入98999会转换成99000，输入99900会报溢出错误。
还有三个特殊值inf、-inf、NaN，这个计算速度比单精度或者双精度浮点数慢  
#### 浮点数：单精度浮点数real,双精度浮点数double precision
#### money 类型存储具有固定小数精度的货币金额,_有单位_，小数精度由数据库的 lc_monetary 设置决定。
除以money时会进行截断操作，要获得舍入结果，先将 money 值转换为 numeric，然后再转换回 money。当 money 值除以另一个 money 值时，结果是 double precision，单位消了  
#### 字符类型
varchar(n):变长，有长度限制、char(n):定长，空格填充、bpchar：变长，无长度限制，去除尾部空格、text：变长，无长度限制  
如果指定了长度 n，它必须大于零，并且不能超过 10,485,760。如果使用varchar而未指定长度，该类型将接受任意长度的字符串。如果 bpchar 缺少长度说明符，它也接受任意长度的字符串，但尾部空格在语义上是无关紧要的。如果char缺少长度说明符，它等价于 char(1)。由于额外的存储成本，char(n) 通常是这三者中最慢的。在大多数情况下，应该使用 text 或 varchar。
#### 枚举类型
声明：```CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');```每个枚举数据类型都是独立的，不能与其他枚举类型进行比较。
如果你确实需要这样做，可以编写自定义运算符，或者在查询中添加显式转换::text。
#### 数组类型
声明：
```postgresql
CREATE TABLE sal_emp (
    name            text,
    pay_by_quarter  integer[],
    schedule        text[][]
);
```
声明数组大小或维度数量仅用于文档记录；它不会影响运行时行为。  
默认情况下，PostgreSQL 使用基于一的编号约定进行数组编号。下标从1开始而不是0.  
切片：
```postgresql
SELECT schedule[1:2][1:1] FROM sal_emp WHERE name = 'Bill';
```
表示第一到二行的第一列，左闭右闭。
如果任何维度被写为切片（即包含冒号），则所有维度都被视为切片。如果下标超出了数组边界（此情况不引发错误），则返回 NULL。  
json类型：

数据类型转换：
方法一：
```postgresql
SELECT
  '100'::INTEGER,
  '01-OCT-2015'::DATE;
```
方法二：
```postgresql
SELECT
	CAST ('100' AS INTEGER);
```
建数据库：```create database mydb; ``` 
删除数据库：```drop database mydb; ```   
建表：
```postgresql
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
``` 
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
插入数据：create table tmp1(num int);insert into tmp1 values (64);  
更新语句：update ppol set name='opp' where id=1;  
查询数据：select num,num+10,num+10.567 from tmp1;**注意:**这里自动转换成了numeric类型，自动转换规则和c语言一样。
排序：select * from ppol order by name desc;默认升序，desc是降序  
Like:通配符（%，_）select * from ppol where name like '_p';会全表扫描，除非有特殊索引  
limit和offset:分页查询，limit是每页几条数据，offset是偏移量从0开始计算。（这里可以扩展怎么优化）  


比较运算符规则：在sql中是=，就是java中的==    
1.有参数为null,则运算结果为空，例如：null=null返回null,least(10,null)返回10因为null忽略不计
2.字符串和数字比较，自动将字符串转换成数字，例如'2'=2返回t

# 函数
## 数学函数
绝对值：ABS()  
圆周率：PI()  
平方根：SQRT()  
求余数：MOD(x,y)  
向上取整：CEIL()、CEILING()  
向下取整：FLOOR()  
四舍五入：ROUND(x,y),x是要舍入值，y是小数点左右几位开始舍入，如：round(232.38,1)返回232.4，round(232.38,-1)返回230  
符号函数：SIGN()返回-1，0，1分别表示负数、0、正数  
幂运算：POW(x,y)、exp()  
对数函数：LOG()是以e为底数  
等等
## 字符串函数
字符串拼接：
`concat('postgresql','18.0')`输出postgresql18.0  
`concat_ws('-','postgresql','18.0')`输出postgresql-18.0  
去除头尾空格：`trim()`,还有去除头空格：`ltrim()`,去除尾空格：`r`



