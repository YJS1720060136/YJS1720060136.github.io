# 第二章 MySql单表&约束&事务

## 2.1 DQL操作单表

### 2.1.1创建数据库、复制表

* 创建一个新的数据库 db2

~~~mysql
CREATE DATABASE bd2 CHARACTER SET utf8;
-- 使用SQLyog将db1数据库中的emp表复制到bd2中
~~~

## 2.2 排序

* 通过ORDER BY子句，可以将查询出的结果进行排序(排序只是显示效果，不会影响真实数据)

* 语法结构

~~~ mysql
SELECT 字段名 FROM 表名 [WHERE 字段 = 值] ORDER BY 字段名 [ASC / DESC]
~~~

| **ASC表示升序排序(默认)**  |
| -------------------------- |
| **DESC表示降序排序(默认)** |

### 2.2.1 单列排序

~~~mysql
-- 单列排序 按照某一个字段进行排序
-- 使用salary 字段 对emp表进行排序
SELECT * FROM emp ORDER BY salary;-- 默认升序排序
SELECT * FROM emp ORDER BY salary DESC;-- 默认降序排序
~~~

### 2.2.2 组合排序

* 同时对多个字段进行排序，如果第一个字段相同，就按照第二个字段进行排序，以此类推

~~~mysql
-- 组合排序 同时对多个字段进行排序
-- 在薪资的排序基础上，在使用id字段进行排序
SELECT * FROM emp ORDER BY salary DESC , eid;-- id字段升序排序
SELECT * FROM emp ORDER BY salary DESC , eid DESC;-- id字段降序排序
~~~

## 2.3 聚合函数

* 纵向查找，对某一列的值进行计算，返回一个单一的值
* 聚合函数会忽略null空值

* 语法结构

~~~mysql
SELECT 聚合函数(字段名) FROM 表名;
~~~

### 2.3.1 五个聚合函数

| 聚合函数    | 作用                         |
| ----------- | ---------------------------- |
| count(字段) | 统计指定列不为NULL的记录行数 |
| sun(字段)   | 计算指定列的数值和           |
| max(字段)   | 计算指定列的最大值           |
| min(字段)   | 计算指定列的最小值           |
| avg(字段)   | 计算指定列的平均值           |

#### 示例代码

~~~~mysql
# 查询员工的总数
SELECT COUNT(*) FROM emp;	-- 使用某一字段
SELECT COUNT(1) FROM emp;	-- 使用 *
SELECT COUNT(eid)FROM emp;	-- 使用 1,与 * 效果一样
-- count函数 在统计的时候会忽略空指
-- 注意：不要使用带空值的列进行统计 count

# 查看员工总薪水、最高薪水、最小薪水、平均薪水
SELECT 
    SUM(salary) AS '总薪水',
    MAX(salary) '最高薪水',	-- as可以省略
    MIN(salary) '最小薪水',
    AVG(salary) '平均薪水'
FROM emp;

# 查看薪水大于4000员工的个数
SELECT COUNT(*) FROM emp WHERE salary > 4000;

# 查询部门为教学部的所有员工的个数
SELECT COUNT(*) FROM emp WHERE dept_name  = '教学部';

# 查询部门为'市场部'所有员工的平均薪水
SELECT AVG(salary) AS '市场部平均薪水' FROM emp WHERE dept_name = '市场部';
~~~~

## 2.4 分组查询

* 分组查询指的是使用GROUP BY语句，对查询的信息进行分组，相同数据作为一组
* 分组的目的就是为了做统计操作，一般分组会和聚合函数一起使用，单独使用没有意义
* 语法格式

~~~mysql
SELECT 分组字段/聚合函数 FROM 表名 GROUP BY 分组字段 [HAVING 条件]
~~~

#### 示例代码

~~~mysql
# 通过性别字段 进行分组，求各组的平均薪资
SELECT sex '性别', AVG (salary)'按性别统计的平均薪资' FROM emp GROUP BY sex; 

# 查询所有部门信息
SELECT dept_name AS '部门名称' FROM emp GROUP BY dept_name;

# 查询每个部门的平均薪资
SELECT 
	dept_name '部门名称', 
	AVG(salary) '平均薪资' 
FROM emp 
GROUP BY dept_name;

