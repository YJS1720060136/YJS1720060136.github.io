# 第一章 MySQL基础与SQL入门

## 1.1基础知识

* 数据库(DataBase)是存储和管理文件的仓库
* 数据库本质上是一个文件系统，还是以文件的方法将数据保存在电脑上
* DB2是IBM公司的数据路产品，收费的超大型数据库，常在银行系统中使用
* Oracle数据库：收费的大型数据库，Oracle公司的核心产品，安全性高
* MySql数据库：开源免费，运作简单，功能强大，足以应对web应用开发，1996年开始运作，已经被Oracle公司收购，MySQL6.x开始收费
* 3306是mysql的端口号
* Windows Service Name：MySQL57

## 1.2 数据存取方式的比较

| 存储方式 | 优点                                                         | 缺点                               |
| -------- | ------------------------------------------------------------ | ---------------------------------- |
| 内存     | 速度快                                                       | 不能永久保存，数据是临时状态       |
| 文件     | 数据可以永久保存                                             | 使用IO流操作文件，不方便           |
| 数据库   | 1、数据可以永久保存<br>2、方便存储和管理数据<br>3、使用统一的方式操作数据库(SQL) | 占用资源，有些数据需要付费(Oracle) |

### 1.2.1DOS命令启动关闭数据库

* 启动数据库

~~~mysql
net start mysql57
~~~

* 关闭数据库

~~~mysql
net stop mysql57
~~~

### 1.2.2 DOS命令登录数据库

* 使用指定用户名和密码登录

~~~ mysql
mysql -u用户名 -p密码	
mysql -u用户名 -p	-- 密码可以省略
~~~

* 使用IP地址登录

~~~mysql
mysql -hIP地址 -u用户名 -p密码
-- mysql -h127.0.0.1 -uroot -p123456
~~~

* 退出登录命令

~~~ mysql
exit 或者 quit
~~~

### 1.2.3 MySql默认安装目录

| 目录/文件   | 目录内容                       |
| ----------- | ------------------------------ |
| bin目录     | 放置一些可执行文件mysql.exe    |
| docs目录    | 文档                           |
| include目录 | 包含头文件                     |
| lib目录     | 依赖库                         |
| share目录   | 用于存放字符集和语言相关的信息 |

* MySql数据文件的位置：c:\prigramData\MySQL\MySQL Server 5.7
* 数据库就是文件夹，数据表就是文件

| 目录       | 目录内容             |
| ---------- | -------------------- |
| Date目录   | 数据库和数据表的信息 |
| my.ini文件 | Mysql配置文件        |

### 1.2.4 MySql自带数据库介绍

| 数据库名           | 含义                                                         |
| ------------------ | ------------------------------------------------------------ |
| information_schema | 信息数据库-保存其他数据库的信息                              |
| mysql              | mysql核心数据库-保存的是用户和权限                           |
| performance_schema | 保存性能相关数据-监控MySql的性能                             |
| sys                | 记录了DBA所需要的信息，更加方便的让DBA了解数据库的运行情况(DBA-数据库管理员) |

## 1.3 数据库管理系统

* 数据库管理系统：指一种操作和管理维护数据库的大型软件，mysql就是一个数据库管理系统软件，安装了Mysql的电脑，叫做数据库服务器
* 数据库管理系统用来建立、使用和维护数据库、对数据库进行统一的管理
* ==数据库管理系统、数据库和表之间的关系：==
  * MySql中管理着很多数据库，在实际的开发环境中一个数据库就是一般对应一个应用，数据库当中保存着多张表，每一张表对应着不同的业务，表中保存着对应业务的数据
* MySql服务器就是一台安装了MySql数据库服务软件的电脑
* MySql数据库服务软件中管理着多个数据库
* 数据库中包含着多张表
* 表中记录着多条数据记录

## 1.4 数据库表

* 数据库中以表为组织单位存储数据

* 表类似于Java中的类，每个字段都有对应的数据类型

  类-----------------> 表

  成员变量 -------->表中字段

  对象--------------->数据记录

  ~~~java
  public class cloth{//服装类----服装表
      String Number;//描述编号的成员变量------>表中的字段编号
      String brand;//描述品牌的成员变量------->表中的字段品牌
      String price;//描述价格的成员变量------->表中的字段价格
  }
  Cloth cloth = new Cloth(A001,李宁，2000);//对象------>一条记录
  ~~~

## 1.5 SQL(重点)

### 1.5.1 概念

* SQL：结构化查询语言，是一种特殊目的的编程语言，是一种数据库查询和程序设计语言，用于存取数据以及查询，更新和管理关系数据库系统
* SQL的作用
  * 是所有关系型数据库的统一查询规范，不同的关系型数据库都支持SQL
  * 所有的关系型数据库都可以使用SQL
  * 不同的数据库之间的SQL有一些区别

