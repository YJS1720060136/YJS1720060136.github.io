# 第四章 MySQL索引&视图&存储过程 

## 4.1 MySQL索引

* 在数据库表中，对字段建立索引可以大大提高查询速度。通过善用这些索引，可以令MySQL的查询和 运行更加高效。

### 4.1.1 常见的索引分类

* MySql将一个表的索引都保存在同一个索引文件中, 如果对中数据进行增删改操作,MySql都会自动的更新索引

| 索引名称               | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 主键索引 (primary key) | 主键是一种唯一性索引,每个表只能有一个主键, 用于标识数据表中的每一 条记录 |
| 唯一索引 (unique)      | 唯一索引指的是 索引列的所有值都只能出现一次, 必须唯一        |
| 普通索引 (index)       | 最常见的索引,作用就是 加快对数据的访问速度                   |

### 4.1.2 主键索引

* 主键是一种唯一性索引,每个表只能有一个主键,用于标识数据表中的某一条记录
* 一个表可以没有主键，但最多只能有一个主键，并且主键值不能包含NULL

* 创建表的时候直接添加主键索引 (最常用)

~~~mysql
CREATE TABLE 表名(      
    -- 添加主键 (主键是唯一性索引,不能为null,不能重复,)    
    字段名 类型 PRIMARY KEY,   
);  
~~~

* 修改表结构 添加主键索引

~~~mysql
ALTER TABLE 表名 ADD PRIMARY KEY ( 列名 )
~~~

* 为demo1 表添加主键索引

~~~mysql
ALTER TABLE demo01 ADD PRIMARY KEY (did);
~~~

### 4.1.3 唯一索引

* 特点: 索引列的所有值都只能出现一次, 必须唯一. 唯一索引可以保证数据记录的唯一性。
* 事实上，人们创建唯一索引的目的往往不是为了 提高访问速度，而只是为了避免数据出现重复
* 创建表的时候直接添加主键索引 

~~~mysql
CREATE TABLE 表名(      
    列名 类型(长度),      
    -- 添加唯一索引    
    UNIQUE [索引名称] (列名) 
);  
~~~

* 使用create语句创建: 在已有的表上创建索引

~~~mysql
create unique index 索引名 on 表名(列名(长度))
~~~

* 修改表结构添加索引

~~~mysql
ALTER TABLE 表名 ADD UNIQUE ( 列名 )
~~~

*  为 hobby字段添加唯一索引

~~~mysql
create unique index ind_hobby on demo01(hobby)
~~~

### 4.1.4 普通索引

*  普通索引（由关键字KEY或INDEX定义的索引）的唯一任务是加快对数据的访问速度。因此，应该只 为那些最经常出现在查询条件（WHERE column=）或排序条件（ORDERBY column）中的数据列创建 索引
* 使用create index 语句创建: 在已有的表上创建索

~~~mysql
create index 索引名 on 表名(列名[长度])
~~~

* 修改表结构添加索引

~~~mysql
ALTER TABLE 表名 ADD INDEX 索引名 (列名)
~~~

* 给 dname字段添加索引

~~~mysql
# 给dname字段添加索引 
alter table demo01 add index dname_indx(dname);
~~~

### 4.1.5 删除索引

* 由于索引会占用一定的磁盘空间，因此，为了避免影响数据库的性能，应该及时删除不再使用的索 引 
* 语法格式

~~~mysql
ALTER  TABLE  table_name   DROP  INDEX  index_name;
~~~

* 删除  demo01 表中名为  dname_indx  的普通索引

~~~mysql
ALTER TABLE demo01 DROP INDEX dname_indx;
~~~

### 4.1.6 索引性能测试

~~~mysql
# 索引性能测试

-- 表中有5000000条数据
SELECT COUNT(*) FROM test_index;

-- 通过id查询一条数据
SELECT * FROM test_index WHERE id = 100001;

-- 通过dname字段查询 执行5秒
SELECT * FROM test_index WHERE dname = "name5200";

-- 执行分组查询 1分45秒
SELECT * FROM test_index GROUP BY dname;
~~~

### 4.1.7  索引的优缺点总结 

* 创建索引的原则：优先选择为经常出现在查询条件或者排序分组后面的字段，创建索引
* 索引的优点
  * 可以大大的提高查询速度
  * 减少查询中分组和排序的时间
  * 通过创建唯一索引保证数据的唯一性
* 索引的缺点
  * 创建和维护索引需要时间，数据量越大，时间就越长
  * 表中的数据进行增删改查操作时，索引也需要进行维护，降低了维护的速度
  * 索引文件需要占据磁盘空间

## 4.2 MySQL视图