# 查询每个部门的平均薪资，部门名称不能为null
SELECT 
    dept_name '部门名称', 
    AVG(salary)'部门平均薪资' 
FROM emp 
WHERE dept_name IS NOT NULL 
GROUP BY dept_name;

~~~

### 2.4.1 where与having的区别

| 过滤方式 | 特点                                             |
| -------- | ------------------------------------------------ |
| where    | where进行分组前的过滤<br>where后面不能写聚合函数 |
| having   | having是分组后的过滤<br>having后面可以写聚合函数 |

#### 示例代码

~~~mysql
# 查询平均薪资大于6000的部门
-- 需要在分组后，对数据进行过滤，使用关键字hiving
-- 分组操作中的having子语句，是使用分组后对数据进行过滤的，作用类似于where
SELECT 
    dept_name '部门名称',
    AVG(salary) '部门平均薪资' 
FROM emp 
WHERE dept_name IS NOT NULL  
GROUP BY dept_name
HAVING AVG(salary) > 6000;
~~~

## 2.5 limit关键字

* limit关键字的作用
  * 用于限制返回的查询结果的行数(可以通过limit指定查询多少行数据)
  * limit语法是MySql的方言，用来完成分页
* 语法结构

~~~mysql
SELECT 字段1,字段2,...FROM 表名 LIMIT offset , length;
~~~

* 参数说明

| limit offset，length;关键字可以接受一个或者两个为0或者正整数的参数 |
| ------------------------------------------------------------ |
| offset 起始行数，从0开始计数，如果忽略，则默认为0            |
| length 返回的行数                                            |

#### 示例代码

~~~mysql
# 查询emp表的前5条数据
-- 参数1 起始值,默认是0，参数2 要查询的条数
SELECT sname, sex FROM emp LIMIT 5;
SELECT * FROM emp LIMIT 0, 5;

# 查询emp表中从第四条开始，查询六条
-- 起始默认是从0开始的
SELECT * FROM emp LIMIT 3,6;
~~~

### 2.5.1 分页操作

#### 示例代码

~~~mysql
-- 分页操作 每页显示3条数据
SELECT * FROM emp LIMIT 0, 3;	-- 第一页
SELECT * FROM emp LIMIT 3, 3;	-- 第二页
SELECT * FROM emp LIMIT 6, 3;	-- 第三页

-- 分页公式	起始索引页 = （当前页 - 1）*每页条数
-- limit 是mysql中的方言
~~~

## 2.6 SQL约束

* 约束是指对数据进行一定的限制，来保证数据的完整性，有效性，正确性

* 违反约束的不正确数据将无法插入表中

* 常见的约束：


| 约束名   | 约束关键字  |
| -------- | ----------- |
| 主键约束 | primary key |
| 唯一约束 | unique      |
| 非空约束 | not null    |
| 外键约束 | foreign key |

### 2.6.1 主键约束

* 通常针对业务,每张表去设计一个主键ID
* 主键是给数据库和程序使用的,跟最终的客户无关,所以主键没有意义,只要保证不重复就好,比如身份证

| **特点** | **不可重复、唯一、非空**         |
| -------- | -------------------------------- |
| **作用** | **用来表示数据库中的每一条记录** |

* 语法格式：

~~~ mysql
字段名 字段类型 primary key
~~~

#### 示例代码

~~~mysql
-- 方式一 创建一个带有主键的表
CREATE TABLE emp2(
	eid INT PRIMARY KEY,
    ename VARCHAR(20),
    sex CHAR(1)
);
-- 查看表的结构
DESC emp2;

-- 方式二 创建
CREATE TABLE　emp2(
	eid INT,
    ename VARCHAR(20),
    sex CHAR(1),
    PRIMARY KEY(eid) -- 指定eid为主键
);

-- 方式三 创建表之后再添加主键
CREATE TABLE emp2(
	eid INT,
    ename VARCHAR(20),
    sex CHAR(1)
);
-- 通过DDL语句添加主键
ALTER TABLE emp2 ADD PRIMARY KEY(eid);
~~~

### 2.6.2 删除主键约束

~~~mysql
-- 删除主键约束 DDL语句
ALTER TABLE emp2 DROP PRIMARY KEY;
~~~

### 2.6.3 主键自增

* 语法格式

~~~mysql
auto_increment -- 主键的自动增长（字段类型必须是 整数类型）  
~~~