### 1.5.2 通用语法

* SQL语句可以单行或多行书写，以分号结尾；(Sqlyog中不可以写分号)

* 可以使用空格和缩进来增加语句的可读性

* Mysql中使用SQL不区分大小写，一般关键字大小写，数据库名、表名、列表、小写。

* 注释方式（单行注释有空格）

~~~mysql
# show databases;	单行注释
-- show databases;	单行注释
/*
	多行注释
	show databases;
*/
~~~

### 1.5.3 SQL的分类

| 分类             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| 数据定义语言     | DDL(Data Deﬁnition Language),用来定义数据库对象：数据库、表、列等 |
| ==数据操作语言== | DML(Data Manipulation Language)，用来对数据库中表的记录进行更新 |
| ==数据查询语言== | DQL(Data Query Language)，用来查询数据库中表的记录           |
| 数据控制语言     | DCL(Date Control Language)，用来定义数据库的访问权限和安全级别及创建用户 |

## 1.6 数据库的操作

### 1.6.1 创建数据库

| 命令                                            | 说明                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| create databases 数据库名;                      | 创建指定名称的数据库==（默认latin1编码方式 ,可能会导致插入数据乱码）== |
| create databases 数据库名 character set 字符集; | 创建指定名称的数据库，并且指定字符集（一般utf8）             |

### 1.6.2 查看/选择数据库

| 命令                           | 说明                                 |
| ------------------------------ | ------------------------------------ |
| use 数据库;                    | 切换数据库                           |
| select database();             | 查看当前正在使用的数据库             |
| show databases;                | 查看mysql中有哪些数据库              |
| show create database 数据库名; | 查看一个数据库的定义信息(字符集信息) |

### 1.6.3 修改数据库

| 命令                                         | 说明                   |
| -------------------------------------------- | ---------------------- |
| alter database 数据库名 character set 字符集 | 数据库的字符集修改操作 |

### 1.6.4 删除数据库

| 命令                   | 说明                        |
| ---------------------- | --------------------------- |
| drop database 数据库名 | 从MySql中永久删除某个数据库 |

## 1.7 DDL操作数据表

### 1.7.1 MySql常见的数据类型

| 类型           | 描述                                        |
| -------------- | ------------------------------------------- |
| int            | 4字节，整型                                 |
| double（m, d） | 8字节，双精度浮点型 m总个数 d小数位         |
| varchar(n)     | 字符串型（可变长度）                        |
| char(n)        | 固定长度                                    |
| date           | 3字节，日期类型 格式2014-09-18              |
| time           | 3字节，时间类型 格式08:42:30                |
| datetime       | 8字节，日期时间类型 格式2014-09-18 08:42:30 |

* char类型与varchar类型的区别
  * char类型是固定长度的：根据定义的字符串长度分配足够的空间，存储固定长的的字符串
  * varchar类型是可变长度的：只使用字符串长度所需要的空间，存储在一定范围，有长度变化的字符串

### 1.7.2 创建表

* 创建表语法格式

~~~mysql
CREATE TABLE 表名(
	字段名称1 字段类型(长度)，
	字段名称2 字段类型 	-- 最后一行不要加逗号
);
~~~

~~~mysql
-- 创建商品分类表
表名： category
表中字段：
	分类ID：cid 整型
	分类名称：cname 字符串类型
CREATE TABLE categroy(
	cid INT,
    cname VARCHAR(20)
);
~~~

* 快速创建一个表结构相同的表(复制表结构)

~~~mysql
CERATE TABLE 新表名 like 旧表名
~~~

* 查看表结构

| 命令           | 说明                        |
| -------------- | --------------------------- |
| show tables;   | 查看当前数据库中的所有表名; |
| desc 表名;     | 查看数据表的结构            |
| describe 表名; | 查看数据表的结构            |

### 1.7.3 删除表

| 命令                      | 说明                                               |
| ------------------------- | -------------------------------------------------- |
| drop table 表名；         | 删除表（从数据库中永久删除某一张表）               |
| drop table if exists 表名 | 判断表是否存在，存在的话就删除，不存在就不执行删除 |

### 1.7.4 修改表

| 命令                                             | 说明                                       |
| ------------------------------------------------ | ------------------------------------------ |
| rename table 旧表名 to 新表名                    | 修改表的名称                               |
| alter table 表名 character set 字符集            | 修改表的字符集                             |
| alter table 表名 add 字段名称 字段类型           | 向表中添加列，关键字ADD                    |
| alter table 表名 modify 字段名称 字段类型        | 修改表中列的数据类型或者长度，关键字MODIFY |
| alter table 表名 change 旧列名 新列名 类型(长度) | 修改列名称 关键字CHANGE                    |
| alter table 表名 drop 列名;                      | 删除列 关键字DROP                          |

