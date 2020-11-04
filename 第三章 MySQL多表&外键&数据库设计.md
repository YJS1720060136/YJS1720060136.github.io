# 第三章 MySQL多表&外键&数据库设计

## 3.1 多表

### 3.1.1 多表的简述

* 一个项目通常需要很多张表才能完成，一个商城项目的数据库会有：用户表、分类表、商品表、订单表

## 3.2 单表的缺点

### 3.2.1 数据准备

~~~mysql
-- 创建一个数据库
CREATE DATABASE db3 CHARACTER SET utf8;

-- 创建一个员工表 主键自增
CREATE TABLE emp(
	eid INT PRIMARY KEY AUTO_INCREMENT,
    ename VARCHAR(20),
    age INT,
    dep_name VARCHAR(20),
    dep_location VARCHAR(20)
);

-- 添加数据  
INSERT INTO emp (ename, age, dep_name, dep_location) VALUES ('张百', 20, '研发部', '广州'); 
INSERT INTO emp (ename, age, dep_name, dep_location) VALUES ('赵四', 21, '研发部', '广州'); INSERT INTO emp (ename, age, dep_name, dep_location) VALUES ('广坤', 20, '研发部', '广州'); INSERT INTO emp (ename, age, dep_name, dep_location) VALUES ('小斌', 20, '销售部', '深圳'); INSERT INTO emp (ename, age, dep_name, dep_location) VALUES ('艳秋', 22, '销售部', '深圳'); INSERT INTO emp (ename, age, dep_name, dep_location) VALUES ('大玲', 18, '销售部', '深圳');
~~~

### 3.2.3 单表的问题

* 冗余, 同一个字段中出现大量的重复数据

### 3.2.4 解决方案

* 设计为两张表

~~~mysql
-- 重新创建两张表 部门表和员工表

-- 创建部门表
-- 一方 主表
CREATE TABLE department(     
    id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键自增        
    dep_name VARCHAR(30),	-- 部门名称       
    dep_location VARCHAR(30)	-- 工作地点 
);

-- 创建员工表 
-- 多方 ,从表 
CREATE TABLE employee(    
    eid INT PRIMARY KEY AUTO_INCREMENT,    
    ename VARCHAR(20),    
    age INT,    
    dept_id INT -- 外键字段对应主表的主键
);


-- 添加部门数据
-- 添加2个部门 
INSERT INTO department VALUES(NULL, '研发部','广州'),(NULL, '销售部', '深圳'); 
SELECT * FROM department