#### 示例代码

~~~mysql
-- 创建主键自增的表
CREATE TABLE emp2(
	-- 关键字 AUTO_INCREMENT,主键类型必须是整数类型
    eid INT PRIMARY KEY AUTO_INCREMENT,
    ename VARCHAR(20),
    sex CHAR(1)
);
~~~

### 2.6.4 修改主键自增的起始值

* 默认地AUTO_INCREMENT的开始值是1

~~~mysql
-- 创建主键自增的表，自定义主键自增起始值100
CREATE TABLE emp2(
	eid INT PRIMARY KEY AUTO_INCREMENT,
    ename VARCHAR(20),
    sex CHAR(1)
)AUTO_INCREMENT = 100;
-- 插入数据，观察主键的起始值
INSERT INTO emp2(ename, sex) VALUES('张百万','男');
~~~

### 2.6.5 delete 和truncate对自增的影响

| 清空表数据的方式 | 特点                                                         |
| ---------------- | ------------------------------------------------------------ |
| DELETE           | 只是删除表中所有数据，对自增没有影响                         |
| TRUNCATE         | truncate是将整个表删掉，然后创建一个新表<br>自增的主键，重新从1开始 |

### 2.6.6 非空约束

* **特点：某一列不允许为空**
* 语法格式： 

~~~myssql 
字段名 字段类型 not null
~~~

#### 示例代码

~~~mysql
# 非空约束
-- 为enmae字段添加非空约束
CREATE TABLE emp2(
	eid INT PRIMARY KEY AUTO_INCREMENT,
	-- 将enmae 字段添加非空约束
	ename VARCHAR(20) NOT NULL,
	sex CHAR(1)
);
~~~

### 2.6.7 唯一约束

* **特点：表中的某一列不能重复（对null值 不做判断）**
* 语法格式：

~~~mysql
字段名 字段类型 unique
~~~

#### 示例代码

~~~mysql
# 添加唯一约束
-- 创建emp3表 为ename 字段添加唯一约束
CREATE TABLE emp3(
	eid INT PRIMARY KEY AUTO_INCREMENT,
    ename VARCHAR(20) UNIQUE,
    sex CHAR(1)
);

~~~

### 2.6.8 主键约束与唯一约束的区别

* 主键约束：唯一且不能为空
* 唯一约束：唯一，但是可以为空
* 一个表中只能有一个主键，但是可以有多个唯一约束

### 2.6.9 默认值

* **特点：用来指定某一列的默认值，设置值可以覆盖默认值**
* 语法格式：

~~~mysql
字段名 字段类型 default 默认值
~~~

#### 示例代码

~~~mysql
-- 创建带有默认值的表
CREATE TABLE emp4(
	eid INT PRIMARY KEY AUTO_INCREMENT,
    -- 为ename字段添加默认值
    ename VARCHAR(20) DEFAULT '奥里给',
    sex CHAR(1)
);
~~~

## 2.7 数据库事务

* 事务是由一条或多条SQL组成的一个整体，事务中的操作，要么成功，要么整个操作就会回滚(撤销执行的操作)
* 回滚：在事务运行过程中发生某种故障，事务不能继续执行，系统将事务中对数据库的所有已完成的操作全部撤销，滚回到事务开始时状态。（在提交之前执行）

### 2.7.1 模拟转账操作

~~~mysql
-- 创建账户表 
CREATE TABLE account(    
    -- 主键    
    id INT PRIMARY KEY AUTO_INCREMENT,    
    -- 姓名    
    NAME VARCHAR(10),    
    -- 余额    
    money DOUBLE 
);
-- 添加两个用户 初始金额1000元
INSERT INTO account (NAME, money) VALUES ('tom', 1000), ('jack', 1000);

-- tom账户 -500元 
UPDATE account SET money = money - 500 WHERE NAME = 'tom';

-- jack账户 + 500元 
UPDATE account SET money = money + 500 WHERE NAME = 'jack';

-- 假设当tom 账号上 -500 元,服务器崩溃了 jack 的账号并没有+500 元，数据就出现问题了 
-- 我们要保证整个事务执行的完整性，要么都成功，要么都失败。这个时候我们就要学习如何操作事务
~~~

### 2.7.2 MySql事务·操作