##### 示例代码

~~~mysql
-- 为分类表添加一个新的字段 cdesc varchar(20)
ALTER TABLE category ADD cdesc VARCHAR(20);

-- 对分类表的描述字段进行修改 类型VARCHAR(50)
ALTER TABLE category MODIFY cdesc VARCHAR(50);

-- 对分类表中的desc字段进行更换，跟换为description varchar(30)
ALTER TABLE category CHANGE cdesc description VARCHAR(30);

-- 删除分类表中descriptionn这列
ALTER TABLE category DROP description;
~~~

## 1.8 DML操作表中数据

* SQL中的DML用于对表中的数据进行增删改操作

### 1.8.1 插入数据

* 语法格式

~~~mysql
insert into 表名(字段名1，字段名2...) values(字段值1，字段值2...);
~~~

* 方式一：插入全部字段，将字段名都写出来

~~~mysql
INSTER INTO student (sid, sname, age, sex, address) VALUES(1,'孙悟空',20,'男','花果山');
~~~

* 方式二：插入全部字段，不写字段名

~~~mysql
INSTER　INTO student VALUES(1,'孙悟空',20,'男','花果山');
~~~

* 方式三：插入指定字段的值

~~~mysql
INSTER INTO category (cname) VALUES('白骨精');
~~~

#### 注意事项

1. 值与字段值必须要对应，个数相同&数据类型相同
2. 值的数据大小，必须在字段指定的长度范围内
3. varchar char date类型的值必须使用但引号，或者双引号包裹
4. 如果要插入空指，可以忽略不写，或者插入null
5. 如果插入指定字段的值，必须写上列名

### 1.8.3 更改数据

| 格式                                                      | 说明           |
| --------------------------------------------------------- | -------------- |
| update 表名 set 列名 = 值                                 | 不带条件的修改 |
| update 表名 set 列名 = 值 [where 条件表达式: 字段名 = 值] | 带条件的修改   |

#### 示例代码

~~~mysql
-- 不带条件的修改
UPDATE student SET sex = '女';

-- 带条件的修改
UPDATE student SET sex = '男' WHERE sid = 3;

-- 一次修改多个列，将sid为2的学员，年龄改为20,地址改为北京
UPDATE student SET age = 20,address = '北京' WHERE sid = 2;
~~~

### 1.8.4 删除数据

* 语法格式一：删除所有数据

~~~mysql
delete from 表名
~~~

* 语法格式二：指定条件删除数据

~~~mysql
delete from 表名 [where 字段名 = 值]
~~~

* 删除表中所有数据

~~~mysql
truncate table student;
~~~

#### 示例代码

~~~
-- 删除sid为1的数据
DELETE FROM student WHERE sid = 1;

-- 删除所有数据
DELETE FROM student;
~~~

#### 删除表中所有数据的两种方法

* delete from 表名; 不推荐，有多少条记录，就执行多少次删除，效率低
* truncate table 表名; 推荐，先删除整张表，然后再重新创建一张一模一样的表，效率高

## 1.9 DQL查询表中数据

### 1.9.1 准备数据

~~~mysql
#创建员工表 
CREATE TABLE emp(    
    eid INT,    
    ename VARCHAR(20),    
    sex CHAR(1),    
    salary DOUBLE,    
    hire_date DATE,    
    dept_name VARCHAR(20) 
);
#添加数据 
INSERT INTO emp VALUES(1,'孙悟空','男',7200,'2013-02-04','教学部'); 
INSERT INTO emp VALUES(2,'猪八戒','男',3600,'2010-12-02','教学部'); 
INSERT INTO emp VALUES(3,'唐僧','男',9000,'2008-08-08','教学部'); 
INSERT INTO emp VALUES(4,'白骨精','女',5000,'2015-10-07','市场部'); 
INSERT INTO emp VALUES(5,'蜘蛛精','女',5000,'2011-03-14','市场部'); 
INSERT INTO emp VALUES(6,'玉兔精','女',200,'2000-03-14','市场部'); 
INSERT INTO emp VALUES(7,'林黛玉','女',10000,'2019-10-07','财务部'); 
INSERT INTO emp VALUES(8,'黄蓉','女',3500,'2011-09-14','财务部'); 
INSERT INTO emp VALUES(9,'吴承恩','男',20000,'2000-03-14',NULL); 
INSERT INTO emp VALUES(10,'孙悟饭','男', 10,'2020-03-14','财务部');
INSERT INTO emp VALUES(11,'兔八哥','女', 300,'2010-03-14','财务部');
~~~

