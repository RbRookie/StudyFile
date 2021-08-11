#### 1、初识MySQL

数据库：存数据

> 为什么学习数据库

1、岗位技能需求

2、现在的世界,得数据者得天下

3、存储数据的方法

4、程序,网站中,大量数据如何长久保存?

5、**数据库是几乎软件体系中最核心的一个存在。**



> 什么是数据库

数据库 ( **DataBase** , 简称**DB** )

**概念** : 长期存放在计算机内,有组织,可共享的大量数据的集合,是一个数据 "仓库"

**作用** : 保存,并能安全管理数据(如:增删改查等),减少冗余...

**数据库总览 :**

- 关系型数据库 ( SQL )

- - MySQL , Oracle , SQL Server , SQLite , DB2 , ...
  - 关系型数据库通过外键关联来建立表与表之间的关系

- 非关系型数据库 ( NOSQL )

- - Redis , MongoDB , ...
  - 非关系型数据库通常指数据以对象的形式存储在数据库中，而对象之间的关系通过每个对象自身的属性来决定



> 什么是DBMS

数据库管理系统 ( **D**ata**B**ase **M**anagement **S**ystem )

数据库管理软件 , 科学组织和存储数据 , 高效地获取和维护数据

![image](https://cdn.jsdelivr.net/gh/RbRookie/image-management@master/20210423/image.3mf9sl6lkbm0.png)

> 连接数据库

打开MySQL命令窗口

- 在DOS命令行窗口进入 **安装目录\mysql\bin**
- 可设置环境变量，设置了环境变量，可以在任意目录打开！

**连接数据库语句 :** mysql -h 服务器主机地址 -u 用户名 -p 用户密码

注意 : -p后面不能加空格,否则会被当做密码的内容,导致登录失败 !

**几个基本的数据库操作命令 :**

```sql
update user set password=password('123456')where user='root';-- 修改密码
flush privileges; --刷新数据库

---------------------
show databases; --显示所有数据库
use dbname；打开某个数据库
show tables; 显示数据库mysql中所有的表
describe user; 显示表mysql数据库中user表的列信息
create database name; 创建数据库
use databasename -- 选择数据库

exit; 退出Mysql
? 命令关键词 : 寻求帮助
-- 表示注释
```

#### 2 DDL定义数据

操作数据库，操作数据库中的表，操作数据库中的数据

##### 2.1 操作数据库

- 关键字需要背

```sql
- 创建数据库
CREATE DATABASE IF NOT EXISTS school1;
-- 删除数据库
DROP DATABASE IF EXISTS school1;
-- ` 是tab键上面的特殊字符，当出现关键字和名字相同的时候使用
USE `school`;
```

##### 2.2 数据库的列类型

**数据类型：**

tinyint:十分小的数据，1个字节
smallint：教小的数据，2个字节
mediumint:中等大小的数据，3个字节
int：标准的使用，4个字节
bigint：大数据，8个字节
float:浮点数，4个字节
double：双精度的浮点数。8个字节
deciaml：字符串形式的浮点数，金融计算的时候，一般使用deciaml

**常用字符串:**

char:固定字符串大小，0-255

varchar:可变字符串，2^16 - 1，**常用的变量   String**

text:文本串，2^16 - 1

**时间类型**：

java.util.Data

date：YYYY-MM-DD
time:HH:mm:ss
datetime:YYYY-MM-DD HH:mm:ss 最常用的时间格式
timestamp:时间戳，1970.1.1至今的毫秒数
null：

==代表没有值，注意是不要使用null进行计算，因为结果为null==

##### 2.3数据库的字段属性（重点）

Unsigned

无符号整数，声明了该列不能声明为负数
Zerofill:

0填充，不足的位数，使用0来填充

自增:自动在上一条记录的基础上+1
通常用来设计唯一的主键，可以在“高级”中更改每次自增的位数

非空：如果不给他赋值就会报错
如果不写值，就是null值

默认：如果字段是sex，“默认”为男，就是不写就是男

**拓展：每一个表都必须有的字段：**（阿里巴巴规范手册中规定）

```sql
id ：主键  --primary key

version:乐观锁

is_delete：伪删除

gmt_create：创建时间

gmt_update:修改时间
```

##### 2.4创建数据库表（重点）

```sql
--

CREATE TABLE IF NOT EXISTS `student`(
	`id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号id',
	`name` VARCHAR(30) NOT NULL DEFAULT'匿名' COMMENT '姓名',
	`pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex` VARCHAR(2) NOT NULL DEFAULT'女' COMMENT'性别',
	`birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
	`address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (`id`)
)ENGINE = INNODB DEFAULT CHARSET =utf8
-- 注意PRIMARY KEY (`id`)后需要跟()

```

**格式**

```sql
CREATE TABLE [IF NOT EXISTS] `表名`（
	`字段名` 列类型 [属性] [索引] [注释],
	.....
	`字段名` 列类型 [属性] [索引] [注释] --最后一个不加
）[表类型][字符集设置][注释]
```

##### 2.5数据表的类型

```sql
-- 关于引擎数据
/*
INNODB 默认使用
MYISAM 早期使用
*/
```

|            | MYISAM |       INNODB        |
| :--------: | :----: | :-----------------: |
|  事务支持  | 不支持 |        支持         |
| 数据行锁定 | 不支持 |        支持         |
|  外键约束  | 不支持 |        支持         |
|  全文索引  |  支持  |       不支持        |
| 表空间大小 |  较小  | 较大，约为MYISAM2倍 |

常规使用操纵

- MYISAM  节约空间，速度较快
- INNODB  安全性高，事务的处理，多表多用户操作



>在物理空间存放的位置

- 所有的文件都在data目录下
- INNODB：在数据库中，只有一个*.frm文件，以及上级目录下的ibata1文件
- MYISAM：
  - *.frm:表结构的定义文件
  - *.MYD:数据文件（data）
  - *.MYI:索引文件（index）

##### 2.6 修改和删除表

```sql
-- 修改表的名字
ALTER TABLE `student1`  RENAME AS `student`;
-- 增加表的字段
ALTER TABLE `student` ADD age1 INT(11);
-- modify修改约束
ALTER TABLE `student` MODIFY `name` VARCHAR(20);
-- change重命名：[旧名] [新名] 
ALTER TABLE `student` CHANGE `age1` `age` INT(1);
-- 删除表的字段
ALTER TABLE `student` DROP age;
-- 删除表
DROP TABLE IF EXISTS `student`;

```

==所有的创建和删除操作尽量都加上判断，防止报错==



#### 3、MySQL数据管理

##### 3.1、外键（了解即可）

- 以下是物理外键（数据库级别的外键），使用麻烦，不推荐使用，了解即可。通常使用逻辑外键。

  ```sql
  -- constraint 外键名 foreign key (本表列名) reference 其他表名(其他标列名)
  ALTER TABLE `student`
  ADD CONSTRAINT `FK_gradeId` FOREIGN KEY(`gradeId`) REFERENCES `grade`(`gradeId`);
  ```

  ==最佳实践==

  - 数据库就是单纯的表，只有行和列；
  - 想建立多张表的数据，使用逻辑外键（程序去实现）

##### 3.2、DML语言（全部记住）

- Insert
- update
- delete

##### 3.3insert插入数据

```sql
insert into 表名([字段名1，字段2，字段3])values('值1','值2','值3'),
											('值1','值2','值3'),
											('值1','值2','值3')  --插入多行
---------------------------------------------------
--一般插入语句一定要保证数据和字段一一对应！
--例子
-- 完成插入一个数据
INSERT INTO `grade`(gradeId,gradeName) VALUES(1,'大一'); 
-- 不写列名就会一一对应，不对应就会报错
INSERT INTO `grade` VALUES(2,'大二'); 
-- 插入多个数据:(),()
INSERT INTO `grade`(`gradeName`) VALUES('大三'),('大四'); 


```

字段可以省略，但是后面的值必须要一一对应

##### 3.4修改

```sql
--update 修改谁(条件) set原来的值 = 新值 where 指定那一列
----------------------
UPDATE `stu1` SET  `name`='haha' WHERE id=1;

--不指定那张表，会改动所有表
UPDATE `stu1` SET  `name`='haha'
---语法
--update 表名 set colnum_name = value ,[colnum_name = value,....] where [条件]

```



|      操作符      |         含义          |
| :--------------: | :-------------------: |
|        =         |         等于          |
|      <>或!=      |        不等于         |
| between      and | 【a，b】 在某个范围内 |
|       and        |         && 与         |
|        or        |        \|\| 或        |

##### 3.5删除

>delete命令 

```sql
语法 delete from 表名 [where 条件]
------
--这样会删除整个表格
delete from `student`

--删除指定数据
delete from `student` where id=1;

```

> trucate命令

作用：完全删除一个数据库表格，表结构索引不会变

```sql
trucate table [b]
```

delete和truncate区别：

- delete删除时候
  - InnoDB:自增列会从1开始（存到内存中）
  - truncate:自增继续从上一个开始(存到文件中)
- truncate:重要，自增和计数器会归零

```SQL
-- 删除单个数据列
DELETE FROM `student` WHERE `name` = '李四';
-- delete from 清空整个表,不会影响自增
DELETE FROM `test`;
-- truncate 清空整个表，自增，计数器会归零，不会影响事务
TRUNCATE `test`;
```

#### 4、DQL查询数据(重点)

##### 4.1数据查询语言

数据库中最核心，最重要的语句

**select完整的语法：顺序不能出错**

![image](https://cdn.jsdelivr.net/gh/RbRookie/image-management@master/20210423/image.75wkwrfr6340.png)

##### 4.2 指定查询字段

```sql
-- 查询整张表的数据
SELECT * FROM student;
-- 查询指定数据,使用别名，也可以给表使用别名
SELECT `name` AS '学生姓名' FROM student;
-- 函数 concat(a,b)
SELECT CONCAT('学生姓名：',`name`) AS '拼接的新名字' FROM `student`;

```

> 去重，distinct

```sql
--去重distinct可以去掉这一列中重复的数据（重复的数据只显示一次）
select distinct `gradeid` from `student`;
-- 查询版本号
SELECT VERSION();
-- 用于计算
SELECT 100*3;
-- select可以用来自增某些数据
SELECT `grade`+10 AS '成绩'  FROM `student`;

```

##### 4.3 where后逻辑表达式

and &&; or ||; not  !=

##### 4.4 模糊查询

| **运算符**  |              *语法*               |
| :---------: | :-------------------------------: |
|   is null   |             a is null             |
| is not null |           a is not null           |
|   between   |         a between c and d         |
|  **like**   |         **a like b:a像b**         |
|   **in**    | **a in (a1,a2,a3)：a=a1或a2或a3** |

**重点是like结合%和_:**

- **% : 表示0到任意一个字符**
- **_：表示一个字符**  

```sql
--查询学生名字里面王字开头的后面是一个字的，如王福
SELECT `name` FROM `students` WHERE `name` LIKE '王_';
--查询学生名字里面王字开头的后面是两个字的，如王富贵
SELECT `name` FROM `students` WHERE `name` LIKE '王__';
--查询学生名字里面有亲的
SELECT `name` FROM `students` WHERE `name` LIKE '%亲%';

```

> in 的使用

```sql
SELECT `studentID` FROM `students`
WHERE `studentID` IN (1,2,5);
```

##### 4.5联表查询

<img src="https://img-blog.csdnimg.cn/2019061718553496.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0Fzc2Fzc2luaGFuYw==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:67%;" />

|   inner join   |        表中至少有一个匹配，就会返回        |
| :------------: | :----------------------------------------: |
| **left join**  | **会返回左表中所有的值，即使右表没有匹配** |
| **right join** | **会返回右表中所有的值，即使左表没有匹配** |

```sql
-- 左查询，返回左边查询的所有值，即使右表没有匹配
SELECT `film_name`,`biography` 
FROM `mtime_film_info_t` AS it 
LEFT JOIN `mtime_film_t` AS ft
ON ft.`UUID`=it.`film_id`;
-- 右查询，返回右边查询的所有值，即使左表没有匹配
SELECT `film_name`,`biography` 
FROM `mtime_film_info_t` AS it 
RIGHT JOIN `mtime_film_t` AS ft
ON ft.`UUID`=it.`film_id`;
-- from a left join b
-- from a right join b
-- 永远都是：a是左表，b是右边
```

> 自连接

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200909144755749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDczMzQ4,size_16,color_FFFFFF,t_70#pic_center)

==自己的表和自己的表连接==

##### 4.5分页和排序

```sql
============================分页 limit 和排序order by=================


-- 排序：  升序ASC  降序  DESC
SELECT  xx
FROM xx
JOIN xx
WHERE  xx
ORDER BY  xx
ASC   ||  DESC
```

> 分页 limit

```sql
--limit 起始的位置，页面的大小
limit 0, 5
--网页应用： 当前页 ，页面大小
```

> 排序 order by

```sql
Order by  [目标]  ACS 升序
Order by  [目标]  DESC 降序
```

##### 4.7 分组

```sql
-- 查询不同课程的平均分，最高分，最低分，平均分大于80
-- 核心：（根据不同的课程分组）

SELECT `SubjectName`,AVG(StudentResult),MAX(StudentResult)
FROM result r
INNER JOIN `Subject` sub
ON r.SubjectNo=sub.SubjectNo

GROUP BY r.SubjectNo -- 通过什么字段来分组
HAVING AVG(StudentResult)>80
```



#####   4.6子查询

where (这个值是计算出来的)

本质：`在where语句中嵌套一个子查询语句`

```sql
-- ===========================where=========================

-- 1.查询 数据库结构-1的所有考试结构（学号，科目编号，成绩） 降序
-- 方式一： 连接查询
SELECT `StudentNo`,r.`SubjectNo`,`StudentResult`
FROM `result` r
INNER JOIN `subject` sub
ON r.SubjectNo = sun.SubjectNo
WHERE subjectName = '数据库结构-1'
ORDER BY StudentResult DESC

-- 方式二：使用子查询(由里及外)
SELECT `StudentNo`,r.`SubjectName`,`StudentResult`
FROM `result`
WHERE StudentNo=(
	SELECT SubjectNo FROM  `subject` 
    WHERE SubjectName = '数据库结构-1'
)
ORDER BY StudentResult DESC


-- 分数不少于80分的学生的学号和姓名
SELECT DISTINCT s.`StudentNo`,`StudentName`
FROM student s
INNER JOIN result r
ON r.StudentNo = s.StudentNo
WHERE StudentResult>=80

-- 在这个基础上 增加一个科目 ，高等数学-2
SELECT DISTINCT s.`StudentNo`,`StudentName`
FROM student s
INNER JOIN result r
ON r.StudentNo = s.StudentNo
WHERE StudentResult>=80 AND `SubjectNo`=(
    SELECT Subject FROM `subject`
    WHERE SubjectName='高等数学-2'
)

-- 查询课程为 高等数学-2 且分数不小于80分的同学的学号和姓名
SELECT s.`StudentNo`,`StudentName`
FROM student s
INNER JOIN result r
ON s.StudentNo = r.StudentNo
INNER JOIN `subject` sub
ON r.`SubjectName`='高等数学-2'
WHERE `SubjectaName`='高等数学-2' AND StudentResult >=80


-- 再改造 (由里即外)
SELECT `StudentNo`,`StudentName` FROM student
WHERE StudentNo IN(
SELECT StudentNo result WHERE StudentResult >80 AND SubjectNo =(
SELECT SubjectNo FROM `subject` WHERE `SubjectaName`='高等数学-2'
)
)


```

#### 5.Mysql函数

##### 5.1常用函数

```sql
-- 数学运算

SELECT ABS(-8) -- 绝对值
SELECT CEILING(9.4) -- 向上取整
SELECT FLOOR(9.4)  -- 向下取整
SELECT RAND() -- 返回0-1随机数
SELECT SIGN(-10) -- 判断一个数的符号 0-0 负数返回-1 正数返回1

-- 字符串函数
SELECT CHAR_LENGTH('2323232') -- 返回字符串长度
SELECT CONCAT('我','233') -- 拼接字符串
SELECT INSERT('java',1,2,'cccc') -- 从某个位置开始替换某个长度
SELECT UPPER('abc') 
SELECT LOWER('ABC')
SELECT REPLACE('坚持就能成功','坚持','努力')

-- 查询姓 周 的同学 ，改成邹
SELECT REPLACE(studentname,'周','邹') FROM student
WHERE studentname LIKE '周%'

-- 时间跟日期函数（记住）
SELECT CURRENT_DATE() -- 获取当前日期
SELECT CURDATE() -- 获取当前日期
SELECT NOW() -- 获取当前日期
SELECT LOCATIME()  -- 本地时间
SELECT SYSDATE()  -- 系统时间

SELECT YEAR(NOW())
SELECT MONTH(NOW())
SELECT DAY(NOW())
SELECT HOUR(NOW())
SELECT MINUTE(NOW())
SELECT SECOND(NOW())

-- 系统
SELECT SYSTEM_USER()
SELECT USER()
SELECT VERSION()

```

##### 5.2聚合函数

| 函数名称 | 描述   |
| -------- | ------ |
| COUNT()  | 计数   |
| SUM()    | 求和   |
| AVG()    | 平均值 |
| MAX()    | 最大值 |
| MIN()    | 最小值 |

##### 5.3 数据库级别MD5加密（拓展）

什么是MD5

主要增强算法复杂度不可逆性。

MD5不可逆，具体的MD5是一样的

MD5破解原理，背后有一个字典，MD5加密后的值，加密前的值

```sql
CREATE TABLE `testmd5`(
`id` INT(4) NOT NULL,
`name` VARCHAR(20) NOT NULL,
`pwd` VARCHAR(50) NOT NULL,
PRIMARY KEY (`id`)

)ENGINE=INNODB DEFAULT CHARSET=UTF8


-- 明文密码
INSERT INTO testmd5 VALUES(1,'张三','123456'),(2,'李四','123456'),(3,'王五','123456')

-- 加密
UPDATE testmd5 SET pwd=MD5(pwd) WHERE id =1
UPDATE testmd5 SET pwd=MD5(pwd) WHERE id !=1  -- 加密全部

-- 插入时加密

INSERT INTO testmd5 VALUES(4,'小明',MD5('123456'))
INSERT INTO testmd5 VALUES(5,'红',MD5('123456'))

-- 如何校验，将用户传递过来的密码，进行MD5加密，然后对比加密后的值
SELECT * FROM testmd5 WHERE `name`='红' AND pwd=MD5('123456')

```

#### 6、事务

##### 6.1 什么是事务

得执行多个操作的时候需要事务，组合起来多个操作，如转账，A→B，A中的表和B中的表应该同时变化。构成一个事务。

==**将一组SQL放在一个批次中执行**==

> 事务原则 ： ACID原则 原子性，一致性，隔离性，持久性 （脏读，幻读…）

**原子性**（Atomicity）

要么都成功，要么都失败

**一致性（Consistency）**

事务前后的数据完整性要保持一致

**持久性（Durability）**–事务提交

事务一旦提交就不可逆转，被持久化到数据库中

**隔离性**

事务产生多并发时，互不干扰

> 隔离产生的问题

**脏读：**

指一个事务读取了另外一个事务未提交的数据。

**不可重复读：**

在一个事务内读取表中的某一行数据，多次读取结果不同。（这个不一定是错误，只是某些场合不对）

**虚读(幻读)**

是指在一个事务内读取到了别的事务插入的数据，导致前后读取不一致。
（一般是行影响，多了一行）

> 执行事务

```sql
-- mysql 自动开启事务提交
SET autocommit=0 -- 关闭
SET autocommit=1 -- 开启（默认的）

-- 手动处理事务
SET autocommit =0 -- 关闭自动提交

-- 事务开启

START TRANSACTION -- 标记一个事务的开始，从这个之后的SQP都在同一个事务内

INSERT XX
INSERT XX

-- 提交 ： 持久化(成功)
COMMIT 
-- 回滚：  回到原来的样子（失败）
ROLLBACK
-- 事务结束
SET autocommit = 1 -- 开启自动提交
-- 了解
SAVEPOINT 保存点名称 -- 设置一个事务的保存点
ROLLBACK TO SAVEPOINT 保存点名 -- 回滚到保存点
RELEASE SAVEPOINT 保存点 -- 删除保存点
```

> 例子

```sql
CREATE DATABASE shop CHARACTER SET utf8 COLLATE utf8_general_ci
USE shop
CREATE TABLE `account`(
`id` INT(3) NOT NULL AUTO_INCREMENT,
`name` VARCHAR(30) NOT NULL,
`money` DECIMAL(9,2) NOT NULL,
PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO account(`name`,`money`)
VALUES('A',2000),('B',10000)

-- 模拟转账：事务
SET autocommit = 0; -- 关闭自动提交
START TRANSACTION -- 开启事务（一组事务）
UPDATE account SET money = money-500 WHERE `name` = 'A' -- A 转账给B
UPDATE account SET money = money+500 WHERE `name` = 'B' -- B 收到钱

COMMIT ; -- 提交事务
ROLLBACK ; -- 回滚

SET autocommit=1 -- 恢复默认值
```

#### 7、索引

> MySQL索引的建立对于MySQL的高效运行是很重要的，索引可以大大提高MySQL的检索速度。

##### 7.1索引的分类

> 在一个表中，主键索引只能有一个，唯一索引可以有多个(唯一索引不唯一)

- 主键索引 （PRIMARY KEY）
  - 唯一的标识，主键不可重复，只能有一个列作为主键
- 唯一索引 （UNIQUE KEY）
  - 避免重复的列出现，唯一索引可以重复，多个列都可以标识唯一索引
- 常规索引（KEY/INDEX）
  - 默认的，index,key关键字来设置
- 全文索引（FULLTEXT）
  - 在特点的数据库引擎下才有，MyISAM
  - 快速定位数据

```sql
-- 索引的使用
-- 1.在创建表的时候给字段增加索引
-- 2.创建完毕后，增加索引

-- 显示所有的索引信息
SHOW INDEX FROM 表

-- 增加一个索引
ALTER TABLE 表 ADD FULLTEXT INDEX 索引名（字段名）

-- EXPLAIN 分析sql执行状况
EXPLAIN SELECT * FROM student -- 非全文索引

```

##### 7.2 测试索引

```sql
CREATE TABLE `app_user` (
`id` BIGINT(20) UNSIGNED NOT NULL AUTO_INCREMENT,
`name` VARCHAR(50) DEFAULT '',
`email` VARCHAR(50) NOT NULL,
`phone` VARCHAR(20) DEFAULT '',
`gender` TINYINT(4) UNSIGNED DEFAULT '0',
`password` VARCHAR(100) NOT NULL DEFAULT '',
`age` TINYINT(4) DEFAULT NULL,
`create_time` DATETIME DEFAULT CURRENT_TIMESTAMP,
`update_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

-- 插入100万数据
DELIMITER $$ --  写函数之前必写
CREATE FUNCTION mock_data()
RETURNS INT 
BEGIN
DECLARE num INT DEFAULT 1000000;
DECLARE i INT DEFAULT 0;

WHILE i<num DO
-- 插入语句
INSERT INTO app_user(`name`,`email`,`phone`,`gender`,`password`,`age`)
VALUE(CONCAT('用户',i),'534240118@qq.com',FLOOR (CONCAT('18',RAND()*9999999)),FLOOR (RAND()*2),
UUID(),FLOOR (RAND()*100));

SET i = i+1;
END WHILE;
RETURN i;


END;

INSERT INTO app_user(`name`,`email`,`phone`,`gender`,`password`,`age`)
VALUE(CONCAT('用户',i),'534240118@qq.com',FLOOR (CONCAT('18',RAND()*9999999)),FLOOR (RAND()*2),
UUID(),FLOOR (RAND()*100))


SELECT mock_data();

SELECT * FROM app_user WHERE `name`='用户9999' -- 接近半秒

EXPLAIN SELECT * FROM app_user WHERE `name`='用户9999'  -- 查询99999条记录

-- id _ 表名_字段名
-- create index on 字段
CREATE INDEX id_app_user_name ON app_user(`name`); -- 0.001 s
EXPLAIN SELECT * FROM app_user WHERE `name`='用户9999'  -- 查询一条记录

```

##### 7.3 索引原则

- 索引不是越多越好
- 不要对经常变动的数据加索引
- 小数据量的表不需要加索引
- 索引一般加在常用来查询的字段上

> 索引的数据结构

Hash 类型的索引

Btree: 默认innodb 的数据结构

阅读： http://blog.codinglabs.org/articles/theory-of-mysql-index.html

####  8、备份

![img](https://img-blog.csdnimg.cn/20200708013426360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzOTU2NTM2,size_16,color_FFFFFF,t_70)

#### 9、规范数据库设计(三大范式)

为什么需要数据规范化？

- 信息重复
- 更新异常
- 插入异常
- 删除异常
  - 无法正常显示异常
- 删除异常
  - 丢失有效的信息

>三大范式(了解)
>
>

**第一范式（1NF）：**

原子性：保证每一列不可再分

**第二范式（2NF）：**

前提：满足第一范式

每张表只描述一件事情（只修饰一个主键）

**第三范式（3NF）：**

满足第一第二范式的前提下；

第三范式需要确保数据表中的每一列数据和主键直接相关

而不是间接相关；（规范数据库的设计）

**规范性和性能的问题**

关联查询的表，不得超过三张表

- 考虑商业化的需求和目标（成本和用户体验） 数据库的性能更加重要
- 再规范性能的问题的时候，需要适当的考虑一下，规范性
- 故意给某些表加一些冗余的字段（从多表，变成单表）
- 故意增加一些计算列（从大数据量降低为小数据量的查询：索引）

#### 10、JDBC（重点）

SUN 公司为了简化开发人员的（对数据库的统一）操作，提供了一个(Java操作数据库的)规范，JDBC

这些规范的实现由具体的厂商去做

对于开发人员来说，我们只需要掌握JDBC的接口操作即可

![image](https://cdn.jsdelivr.net/gh/RbRookie/image-management@master/20210423/image.4lpy79dg66c0.png)

java.sql

javax.sql

还需要导入数据库驱动包

##### 10.1第一个jdbc的程序

```sql
CREATE DATABASE jdbcStudy CHARACTER SET utf8 COLLATE utf8_general_ci;

USE jdbcStudy;

CREATE TABLE `users`(
id INT PRIMARY KEY,
NAME VARCHAR(40),
PASSWORD VARCHAR(40),
email VARCHAR(60),
birthday DATE
);

INSERT INTO `users`(id,NAME,PASSWORD,email,birthday)
VALUES(1,'zhansan','123456','zs@sina.com','1980-12-04'),
(2,'lisi','123456','lisi@sina.com','1981-12-04'),
(3,'wangwu','123456','wangwu@sina.com','1979-12-04')
```

1.创建一个普通项目

2.导入数据库驱动

<img src="https://cdn.jsdelivr.net/gh/RbRookie/image-management@master/20210423/image.7ak2g9ztb1c0.png" alt="image" style="zoom: 67%;" />

3.编写测试代码

```java
package com.hui.demo;

import com.mysql.jdbc.Driver;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

/**
 * @ClassName: JdbcFirst
 * @Author: Rokkie丶zimiao
 * @Date: 2021/8/6 11:25
 * @Description:  第一个jdbc程序
 */
public class JdbcFirst {
    public static void main(String[] args) throws Exception {
    // 1、加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver"); //固定写法
    // 2、用户信息和url useUnicode=true&characterEnding=utf8
        String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEnding=utf8&serverTimezone=GMT";
        String name = "root";
        String password = "";
    // 3、连接成功，数据库对象
        Connection connection = DriverManager.getConnection(url, name, password);

    // 4、执行sql的对象
        Statement statement = connection.createStatement();
        // 5、执行sql的对象去执行连接；
        String sql = "SELECT *FROM users";
        ResultSet resultSet = statement.executeQuery(sql);
        while (resultSet.next()){
            System.out.println("id+"+resultSet.getObject("id"));
            System.out.println("name+"+resultSet.getObject("NAME"));
            System.out.println("password+"+resultSet.getObject("PASSWORD"));
            System.out.println("email+"+resultSet.getObject("email"));
            System.out.println("birthday+"+resultSet.getObject("birthday"));
        }
        // 6、释放资源；
        resultSet.close();
        statement.close();
        connection.close();
    }
    }

```

**注意：**连接数据库时候因为数据库没有定义时区所以给数据库得定义时区：$serverTimezone=GMT$。

**步骤总结：**
1.加载驱动

2.连接数据库 DriverManager

3.获取执行SQL的对象 Statement

4.获得返回的结果集

5.释放连接

> DriverManager

```java
//DriverManager.registerDriver(new com.mysql.jdbc.Driver());
Class.forName("com.mysql.jdbc.cj.Driver");//固定写法
Connection connection= DriverManager.getConnection(url,name,password);

//connection代表数据库
//数据库设置自动提交
//事务提交
//事务回滚
connection.rollback();
connection.commit();
connection.setAutoCommit();

```

> URL

```java
String url ="jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&&useSSL=false";

//mysql 默认3306
//协议://主机地址:端口号/数据库名？参数1&参数2&参数3

//Oracle   1521
//jdbc:oralce:thin:@localhost:1521:sid

```

> statement 执行SQL的对象PrepareStatement 执行SQL的对象

```java
String sql="SELECT * FROM users";//编写Sql

statement.executeQuery();
statement.execute();
statement.executeUpdate();//更新，插入，删除，返回一个受影响的行数

```

> Result 结果集，封装了你之前执行sql语句的所有的结果

```java 
ResultSet resultSet = statement.executeQuery(sql);//返回的结果集,结果集中封装了我们全部查询的结果
        resultSet.getObject();//在不知道列类型下使用
        resultSet.getString();//如果知道则指定使用
        resultSet.getInt();
		// 遍历指针
        resultSet.next(); //移动到下一个
        resultSet.afterLast();//移动到最后
        resultSet.beforeFirst();//移动到最前面
        resultSet.previous();//移动到前一行
        resultSet.absolute(row);//移动到指定行
```

> 释放内存

```java
//6. 释放连接
        resultSet.close();
        statement.close();
        connection.close();//耗资源
```

##### 10.2 statement对象

**Jdbc中的statement对象用于向数据库发送SQL语句，想完成对数据库的增删改查，只需要通过这个对象向数据库发送增删改查语句即可。**

Statement对象的executeUpdate方法，用于向数据库发送增、删、改的sq|语句， executeUpdate执行完后， 将会返回一个整数(即增删改语句导致了数据库几行数据发生了变化)。

Statement.executeQuery方法用于向数据库发生查询语句，executeQuery方法返回代表查询结果的ResultSet对象。

> CRUD操作-create

使用executeUpdate(String sql)方法完成数据添加操作，示例操作：

```java
 Statement statement = connection.createStatement();
        String sql = "insert into user(...) values(...)";
        int num = statement.executeUpdate(sql);
        if(num>0){
            System.out.println("插入成功");
        }

```

> CRUD操作-delete

使用executeUpdate(String sql)方法完成数据删除操作，示例操作：

```java
Statement statement = connection.createStatement();
        String sql = "delete from user where id =1";
        int num = statement.executeUpdate(sql);
        if(num>0){
            System.out.println("删除成功");
        }

```

> CURD操作-update

使用executeUpdate(String sql)方法完成数据修改操作，示例操作：

```java
Statement statement = connection.createStatement();
        String sql = "update user set name ='' where name = ''";
        int num = statement.executeUpdate(sql);
        if(num>0){
            System.out.println("修改成功");
        }
```

> CURD操作-read

使用executeUpdate(String sql)方法完成数据查询操作，示例操作：

```java
Statement statement = connection.createStatement();
        String sql = "select * from  user where id =1";
        ResultSet rs= statement.executeQuery(sql);
        if(rs.next()){
            System.out.println("");
        }
```

> 将操作打包成工具类，方便代码灵活编写

1.提取工具类

```java 
package com.kuang.lesson02.utils;

import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class JdbcUtils {
    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;
    static {
        try{
            InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
            Properties properties = new Properties();
            properties.load(in);
            driver=properties.getProperty("driver");
            url=properties.getProperty("url");
            username=properties.getProperty("username");
            password=properties.getProperty("password");

            //1.驱动只用加载一次
            Class.forName(driver);

        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }


    //2.获取连接
    public static Connection getConnection() throws Exception{
        return DriverManager.getConnection(url, username, password);
    }
    //3.释放资源
    public static void release(Connection conn, Statement st, ResultSet rs) throws SQLException {

        if(rs!=null){
            rs.close();
        }
        if (st!=null){
            st.close();
        }
        if(conn!=null){
            conn.close();
        }

    }
}
```

##### 10.3 PreparedStatement对象

可以防止sql注入，效率更高，更加安全

```java
package com.kuang.lesson03;

import com.kuang.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class Test {
    public static void main(String[] args) {
        Connection connection= null;
        PreparedStatement pstm=null;
        try {

            connection = JdbcUtils.getConnection();
            //区别
            //使用问好占位符代替参数
            String sql = "insert into users(id,`NAME`) values(?,?)";
            pstm = connection.prepareStatement(sql);//预编译sql，先写sql然后不执行
            //手动赋值
            pstm.setInt(1,8);
            pstm.setString(2,"SANJIN");

            //执行
            int i = pstm.executeUpdate();
            if (i>0){
                System.out.println("插入成功");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.release(connection,pstm,null);
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```

 防止sql注入本质，就是传递字符带有""，转义字符会被转义。

##### 10.4JDBC事务代码实现

1.开启事务`conn.setAutoCommit(false)`;

2、一组业务执行完毕。提交事务

3、默认回滚，但是可以在catch语句中显示定义回滚。

```java
public class Action {
    public static void main(String[] args) {

        Connection conn =null;
        PreparedStatement ps = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();
            //关闭数据库的自动提交功能， 开启事务
            conn.setAutoCommit(false);
            //自动开启事务
            String sql = "update account set money = money-500 where id = 1";
            ps =conn.prepareStatement(sql);
            ps.executeUpdate();
            String sql2 = "update account set money = money-500 where id = 2";
            ps=conn.prepareStatement(sql2);
            ps.executeUpdate();

            //业务完毕，提交事务
            conn.commit();
            System.out.println("操作成功");
        } catch (Exception e) {
            try {
                //如果失败，则默认回滚
                conn.rollback();//如果失败，回滚
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.release(conn,ps,rs);
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}

```

### 10.8数据库连接池

数据库连接–执行完毕–释放

连接–释放 十分浪费资源

**池化技术**： 准备一些预先的资源，过来就连接预先准备好的

常用连接数 100

最少连接数：100

最大连接数 ： 120 业务最高承载上限

排队等待，

等待超时：100ms

编写连接池，实现一个接口 DateSource

> 开源数据源实现(拿来即用的)

DBCP

C3P0

Druid：阿里巴巴

使用了这些数据库连接池之后，我们在项目开发中就不需要编写连接数据库的代码了



#### 附录 

  mysql完结