* MySql中有两种方式进行事务的操作：
  * 手动提交事务
  * 自动提交事务(MySql默认的提交方式，自动提交事务)
* 每执行一条DML语句，都是一个单独的事务
* 语法格式

| **功能**     | **语句**                        |
| ------------ | ------------------------------- |
| **开启事务** | **start transaction;或者BEGIN** |
| **提交事务** | **commit;**                     |
| **回滚事务** | **rollback；**                  |

~~~mysql
START TRANSACTION
~~~

* 这个语句显示地标记一个事务的起始点

~~~mysql
COMMIT
~~~

* 表示提交事务，即提交事务的所有操作，就是将事务中所有数据库的更新都写到磁盘上的物理数据库中，事务正常结束

~~~mysql
ROLLBACK
~~~

* 表示撤销事务，即在事务运行的过程中发生了某种故障，事务不能继续执行，系统将事务中对数据库的所有已完成的操作全部撤销，回滚到事务开始时的状态

### 2.7.3 手动提交事务流程

* 执行成功的情况：开启事务 -> 执行多条SQL语句 -> 成功提交事务
* 执行失败的情况：开启事务 -> 执行多条SQL语句 -> 事务的回滚

### 2.7.4 自动提交事务

* MySQL默认每一条DML（增删改）语句都是一个单独的事务，每条语句都会自动开启一个事务，语句执行完毕自动提交事务，MYSQL默认开始自动提交事务

### 2.7.5 取消自动提交

| **on：自动提交**  |
| ----------------- |
| **off：手动提交** |

#### 示例代码

~~~ mysql
-- 登录mysql，查看autocommit状态
SHOW VARIABLES LIKE 'autocommit';

-- 把autocommit改为off
SET @@autocommit=off;
~~~

### 2.7.6 MySql的四大特性（面试考点）

* 原子性：每一个事物都是一个整体，不可以再拆分，事务中的所有SQL要么都执行成功，要么都执行失败
* 一致性：事务在执行之前，数据库的状态，与事务执行之后的状态要保持一致
* 隔离性：事务与事务之间不应该相互影响，执行时要保证隔离状态
* 持久性：一旦事务执行成功，对数据的修改是持久的。

## 2.8 MySql的事务隔离级别

### 2.8.1 数据并发访问

* 一个数据库可能拥有多个访问客户端,这些客户端都可以并发方式访问数据库. 数据库的相同数据可能被多个事务同时访问,如果不采取隔离措施,就会导致各种问题, 破坏数据的完整性

### 2.8.2 并发访问会产生的问题

* 各事务之间是隔离的，相护独立的，但是如果多个事务对数据库中的同一批数据进行并发访问的时候，就会引发一些问题，可以通过设置不同的隔离级别来解决对应的问题

| 并发访问的问题 | 说明                                                  |
| -------------- | ----------------------------------------------------- |
| 脏读           | 同一个事务读取到了另一个事务没有提交的数据            |
| 不可重复读     | 同一个事务中两次读取的数据不一致                      |
| 幻读           | 同·一个事务中，一次查询的结果，无法支撑后续的业务操作 |

### 2.8.3 四种隔离级别

* 隔离级别 从小到大 安全性是越来越高的，但是效率是越来越低的
* 根据不同的情况选择对应的隔离级别

---

| 级别 | 隔离级别                   | 功能                                            |
| ---- | -------------------------- | ----------------------------------------------- |
| 1    | 读未提交：read uncommitted | 无法防止任何问题                                |
| 2    | 读已提交：read committed   | 可以防止脏读（Oracle和SQLServer默认的隔离级别） |
| 3    | 可重复读：repeatable read  | 可以防止脏读，不可重复读（MySql默认的隔离级别） |
| 4    | 串行化：serializable       | 可以防止脏读，不可重复读，幻读                  |

* serializable串行化可以解决幻读，但是事务只能排队执行，严重影响效率，数据库不会使用这种隔离级别

### 2.8.4 隔离级别相关命令

* 设置隔离级别的语法格式

~~~mysql
set global transaction isolation level 级别名称;
~~~

#### 示例代码

~~~mysql
-- 查看隔离级别
SELECT @@tx_isolation;

-- 设置事务隔离级别，需要退出 MySQL 再重新登录才能看到隔离级别的变化
-- 修改隔离级别为读未提交
set global transaction isolation level read uncommitted;
~~~