### 4.2.1 概念

* 视图是一种虚拟表。
* 视图建立在已有表的基础上, 视图赖以建立的这些表称为基表。 
* 向视图提供数据内容的语句为 SELECT 语句, 可以将视图理解为存储起来的 SELECT 语句
* 视图向用户提供基表数据的另一种表现形式 

### 4.2.2 视图的作用

* 权限控制时可以使用 
  * 某几个列可以运行用户查询,其他列不允许,可以开通视图 查询特定的列, 起到权限控制的作用 
* 简化复杂的多表查询
  * 视图本身就是一条查询SQL,我们可以将一次复杂的查询 构建成一张视图, 用户只要查询视图 就可以获取想要得到的信息(不需要再编写复杂的SQL) 
  * 视图主要就是为了简化多表的查询

### 4.2.3 视图的使用

* 语法格式

~~~mysql
create view 视图名 [column_list] as select语句;
view: 表示视图 
column_list: 可选参数，表示属性清单，指定视图中各个属性的名称，默认情况下，与SELECT语句中查询 的属性相同 as : 表示视图要执行的操作 
select语句: 向视图提供数据内容

~~~

* 创建一张视图

~~~mysql
#1. 先编写查询语句 #查询所有商品 和 商品的对应分类信息 
SELECT * FROM products p LEFT JOIN category c ON p.`category_id` = c.`cid`;
#2.基于上面的查询语句,创建一张视图 
CREATE VIEW products_category_view AS SELECT * FROM products p LEFT JOIN category c ON p.`category_id` = c.`cid`;
~~~

* 查询视图 ,当做一张只读的表操作就可以

~~~mysql
SELECT * FROM products_category_view;
~~~

### 4.2.4 通过视图查询

~~~mysql
#通过 多表查询 
SELECT     cname AS '分类名称',    AVG(p.`price`) AS '平均价格'
FROM products p LEFT JOIN category c ON p.`category_id` = c.`cid` GROUP BY c.`cname`;

# 通过视图查询 可以省略连表的操作 
SELECT     cname AS '分类名称',    AVG(price) AS '平均价格' 
FROM products_category_view GROUP BY cname;

#通过连表查询 
#1.先求出鞋服分类下的最高商品价格 
SELECT     MAX(price) AS maxPrice 
FROM products p LEFT JOIN category c ON p.`category_id` = c.`cid` 
WHERE c.`cname` = '鞋服'

#2.将上面的查询 作为条件使用 
SELECT * 
FROM products p LEFT JOIN category c ON p.`category_id` = c.`cid` 
WHERE c.`cname` = '鞋服' AND p.`price` = (
    SELECT     MAX(price) AS maxPrice 
    FROM products p LEFT JOIN category c ON p.`category_id` = c.`cid` 
    WHERE c.`cname` = '鞋服');
    
#通过视图查询 
SELECT * FROM products_category_view pcv 
WHERE  pcv.`cname` = '鞋服' AND pcv.`price` = (
    SELECT MAX(price) 
    FROM products_category_view 
    WHERE cname = '鞋服')
~~~

### 4.2.5 视图与表的区别

* 视图时建立在表的基础上
* 通过视图 不要进行增删改查操作 视图主要就是用来简化查询
* 删除视图 表不受影响 删除表 视图就不在起作用了

## 4.3 MySQL存储过程

* MySQL 5.0 版本开始支持存储过程。 
* 存储过程（Stored Procedure）是一种在数据库中存储复杂程序，以便外部程序调用的一种数据 库对象。存储过程是为了完成特定功能的SQL语句集，经编译创建并保存在数据库中，用户可通过指定存储过程的名字并给定参数(需要时)来调用执行。
* 简单理解: 存储过程其实就是一堆 SQL 语句的合并。中间加入了一些逻辑控制。 

### 4.3.1  存储过程的优缺点 

* 优点:
  * 存储过程一旦调试完成后，就可以稳定运行，（前提是，业务需求要相对稳定，没有变化） 
  * 存储过程减少业务系统与数据库的交互，降低耦合，数据库交互更加快捷（应用服务器，与 数据库服务器不在同一个地区）
* 缺点:
  * 在互联网行业中，大量使用MySQL，MySQL的存储过程与Oracle的相比较弱，所以较少使 用，并且互联网行业需求变化较快也是原因之一 
  * 尽量在简单的逻辑中使用，存储过程移植十分困难，数据库集群环境，保证各个库之间存储 过程变更一致也十分困难。 
  * 阿里的代码规范里也提出了禁止使用存储过程，存储过程维护起来的确麻烦；

### 4.3.2 存储过程的创建方式一