### 1.9.2 简单查询

* ==查询不会对数据库中的数据进行修改，只是一种显示数据的方式  关键字SELECT==
#### 语法格式

~~~mysql
select 列名 from 表名
~~~

#### 示例代码

~~~mysql
-- 查询emp 表中的所有数据
SELECT * FROM emp;	-- 使用 * 表示所有列

-- 查询所有数据 只显示id和name
SELECT eid, ename FROM emp;

-- 别名查询 使用关键字as
SELECT 
	eid AS '编号',
	ename AS '姓名',
	sex AS '性别',
	salary AS '薪资',
	hire_date '入职时间',
	dept_name '部门名称'
FROM emp;

-- 去重操作查询共有几个部门 关键字 distinct
-- 使用distinct关键字，去掉重复部门信息
SELECT DISTINCT dept_name FROM emp;

-- 将员工薪资数据 +1000 进行展示
-- 运算查询（查询结果参与运算）
SELECT ename, salary+1000 AS salary FROM emp;
~~~

### 1.9.3 条件查询

* 如果查询语句中没有设置条件，就会查询所有的行信息，在实际应用中，一定要指定查询条件，对记录进行过滤

#### 语法格式

~~~mysql
select 列名 from 表名 where 条件表达式
-- 先取出表中的每条数据，满足条件的数据就返回，不满足的就过滤掉
~~~

#### 比较运算符

| 运算符             | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| > < <= >= = < > != | 大于、小于、小于等于、大于等于、不等于                       |
| BETWEEN....AND...  | 显示某一区间的值                                             |
| IN(集合)           | 集合表示多个值，使用逗号分隔，例如：name in(悟空，八戒)<br>in中的每个数据都会作为一次条件，只要满足条件就会显示 |
| LIKE '%张%'        | 模糊查询                                                     |
| IS NULL            | 查询某一列为NULL的值，注：不能写 = NULL                      |

#### 逻辑运算符

| 运算符  | 说明               |
| ------- | ------------------ |
| And &&  | 多个条件同时从成立 |
| Or \|\| | 多个条件任一成立   |
| Not     | 不成立，取反       |

#### 示例代码

~~~mysql
# 查询员工姓名为黄蓉的员工信息
-- 1.查哪张表 2.查哪个字段 3.查询条件
SELECT * FROM emp WHERE ename = '黄蓉';

# 查询薪水价格为5000的员工信息表
SELECT * FROM emp WHERE salary = 5000;

# 查询薪资价格不是5000的所有员工信息
SELECT * FROM emp WHERE salary != 5000;
SELECT * FROM emp WHERE salary <> 5000;

# 查询薪水价格大于6000元的所有员工信息
SELECT * FROM emp WHERE salary > 6000;

# 查询薪水价格在5000到10000之间所有员工信息
SELECT * FROM emp WHERE salary BETWEEN 5000 AND 10000;
SELECT * FROM emp WHERE salary >= 5000 AND salary <= 10000;

# 查询薪水价格是3600或7200或者20000的所有员工信息
SELECT * FROM emp WHERE salary = 3600 OR salary = 7200 OR salary = 20000;
SELECT * FROM emp WHERE salary IN(3600,7200,20000);
~~~

### 1.9.4 模糊查询

* 条件查询是先取出表中的每条数据，满足条件的就返回，不满足的就过滤掉

#### 模糊查询 通配符

| 通配符 | 说明                                   |
| ------ | -------------------------------------- |
| %      | 表示匹配任意多个字符串                 |
| _      | 表示匹配一个字符串，__两个表示两个字符 |

#### 示例代码

~~~mysql
# 查询含有'精'字的所有员工信息
SELECT * FROM emp WHERE ename LIKE '%精%'; 

# 查询以'孙'开头的所有员工信息 
SELECT * FROM emp WHERE ename LIKE '孙%';

# 查询第二个字为'兔'的所有员工信息 
SELECT * FROM emp WHERE ename LIKE '_兔%';

# 查询没有部门的员工信息 
SELECT * FROM emp WHERE dept_name = NULL; -- 错误方式
SELECT * FROM emp WHERE dept_name IS NULL;

# 查询有部门的员工信息
SELECT * FROM emp WHERE dept_name IS NOT NULL;
~~~

### 补充知识点

* 多行输入，执行哪一行语句，就将光标放在哪一行
* varchar为什么经常设置20，一般应该设置多少，还有char之类的，有没有一个规范
* 如果添加数据的话，最后一个字段一定不要加逗号
* 给列名起别名的过程中，as可以省略
* mysql中对于null的判断不能用等于号