-- 添加员工,dep_id表示员工所在的部门 
INSERT INTO employee (ename, age, dept_id) VALUES ('张百万', 20, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('赵四', 21, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('广坤', 20, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('小斌', 20, 2); 
INSERT INTO employee (ename, age, dept_id) VALUES ('艳秋', 22, 2); 
INSERT INTO employee (ename, age, dept_id) VALUES ('大玲子', 18, 2); 

SELECT * FROM employee;
~~~

* 员工表中有一个字段dept_id 与部门表中的主键对应,员工表的这个字段就叫做外键 

## 3.3 外键约束

### 3.3.1 外键知识

* 外键指的是在 从表中与主表的主键对应的那个字段,比如员工表的 dept_id,就是外键 
* 使用外键约束可以让两张表之间产生一个对应关系,从而保证主从表的引用的完整性
* 添加外键约束,就会产生强制性的外键数据检查, 从而保证了数据的完整性和一致性,


### 3.3.2 主表和从表

* 主表：主键ID所在的表，约束别人的表，一的一方
* 从表：外键所在的表，被约束的表，多的一方

### 3.3.3 创建外键约束

* 新建表时添加外键

~~~mysql
-- 新建表时添加外键
[CONSTRAINT] [外键约束名称] FOREIGN KEY(外键字段名) REFERENCES 主表名(主键字段名) 
~~~

* 已有表添加外键
* [CONSTRAINT] [外键约束名称]创建的时候可以不写

~~~mysql
-- 已有表添加外键
ALTER TABLE 从表 ADD [CONSTRAINT] [外键约束名称] FOREIGN KEY (外键字段名) REFERENCES 主表(主键字段名);
ALTER TABLE 从表 ADD FOREIGN KEY (外键字段名) REFERENCES 主表(主键字段名);
~~~

#### 示例代码

~~~mysql
-- 创建 employee表,添加外键约束 
CREATE TABLE employee(    
    eid INT PRIMARY KEY AUTO_INCREMENT,    
    ename VARCHAR(20),    
    age INT,    
    dept_id INT,    
    -- 添加外键约束    
    CONSTRAINT emp_dept_fk FOREIGN KEY(dept_id) REFERENCES department(id) 
);

-- 插入数据
-- 正常添加数据 (从表外键 对应主表主键) 
INSERT INTO employee (ename, age, dept_id) VALUES ('张百万', 20, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('赵四', 21, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('广坤', 20, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('小斌', 20, 2); 
INSERT INTO employee (ename, age, dept_id) VALUES ('艳秋', 22, 2); 
INSERT INTO employee (ename, age, dept_id) VALUES ('大玲子', 18, 2);

-- 插入一条有问题的数据 (部门id不存在) 
-- Cannot add or update a child row: a foreign key constraint fails 
INSERT INTO employee (ename, age, dept_id) VALUES ('错误', 18, 3); 
~~~

### 3.3.4 删除外键约束

#### 语法格式

~~~mysql
alter table 从表 drop foreign KEY 外键约束名称
~~~

* 删除外键

~~~mysql
ALTER TABLE employee DROP FOREIGN KEY;
~~~

### 3.3.5 外键约束的注意事项

* 从表的外键类型必须与主键的主键类型保持一致

* 添加数据，应该先添加主表中的数据

  ~~~mysql
  -- 添加一个新的部门 
  INSERT INTO department(dep_name,dep_location) VALUES('市场部','北京');
  -- 添加一个属于市场部的员工 
  INSERT INTO employee(ename,age,dept_id) VALUES('老胡',24,3);
  ~~~

* 删除数据时应该先删除从表中的数据

  ~~~mysql
  -- 删除数据时 应该先删除从表中的数据 
  -- 报错 Cannot delete or update a parent row: a foreign key constraint fails 
  -- 报错原因 不能删除主表的这条数据,因为在从表中有对这条数据的引用 
  DELETE FROM department WHERE id = 3;
  
  -- 先删除从表的关联数据 
  DELETE FROM employee WHERE dept_id = 3;
  
  -- 再删除主表的数据 
  DELETE FROM department WHERE id = 3
  ~~~

### 3.3.6 级联删除操作

* 如果想实现删除主表数据的同时,也删除掉从表数据,可以使用级联删除操作

~~~mysql
-- 级联删除
ON DELETE CASCADE
~~~

#### 示例代码

~~~mysql
-- 重新创建添加级联操作 
CREATE TABLE employee(    
    eid INT PRIMARY KEY AUTO_INCREMENT,    
    ename VARCHAR(20),    
    age INT,    
    dept_id INT,    
    CONSTRAINT emp_dept_fk FOREIGN KEY(dept_id) REFERENCES department(id)
    -- emp_dept_fk 是外键的名称
    -- 添加级联删除    
    ON DELETE CASCADE 
);
    
-- 添加数据 
INSERT INTO employee (ename, age, dept_id) VALUES ('张百万', 20, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('赵四', 21, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('广坤', 20, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('小斌', 20, 2); 
INSERT INTO employee (ename, age, dept_id) VALUES ('艳秋', 22, 2); 
INSERT INTO employee (ename, age, dept_id) VALUES ('大玲子', 18, 2); 

-- 删除部门编号为2 的记录 
DELETE FROM department WHERE id = 2;
~~~

## 3.4 多表关系设计

* 实际开发中，一个项目通常需要很多张表才能完成。例如：一个商城项目就需要分类表(category)、 商品表(products)、订单表(orders)等多张表。且这些表的数据之间存在一定的关系，接下来我们一起 学习一下多表关系设计方面的知识 

| 表与表之间的关系                                   |
| -------------------------------------------------- |
| 一对多关系：最常见的关系，学生对班级，员工对部门   |
| 多对多关系：学生与课程，用户与角色                 |
| 一对一关系：使用较少，因为一对一关系可以合成一张表 |

### 3.4.1 一对多关系

* 一对多关系
  * 例如：班级和学生，部门和员工，客户和订单，分类和商品
* 一对多键表原则
  * 在从表(多的一方)创建一个字段,字段作为外键指向主表(一的一方)的主键

#### 示例代码

~~~mysql
# 创建一个省份表和一个城市表
# 从表中创建外键指向主表的主键

-- 创建省份表
CREATE TABLE province(
	id INT PRIMARY KEY AUTO_INCREMENT,
    NAME VARCHAR(20),
    description VARCHAR(20)
);

-- 创建城市表
CREATE TABLE city(
	cid INT PRIMARY KEY AUTO_INCREMENT,
    NAME VARCHAR(20),
    description VARCHAR(20),
    
    -- 创建外键字段 
    pid INT,
    -- 添加外键约束
    FOREIGN KEY(pid) REFERENCES province(id)
);

~~~



### 3.4.3 多对多关系

* 多对多
  * 例如：老师和学生，学生和课程，用户和角色
* 多对多关系键表原则
  * 需要创建第三张表，中间表中至少两个字段，是两张表的中的主键字段，作为中间表的外键，这两个字段分别作为外键指向各自一方的主键

#### 示例代码

~~~mysql
# 设计演员角色表(多对多关系表)

-- 创建演员表
CREATE TABLE actor(
	id INT PRIMARY KEY AUTO_INCREMENT,
    aname VARCHAR(20)
);

-- 创建角色表
CREATE TABLE role(
	id INT PRIMARY KEY AUTO_INCREMENT,
    rname VARCHAR(20)
);

-- 创建中间表
CREATE TABLE actor_role(
	-- 中间表的主键
    id INT PRIMARY KEY AUTO_INCREMENT,
    
    -- aid 字段 指向actor表的主键
    aid INT,
    
    -- rid 字段 指向role表的主键
    rid INT,
    
    -- 添加外键约束
    -- aid字段添加外键约束
    ALTER TABLE actor_role ADD FOREIGN KEY(aid) REFERENCES actor(id); 
    
    -- rid 字段添加外键约束
    ALTER TABLE actor_role ADD FOREIGN KEY(rid) REFERENCES role(id);
);
~~~



### 3.4.4 一对一关系

* 一对一
  * 在实际的开发中应用不多，因为一对一可以创建成一张表
* 一对一键表原则
  * 外键唯一 主表的主键和从表的外键（唯一），形成主外键关系，外键唯一 UNIQUE 
  * 可以在任意一方建立外键值指向另一方的主键

## 3.5 多表查询

### 3.5.1 什么是多表查询

* DQL：查询多张表获取到需要的数据

### 3.5.2 案例展示

* 创建db3_2数据库

~~~mysql
-- 创建db3_2数据库 指定编码
CREATE DATABASE db3_2 CHARACTER SET utf8;
~~~

* 创建分类表与商品表

~~~mysql
# 分类表（一方 主表）
CREATE TABLE category(
	cid VARCHAR(32) PRIMARY KEY ,
    cname VARCHAR(50)
);

# 商品表
CREATE TABLE products(
	pid VARCHAR(32) PRIMARY KEY,
    pname VARCHAR(20),
    price INT,
    flag VARCHAR(2),	-- 是否上架标记：1表示上架、0表示下架
    category_id VARCHAR(32),
    
    -- 添加外键约束
    FOREIGN KEY (category_id) REFERENCES category (cid)
);
~~~

* 插入数据

~~~mysql
#分类数据 
INSERT INTO category(cid,cname) VALUES('c001','家电'); 
INSERT INTO category(cid,cname) VALUES('c002','鞋服'); 
INSERT INTO category(cid,cname) VALUES('c003','化妆品'); 
INSERT INTO category(cid,cname) VALUES('c004','汽车');

#商品数据 
#商品数据 
INSERT INTO products(pid, pname,price,flag,category_id) VALUES('p001','小米电视机',5000,'1','c001'); 
INSERT INTO products(pid, pname,price,flag,category_id) VALUES('p002','格力空调',3000,'1','c001'); 
INSERT INTO products(pid, pname,price,flag,category_id) VALUES('p003','美的冰箱',4500,'1','c001');
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p004','篮球鞋',800,'1','c002'); 
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p005','运动裤',200,'1','c002'); 
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p006','T恤',300,'1','c002'); 
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p007','冲锋衣',2000,'1','c002');
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p008','神仙水',800,'1','c003'); 
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p009','大宝',200,'1','c003');
~~~


### 3.5.3 笛卡尔积

* 交叉连接查询,因为会产生笛卡尔积,所以基本不会使用

* 语法格式

~~~mysql
SELECT 字段名 FROM  表1, 表2;
~~~

* 使用交叉连接查询 商品表与分类表

~~~mysql
SELECT * FROM category , products;
~~~

* 观察查询结果,产生了笛卡尔积 (得到的结果是无法使用的)

~~~mysql
SELECT * FROM products , category;  -- 产生的结果无法使用
~~~

## 3.6 多表查询的分类

### 3.6.1 内连接查询

* 通过指定的条件去匹配两张表中的数据, 匹配上就显示,匹配不上就不显示
* 从表的外键 = 主表的主键 方式去匹配

#### 隐式内连接

* 语法格式

~~~mysql
select 字段名...from 左表, 右表 where 连接条件
~~~

#### 示例代码

~~~mysql
-- 查询所有商品信息和对应的分类信息
-- 隐式内连接查询
SELECT * FROM products , category WHERE category_id = cid;

-- 查询商品的表的商品名称和价格，以及商品的分类信息
SELECT 
	p.panem
FROM produccts p, category c WHERE p.category_id = c.cid;

-- 多表查询中可以使用给表起别名的方式，简化查询
SELECT 
    p.`pname`,	-- 商品名称
    p.`price`,	-- 商品价格
    c.`cname`	-- 分类信息
FROM products p, category c WHERE p.`category_id` = c.`cid`;

-- 查询 格力空调是属于哪一类的商品
-- 方式一
SELECT
    p.`pname`,
    c.`cname`
FROM products p, category c WHERE p.`category_id` = c.`cid` AND p.`pname` = '格力空调';

-- 方式二
SELECT
    p.`pname`,
    c.`cname`
FROM products p, category c WHERE p.`category_id` = c.`cid` AND p.`pname` = 'p002';
~~~

#### 显式内连接

* 语法格式

~~~mysql
select 字段名...from 左表 [inner] jion 右表 on 连接条件	-- inner可以省略
~~~

#### 示例代码

~~~mysql
-- 查询所有商品信息和对应的分类信息
-- 显式内连接
SELECT * FROM products p INNER JOIN category c ON p.`category_id` = c.`cid`;

-- 查询鞋服分类下，价格大于5000的商品名称和商品价格
-- 查询之前要确定几件事情 
-- 1、查询几张表 products & category
-- 2、表的连接条件 p.`category_id` = c.`cid`;从表.外键 = 主表.主键
-- 3、查询所用到的字段 商品名称 价格
-- 4、查询的条件 分类 = 鞋服, 价格 > 500
SELECT
    p.`pname`,
    p.`price`
FROM products p INNER JOIN category c ON p.`category_id` = c.`cid`
WHERE p.`price` > 500 AND c.`cname` = '鞋服';
~~~

### 3.6.2 外连接查询

#### 左外连接

* 使用lLEFT OUTER JOIN ，OUTER可以省略
* 以左表为基准, 匹配右边表中的数据,如果匹配的上,就展示匹配到的数据 
* 如果匹配不到, 左表中的数据正常展示, 右边的展示为null. 

语法格式

~~~mysql
-- 语法格式
SELECT 字段名 FROM 左表 LEFT [OUTER] JOIN 右表 ON 条件

-- 左外连接查询
SELECT * FROM category c LEFT JOIN products p  ON c.`cid`= p.`category_id`;
~~~

#### 示例代码

~~~mysql
-- 左外连接查询
SELECT
*
FROM category c
LEFT JOIN products p ON c.`cid` = p.`category_id`;

-- 查询每个分类下的商品
查询的表
查询的条件 分组 统计
查询的字段 分类 分类下的商品个数信息
表的连接条件
SELECT
    c.`cname`,
    COUNT(p.`pid`)
FROM 
category c LEFT JOIN products p ON c.`cid` = p.`category_id`
GROUP BY c.`cname`;
~~~

#### 右外连接

* 关键字 right [outer] join
* 以右表为基准
* 语法格式 

~~~mysql
select 字段名 from 左表 right join 右表 on 条件

-- 右外连接查询
SELECT * FROM products p RIGHT JOIN category c ON p.`category_id` = c.`cid`;
~~~

### 3.6.3 各种连接方式的总结

* 内连接: inner join , 只获取两张表中 交集部分的数据. 
* 左外连接: left join ,  以左表为基准 ,查询左表的所有数据, 以及与右表有交集的部分 
* 右外连接: right join , 以右表为基准,查询右表的所有的数据,以及与左表有交集的部分

## 3.7 子查询

### 3.7.1 子查询概念

* 子查询概念 一条select 查询语句的结果, 作为另一条 select 语句的一部分 

### 3.7.2 子查询的特点

* 子查询必须放在小括号中 
* 子查询一般作为父查询的查询条件使用

### 3.7.3 子查询分类

* where型子查询：将子查询的结果作为父查询的比较条件使用

* from型子查询：将子查询的查询寻结果作为一张表使用

* exists型子查询：查询结果是单列多行的情况，可以将子查询的结果作为父查询的 in函数中的条件使用

### 3.7.4 子查询的结果作为查询条件

* 语法格式

~~~mysql
SELECT 查询字段 FROM 表 WHERE 字段=（子查询）; 
~~~

* 通过子查询的方式, 查询价格最高的商品信息

~~~mysql
# 通过子查询的方式, 查询价格最高的商品信息 
-- 1.先查询出最高价格 
SELECT MAX(price) FROM products;
-- 2.将最高价格作为条件,获取商品信息 
SELECT * FROM products WHERE price = (SELECT MAX(price) FROM products);
~~~

* 查询化妆品分类下的 商品名称 商品价格

~~~mysql
#查询化妆品分类下的 商品名称 商品价格
-- 先查出化妆品分类的 id 
SELECT cid FROM category WHERE cname = '化妆品';
-- 根据分类id ,去商品表中查询对应的商品信息 
SELECT     
	p.`pname`,    
	p.`price` 
FROM products p 
WHERE p.`category_id` = (SELECT cid FROM category WHERE cname = '化妆品');
~~~

* 查询小于平均价格的商品信息

~~~mysql
-- 1.查询平均价格 
SELECT AVG(price) FROM products; 

-- 2.查询小于平均价格的商品 
SELECT * FROM products 
WHERE price < (SELECT AVG(price) FROM products);
~~~

### 3.7.5 子查询的结果作为一张表

* 语法格式

~~~mysql
SELECT 查询字段 FROM （子查询）表别名 WHERE 条件;
~~~

* 查询商品中,价格大于500的商品信息,包括 商品名称 商品价格 商品所属分类名称

~~~mysql
-- 1. 先查询分类表的数据 
SELECT * FROM category;
-- 2.将上面的查询语句 作为一张表使用 
SELECT
	p.`pname`,    
	p.`price`,    
	c.cname 
FROM products p 
-- 子查询作为一张表使用时 要起别名 才能访问表中字段 
INNER JOIN (SELECT * FROM category) c ON p.`category_id` = c.cid WHERE p.`price` > 500;
~~~

* 当子查询作为一张表的时候，需要起别名，否则无法访问表中的字段

### 3.7.6 子查询结果是单列多行

* 子查询的结果类似一个数组, 父层查询使用 IN 函数 ,包含子查询的结果
* 语法格式

~~~mysql
SELECT 查询字段 FROM 表 WHERE 字段 IN （子查询）; 
~~~

*  查询价格小于两千的商品,来自于哪些分类(名称)

~~~mysql
# 查询价格小于两千的商品,来自于哪些分类(名称)
-- 先查询价格小于2000 的商品的,分类ID 
SELECT DISTINCT category_id FROM products WHERE price < 2000;
-- 在根据分类的id信息,查询分类名称 
-- 报错:  Subquery returns more than 1 row -- 子查询的结果 大于一行 
SELECT * FROM category WHERE cid = (SELECT DISTINCT category_id FROM products WHERE price < 2000);
~~~

* 使用in函数,  in( c002,  c003 )

~~~mysql
-- 子查询获取的是单列多行数据 
SELECT * FROM category WHERE cid IN (SELECT DISTINCT category_id FROM products WHERE price < 2000);

~~~

* 查询家电类 与 鞋服类下面的全部商品信息

~~~mysql
# 查询家电类 与 鞋服类下面的全部商品信息 
-- 先查询出家电与鞋服类的 分类ID 
SELECT cid FROM category WHERE cname IN ('家电','鞋服');

-- 根据cid 查询分类下的商品信息 
SELECT * FROM products WHERE category_id IN (SELECT cid FROM category WHERE cname IN ('家电','鞋服'));
~~~

### 3.7.7 子查询总结

* 子查询如果查出的是一个字段(单列), 那就在where后面作为条件使用
* 子查询如果查询出的是多个字段(多列), 就当做一张表使用(要起别名).

## 3.8 数据库设计三范式（空间最省）

*  三范式就是设计数据库的规则. 
* 为了建立冗余较小、结构合理的数据库，设计数据库时必须遵循一定的规则。在关系型数据 库中这种规则就称为范式。范式是符合某一种设计要求的总结。要想设计一个结构合理的关 系型数据库，必须满足一定的范式 
* 满足最低要求的范式是第一范式（1NF）。在第一范式的基础上进一步满足更多规范要求的 称为第二范式（2NF） ， 其余范式以此类推。一般说来，数据库只需满足第三范式(3NF）就 行了 

### 3.8.1 第一范式（1NF）

* 列具有原子性，设计列要做到列不可拆分
* 第一范式是最基本的范式。数据库表里面字段都是单一属性的，不可再分, 如果数据表中每个 字段都是不可再分的最小数据单元，则满足第一范式。 

### 3.8.2 第二范式（2NF）

* 在第一范式的基础上更进一步，目标是确保表中的每列都和主键相关

* 一张表只能描述一件事情

### 3.8.3 第三范式（3NF）

* 消除传递依赖
* 如果表中的信息可以能够推导出来，就不要设计一个字段单独来记录

## 3.9 数据库反三范式

* 通过增加冗余或者增加重复的数据，来提高数据库的读性能
* 浪费存储空间，节省查询空间（以空间换时间）
* 冗余字段：某一个字段属于一张表，但它同时出现在另一个或多个表，且完全等同于它在其本 来所属表的意义表示，那么这个字段就是一个冗余字段 



### 3.9.1 三范式总结 
* 创建一个关系型数据库设计，我们有两种选择 
  * 尽量遵循范式理论的规约，尽可能少的冗余字段，让数据库设计看起来精致、优雅、让人心 醉。 
  * 合理的加入冗余字段这个润滑剂，减少join，让数据库执行性能更高更快。