#### 数据的准备

~~~mysql
# 商品表 
CREATE TABLE goods(  
    gid INT,  
    NAME VARCHAR(20),  
    num INT  -- 库存 
);
#订单表 
CREATE TABLE orders(  
    oid INT,  
    gid INT,  
    price INT -- 订单价格 
);
# 向商品表中添加3条数据 
INSERT INTO goods VALUES(1,'奶茶',20); 
INSERT INTO goods VALUES(2,'绿茶',100); 
INSERT INTO goods VALUES(3,'花茶',25);
~~~

* 语法格式

~~~mysql
-- 创建存储过程
-- 语法格式 
	DELIMITER $$	-- 声明语句的结束符号 自定义
	CREATE PROCEDURE 存储过程名称()  -- 声明存储过程
	BEGIN	-- 开始编写存储过程
		-- 要执行的SQL语句
	END $$	-- 存储过程结束
~~~

#### 示例代码

~~~mysql
#  编写存储过程, 查询所有商品数据
DELIMITER $$   
CREATE PROCEDURE goods_proc()   
BEGIN       
select * from goods; 
END $$   
~~~

#### 调用存储过程

* 语法格式

~~~mysql
call 存储过程名
~~~

* 示例代码

~~~mysql
 -- 调用存储过程 查询goods表所有数据 
 call goods_proc;
~~~



存储过程的创建方式二

创建一个接收参数的存储过程

语法格式

~~~mysql
create procedure 存储过程名 (IN 参数名 参数类型)

-- 创建存储工程
~~~

### 4.3.3 存储过程的创建方式二

*  IN  输入参数：表示调用者向存储过程传入值

~~~mysql
CREATE PROCEDURE 存储过程名称(IN 参数名 参数类型) 
~~~

*  接收一个商品id, 根据id删除数据

~~~mysql
DELIMITER $$   
CREATE PROCEDURE goods_proc02(IN goods_id INT)   
BEGIN           
	DELETE FROM goods WHERE gid = goods_id ; 
END $$ 
~~~

*  调用存储过程 传递参数

~~~mysql
# 删除 id为2的商品 
CALL goods_proc02(2);
~~~

### 4.3.4 存储过程的创建方式三

*  变量赋值

~~~mysql
SET @变量名=值
~~~

*  OUT 输出参数：表示存储过程向调用者传出值

~~~mysql
OUT 变量名 数据类型
~~~

*  向订单表 插入一条数据, 返回1,表示插入成功

~~~mysql
# 创建存储过程 接收参数插入数据, 并返回受影响的行数 
DELIMITER $$ 
CREATE PROCEDURE orders_proc(IN o_oid INT , IN o_gid INT ,IN o_price INT, OUT out_num INT) 
BEGIN    

-- 执行插入操作    
INSERT INTO orders VALUES(o_oid,o_gid,o_price);    
-- 设置 num的值为 1    
SET @out_num = 1;    
-- 返回 out_num的值    
SELECT @out_num; 
END $$
~~~

*  调用存储过程

~~~mysql
# 调用存储过程插入数据,获取返回值 
CALL orders_proc(1,2,30,@out_num);  
~~~

## 4.4 触发器

* 当我们执行一条sql语句的时候，这条sql语句的执行会自动去触发执行其他的sql语句

### 4.4.1  触发器创建的四个要素 
1. 监视地点（table）  
2. 监视事件（insert/update/delete）  
3.  触发时间（before/after）  
4. 触发事件（insert/update/delete）

### 4.4.2 创建触发器

* 语法格式

~~~mysql
delimiter $                 -- 将Mysql的结束符号从 ; 改为 $,避免执行出现错误 
CREATE TRIGGER  Trigger_Name  -- 触发器名，在一个数据库中触发器名是唯一的
before/after（insert/update/delete） -- 触发的时机 和 监视的事件 
on table_Name           -- 触发器所在的表 
for each row        --  固定写法 叫做行触发器, 每一行受影响，触发事件都执行 
begin               -- begin和end之间写触发事件 
end $   -- 结束标记
~~~

* 向商品中添加一条数据

~~~mysql
# 向商品中添加一条数据 
INSERT INTO goods VALUES(1,'book',40);
~~~

*  在下订单的时候，对应的商品的库存量要相应的减少，卖出商品之后减少库存量。编写触发器

~~~mysql
-- 1.修改结束符号
DELIMITER $
-- 2.创建触发器
CREATE TRIGGER t1
-- 3.设置触发的时间，以及监视的事件，监视的表
AFTER INSERT ON orders
-- 4.行触发器
FOR EACH ROW
-- 5.触发后要执行的操作
BEGIN
	-- 执行修改库存的操作 订单+1 库存-1
	UPDATE goods SET num = num - 1 WHERE gid = 4;
END $

-- 向orders表插入订单
INSERT INTO orders VALUES(1,4,25);
~~~

## 4.5  DCL(数据控制语言) 

* MySql默认使用的都是 root 用户，超级管理员，拥有全部的权限。除了root用户以外，我们还可以通 过DCL语言来定义一些权限较小的用户, 分配不同的权限来管理和维护数据库

### 4.5.1 创建用户

~~~mysql
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码'; 
~~~

| 参数   | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| 用户名 | 创建的新用户，登录名称                                       |
| 主机名 | 指定该用户在哪个主机上可以登陆，本地用户可用 localhost 如果想让该用户可以 从任意远程主机登陆，可以使用通配符 % |
| 密码   | 登录密码                                                     |

* 创建 admin1 用户，只能在 localhost 这个服务器登录 mysql 服务器，密码为 123456

~~~mysql
CREATE USER 'admin1'@'localhost' IDENTIFIED BY '123456'; 
-- 创建的用户在名字为 mysql的 数据库中的 user表中
~~~

* 创建 admin2 用户可以在任何电脑上登录 mysql 服务器，密码为 123456

~~~mysql
CREATE USER 'admin2'@'%' IDENTIFIED BY '123456'; 
-- % 表示 用户可以在任意电脑登录 mysql服务器. 
~~~

### 4.5.2 用户授权

* 语法格式

~~~mysql
GRANT 权限 1, 权限 2... ON 数据库名.表名 TO '用户名'@'主机名'; 
~~~

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| 权限 | 授予用户的权限，如 CREATE、ALTER、SELECT、INSERT、UPDATE 等。 如果要授 予所有的权限则使用 ALL |
| ON   | 用来指定权限针对哪些库和表                                   |
| TO   | 表示将权限赋予某个用户                                       |

*  给 admin1 用户分配对 db4 数据库中 products 表的 操作权限：查询 

~~~mysql
GRANT SELECT ON db4.products TO 'admin1'@'localhost';
~~~

* 给 admin2 用户分配所有权限，对所有数据库的所有表

~~~mysql
GRANT ALL ON *.* TO 'admin2'@'%'; 
~~~

### 4.5.3 查看权限

* 语法格式

~~~mysql
SHOW GRANTS FOR '用户名'@'主机名'; 
~~~

* 查看root用户权限

~~~mysql
-- 查看root用户的权限 
SHOW GRANTS FOR 'root'@'localhost';
-- GRANT ALL PRIVILEGES 是表示所有权限
~~~

### 4.5.4 删除用户

* 语法格式

~~~mysql
DROP USER '用户名'@'主机名'; 
~~~

* 删除 admin1 用户

~~~mysql
-- 删除 admin1 用户 
DROP USER 'admin1'@'localhost';
~~~

### 4.5.5 查询用户

* 选择名为 mysql的数据库,  直接查询 user表即可

~~~mysql
-- 查询用户 
SELECT * FROM USER;
~~~

## 4.6 数据库备份&还原

### 4.6.1 SQLYog 数据备份 

1. 选中要备份的数据库,右键 备份导出 ---->选择 备份数据库
2. 指定文件位置,选择导出即可


~~~mysql
-- 查看root用户的权限
SHOW GRANTS FOR 'root'@'localhost';

-- 查看admin1用户的权限
SHOW GRANTS FOR 'admin1'@'localhost';

-- 删除用户
DROP USER 'admin1'@'localhost';

-- 查询用户
SELECT * FROM USER;
~~~

### 4.6.2  SQLYog 数据恢复

* 执行SQL脚本导入

### 4.6.3 命令行备份

* 进入到Mysql安装目录的 bin目录下, 打开DOS命令行
* 语法格式

~~~mysql
mysqldump -u 用户名 -p 密码 数据库 > 文件路径 
~~~

*  执行备份, 备份db2中的数据 到 H盘的 db2.sql 文件中

~~~mysql
mysqldump -uroot -p123456 db2 > H:/db2.sql
~~~

### 4.6.4 命令行恢复

* 还原的时候需要先创建一个 db2数据库 

~~~mysql
source sql文件地址    
source E:\db2.sql
~~~



## 数据库的备份与还原

1、sqlyog方式

2、命令行的方式

* 语法格式

~~~mysql
mysqldump -u用户名 -p密码 数据库名 > 文件路径

mysqldump -uroot -p123456 db3 > E:/db03.sql

命令行导出的sql语句是没有创建数据库语句的，需要自己创建数据库
~~~

