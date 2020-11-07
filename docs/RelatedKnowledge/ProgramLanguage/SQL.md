---
typora-root-url: ..\picture
---

# SQL

SQL（Structured Query Language），结构化查询语言，用于访问和处理数据库的标准的计算机语言。

除了 SQL 标准之外，大部分 SQL 数据库程序都拥有它们自己的专有扩展！

## 一、基本语法

1、SQL对大小写不敏感；

2、某些数据库要求每条语句使用分号间隔；

3、一些重要的SQL命令：

| 命令                | 描述                 |
| ------------------- | -------------------- |
| **SELECT**          | 从数据库中提取数据   |
| **UPDATE**          | 更新数据库中的数据   |
| **DELETE**          | 从数据库中删除数据   |
| **INSERT INTO**     | 向数据库中插入新数据 |
| **CREATE DATABASE** | 创建新数据库         |
| **ALTER DATABASE**  | 修改数据库           |
| **CREATE TABLE**    | 创建新表             |
| **ALTER TABLE**     | 变更（改变）数据库表 |
| **DROP TABLE**      | 删除表               |
| **CREATE INDEX**    | 创建索引（搜索键）   |
| **DROP INDEX**      | 删除索引             |

4、注释，单行一般使用`--`，多行使用`/**/`，MySQL还可使用`#`进行单行注释。

## 二、语句详解

### 1、SELECT

（1）SELECT 语句用于从数据库中选取数据，结果被存储在一个结果表中，称为结果集；

（2）语法如下：

```sql
SELECT column_name,column_name FROM table_name;
SELECT * FROM table_name;
```

（3）SELECT DISTINCT 语句用于返回唯一不同的值：

```sql
SELECT DISTINCT column_name,column_name FROM table_name;
```

（4）SELECT TOP 子句用于规定要返回的记录的数目：

```sql
-- SQL Server / MS Access 语法
SELECT TOP number|percent column_name(s)
FROM table_name;
-- 例如，SELECT TOP 50 PERCENT * FROM Websites;
-- 返回最后5行，select top 5 * from table order by id desc
-- MySQL 语法
SELECT column_name(s)
FROM table_name
LIMIT number;
-- Oracle 语法
SELECT column_name(s)
FROM table_name
WHERE ROWNUM <= number;
```

（5）SELECT INTO 语句从一个表复制数据，然后把数据插入到另一个新表中；

```sql
SELECT column_name(s)
INTO newtable [IN externaldb]
FROM table1;
-- 样例
SELECT *
INTO WebsitesBackup2016
FROM Websites;
SELECT Websites.name, access_log.count, access_log.date
INTO WebsitesBackup2016
FROM Websites
LEFT JOIN access_log
ON Websites.id=access_log.site_id;
-- SELECT INTO语句可用于通过另一种模式创建一个新的空表，只需要添加促使查询没有数据返回的 WHERE 子句即可
SELECT *
INTO newtable
FROM table1
WHERE 1=0;
-- MySQL 数据库不支持 SELECT ... INTO 语句，但支持 INSERT INTO ... SELECT 
```

### 2、WHERE

（1）WHERE 子句用于提取那些满足指定条件的记录；

（2）语法如下：

```sql
SELECT column_name,column_name
FROM table_name
WHERE column_name operator value;
-- 例如：SELECT * FROM Websites WHERE country='CN';
--      SELECT * FROM Websites WHERE id=1;
```

（3）对于文本字段使用单引号且区分大小写，对于数值字段直接数值即可；

（4）可以使用的运算符：

```
=  等于   <>  不等于   >  大于   <  小于   >=  大于等于   <=  小于等于   BETWEEN(and)  在某个范围内，且下限在前上限在后，并且包含上下限值   LIKE  搜索某种模式   IN  指定针对某个列的多个可能值
```

（5）逻辑运算符：

```
not        and         or
```

（6）特殊条件：

```
is null
```

（7）LIKE模糊查询：

```
% 表示多个字值，_ 下划线表示一个字符；
M% : 为能配符，正则表达式，表示的意思为模糊查询信息为 M 开头的；
%M% : 表示查询包含M的所有内容；
%M_ : 表示查询以M在倒数第二位的所有内容；
-- 例如，SELECT * FROM Websites WHERE name LIKE 'G%';
-- 通过NOT关键词，可以选取不匹配模式，例如，SELECT * FROM Websites WHERE name NOT LIKE '%oo%';
-- 转义_和%使用escape，例如，select * from username where 名字 like '段\_%' escape '\';
```

（8）LIKE和REGEXP

```
LIKE能用的通配符，%，_，[charlist]，[^charlist]或[!charlist]；
不过，MySQL 、SQLite 只支持 % 和 _ 通配符，不支持 [^charlist] 或 [!charlist] 通配符；
MS Access 支持，微软 office 对通配符一直支持良好，但微软有时候的通配符不支持 %，而是 *，具体看对应软件说明；
MySQL 中要完成 [^charlist] 或 [!charlist] 通配符的查询效果，需要通过正则表达式来完成；
SQLite不支持Regexp正则方法；
MySQL 中使用 REGEXP 或 NOT REGEXP 运算符 (或 RLIKE 和 NOT RLIKE) 来操作正则表达式，用法同LIKE；
-- 例如，SELECT * FROM Websites WHERE name REGEXP '^[A-H]';
```

（9）WHERE 子句并不一定带比较运算符，当不带运算符时，会执行一个隐式转换，当 0 时转化为 false，1 转化为 true；

```sql
SELECT studentNO FROM student WHERE 0;
SELECT studentNO FROM student WHERE 1;
```

（10）IN语法：

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1,value2,...);
```

（11）BETWEEN语法：

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
-- NOT BETWEEN，表示不在其中
SELECT * FROM Websites
WHERE alexa NOT BETWEEN 1 AND 20;
-- 带有 IN 的 BETWEEN
SELECT * FROM Websites
WHERE (alexa BETWEEN 1 AND 20)
AND country NOT IN ('USA', 'IND');
-- 带有文本值的 BETWEEN
SELECT * FROM Websites
WHERE name BETWEEN 'A' AND 'H'; -- 选取 name 以介于 'A' 和 'H' 之间字母开始的所有网站
-- 带有文本值的 NOT BETWEEN
SELECT * FROM Websites
WHERE name NOT BETWEEN 'A' AND 'H';
-- 带有日期值的 BETWEEN
SELECT * FROM access_log
WHERE date BETWEEN '2016-05-10' AND '2016-05-14'; -- 选取 date 介于 '2016-05-10' 和 '2016-05-14' 之间的所有访问记录
/*请注意，在不同的数据库中，BETWEEN 操作符会产生不同的结果！
在某些数据库中，BETWEEN 选取介于两个值之间但不包括两个测试值的字段。
在某些数据库中，BETWEEN 选取介于两个值之间且包括两个测试值的字段。
在某些数据库中，BETWEEN 选取介于两个值之间且包括第一个测试值但不包括最后一个测试值的字段。
因此，请检查您的数据库是如何处理 BETWEEN 操作符！*/
```

### 3、ORDER BY

（1）ORDER BY 关键字用于对结果集按照一个列或者多个列进行排序，默认按照升序对记录进行排序，如果需要按照降序对记录进行排序，使用 DESC 关键字；

（2）语法如下：

```sql
SELECT column_name,column_name
FROM table_name
ORDER BY column_name,column_name ASC|DESC;
```

（3）ORDER BY 多列的时候：

```
order by A,B        这个时候都是默认按升序排列
order by A desc,B   这个时候 A 降序，B 升序排列
order by A ,B desc  这个时候 A 升序，B 降序排列
```

### 4、INSERT INTO

（1）INSERT INTO 语句用于向表中插入新记录；

（2）语法如下：

```sql
-- 第一种形式无需指定要插入数据的列名，只需提供被插入的值即可
INSERT INTO table_name
VALUES (value1,value2,value3,...);
-- 第二种形式需要指定列名及被插入的值
INSERT INTO table_name (column1,column2,column3,...)
VALUES (value1,value2,value3,...);
```

（3）没有指定要插入数据的列名的形式需要列出插入行的每一列数据；

（4）insert into select 和select into from 的区别，前者重在更新已存在表的数据，后者重在创建新表：

```sql
insert into scorebak select * from socre where neza='neza' --插入一行,要求表scorebak 必须存在
select *  into scorebak from score  where neza='neza'  --也是插入一行,要求表scorebak 不存在
```

（5）INSERT INTO SELECT 语句从一个表复制数据，然后把数据插入到一个已存在的表中，目标表中任何已存在的行都不会受影响；

```sql
INSERT INTO table2
(column_name(s))
SELECT column_name(s)
FROM table1;
-- 样例
INSERT INTO Websites (name, country)
SELECT app_name, country FROM apps;
```

### 5、UPDATE

（1）UPDATE 语句用于更新表中已存在的记录；

（2）语法如下：

```sql
UPDATE table_name
SET column1=value1,column2=value2,...
WHERE some_column=some_value;
```

（3）**请注意 SQL UPDATE 语句中的 WHERE 子句！**

WHERE 子句规定哪条记录或者哪些记录需要更新。如果您省略了 WHERE 子句，所有的记录都将被更新！

（4）在 MySQL 中可以通过设置 **sql_safe_updates** 这个自带的参数来解决，当该参数开启的情况下，你必须在update 语句后携带 where 条件，否则就会报错，**set sql_safe_updates=1;** 表示开启该参数；

### 6、DELETE

（1）DELETE 语句用于删除表中的行；

（2）语法如下：

```sql
DELETE FROM table_name
WHERE some_column=some_value;
```

（3）**请注意 SQL DELETE 语句中的 WHERE 子句！**
WHERE 子句规定哪条记录或者哪些记录需要删除。如果您省略了 WHERE 子句，所有的记录都将被删除！

（4）删除所有数据，表结构、属性、索引将保持不变：

```sql
DELETE FROM table_name;
DELETE * FROM table_name;
```

（5）三条删除语句的区别：

```sql
DROP test; -- 删除表test，并释放空间，将test删除的一干二净
TRUNCATE test; -- 删除表test里的内容，并释放空间，但不删除表的定义，表的结构还在
DELETE FROM table_name; -- 仅删除表test内的所有内容，保留表的定义，不释放空间
DELETE * FROM table_name; -- 仅删除表test内的所有内容，保留表的定义，不释放空间
-- 也就是delete删除的数据可以恢复，truncate和drop删除的数据无法恢复
```

（6）在 MySQL 中可以通过设置 **sql_safe_updates** 这个自带的参数来解决，当该参数开启的情况下，你必须在delete 语句后携带 where 条件，否则就会报错，**set sql_safe_updates=1;** 表示开启该参数；

### 7、别名

（1）列的 SQL 别名语法（如果列名称包含空格，要求使用双引号或方括号）：

```sql
SELECT column_name AS alias_name
FROM table_name;
-- 样例
SELECT name, CONCAT(url, ', ', alexa, ', ', country) AS site_info
FROM Websites; -- 把三个列（url、alexa 和 country）结合在一起，并创建一个名为 "site_info" 的别名
```

（2）表的 SQL 别名语法：

```sql
SELECT column_name(s)
FROM table_name AS alias_name;
-- 样例
SELECT w.name, w.url, a.count, a.date
FROM Websites AS w, access_log AS a
WHERE a.site_id=w.id and w.name="菜鸟教程";
```

（3）在下列情况中使用别名很有用：

- 在查询中涉及超过一个表
- 在查询中使用了函数
- 列名称很长或者可读性差
- 需要把两个列或者多个列结合在一起

### 8、JOIN

（1）JOIN 子句用于把来自两个或多个表的行结合起来，基于这些表之间的共同字段；

![](/4.png)

（2）不同的JOIN类型：

```
INNER JOIN：如果表中有至少一个匹配，则返回行
LEFT JOIN：即使右表中没有匹配，也从左表返回所有的行
RIGHT JOIN：即使左表中没有匹配，也从右表返回所有的行
FULL OUTER JOIN：只要其中一个表中存在匹配，则返回行
```

（3）MySQL 暂不支持 FULL OUTER JOIN，MySQL实现完全外部链接，要使用 UNION 将一个左链接、和一个右链接去重合并；

```
SELECT a.*,b.*
FROM 表1 a LEFT JOIN 表2 b
ON a.unit_NO = b.unit_NO

UNION

SELECT a.*,b.*
FROM 表1 a RIGHT JOIN 表2 b
ON a.unit_NO = b.unit_NO;
```

（4）首先，连接的结果可以在逻辑上看作是由SELECT语句指定的列组成的新表，左连接与右连接的左右指的是以两张表中的哪一张为基准，它们都是外连接；

（5）外连接就好像是为非基准表添加了一行全为空值的万能行，用来与基准表中找不到匹配的行进行匹配，假设两个没有空值的表进行左连接，左表是基准表，左表的所有行都出现在结果中，右表则可能因为无法与基准表匹配而出现是空值的字段；

（6）INNER JOIN 语法，在表中存在至少一个匹配时返回行：

```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name=table2.column_name;
-- 或，INNER JOIN 与 JOIN 是相同的
SELECT column_name(s)
FROM table1
JOIN table2
ON table1.column_name=table2.column_name;
```

（7）LEFT JOIN 关键字从左表（table1）返回所有的行，即使右表（table2）中没有匹配。如果右表中没有匹配，则结果为 NULL；

```sql
SELECT column_name(s)
FROM table1
LEFT JOIN table2
ON table1.column_name=table2.column_name;
-- 或，在某些数据库中，LEFT JOIN 称为 LEFT OUTER JOIN
SELECT column_name(s)
FROM table1
LEFT OUTER JOIN table2
ON table1.column_name=table2.column_name;
```

（8）RIGHT JOIN 关键字从右表（table2）返回所有的行，即使左表（table1）中没有匹配。如果左表中没有匹配，则结果为 NULL；

```sql
SELECT column_name(s)
FROM table1
RIGHT JOIN table2
ON table1.column_name=table2.column_name;
-- 或，在某些数据库中，RIGHT JOIN 称为 RIGHT OUTER JOIN
SELECT column_name(s)
FROM table1
RIGHT OUTER JOIN table2
ON table1.column_name=table2.column_name;
```

（9）FULL OUTER JOIN 关键字只要左表（table1）和右表（table2）其中一个表中存在匹配，则返回行；

```sql
SELECT column_name(s)
FROM table1
FULL OUTER JOIN table2
ON table1.column_name=table2.column_name;
```

（10）简单记法：

```
A inner join B 取交集。
A left join B 取 A 全部，B 没有对应的值为 null。
A right join B 取 B 全部 A 没有对应的值为 null。
A full outer join B 取并集，彼此没有对应的值为 null。
对应条件在 on 后面填写。
```

（11）ON用法：

```
数据库在通过连接两张或多张表来返回记录时，都会生成一张中间的临时表，然后再将这张临时表返回给用户；
on 条件是在生成临时表时使用的条件，它不管 on 中的条件是否为真，都会返回表中的记录；
where 条件是在临时表生成好后，再对临时表进行过滤的条件；
```

### 9、UNION

（1）UNION 操作符合并两个或多个 SELECT 语句的结果；

（2）请注意，UNION 内部的每个 SELECT 语句必须拥有相同数量的列，列也必须拥有相似的数据类型，同时，每个 SELECT 语句中的列的顺序必须相同，UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名；

（3）SQL UNION 语法：

```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

（4）默认地，UNION 操作符选取不同的值。如果允许重复的值，请使用 UNION ALL；

```sql
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

（5）使用UNION命令时需要注意，只能在最后使用一个ORDER BY命令，是将两个查询结果合在一起之后，再进行排序！绝对不能写两个ORDER BY命令；

### 10、CREATE

（1）CREATE DATABASE 语句用于创建数据库；

```sql
CREATE DATABASE dbname;
```

（2）CREATE TABLE 语句用于创建数据库中的表，表由行和列组成，每个表都必须有个表名；

```sql
CREATE TABLE table_name
(
column_name1 data_type(size),
column_name2 data_type(size),
column_name3 data_type(size),
....
);
/*
column_name 参数规定表中列的名称。
data_type 参数规定列的数据类型（例如 varchar、integer、decimal、date 等等）。
size 参数规定表中列的最大长度。
*/
```

（3）CREATE INDEX 语句用于在表中创建索引，以便更加快速高效地查询数据，用户无法看到索引，更新一个包含索引的表需要比更新一个没有索引的表花费更多的时间，这是由于索引本身也需要更新；

```sql
-- 创建简单索引
CREATE INDEX index_name
ON table_name (column_name)
-- 创建唯一索引
CREATE UNIQUE INDEX index_name
ON table_name (column_name)
-- 用于创建索引的语法在不同的数据库中不一样
```

### 11、DROP

（1）DROP INDEX 语句用于删除表中的索引；

```sql
-- 用于 MS Access 的 DROP INDEX 语法
DROP INDEX index_name ON table_name
-- 用于 MS SQL Server 的 DROP INDEX 语法
DROP INDEX table_name.index_name
-- 用于 DB2/Oracle 的 DROP INDEX 语法
DROP INDEX index_name
-- 用于 MySQL 的 DROP INDEX 语法
ALTER TABLE table_name DROP INDEX index_name
```

（2）DROP TABLE 语句用于删除表；

```sql
DROP TABLE table_name
```

（3）DROP DATABASE 语句用于删除数据库；

```sql
DROP DATABASE database_name
```

（4）仅仅需要删除表内的数据，但并不删除表本身使用TRUNCATE TABLE 语句；

```
TRUNCATE TABLE table_name
```

### 12、ALTER

ALTER TABLE 语句用于在已有的表中添加、删除或修改列。

（1）添加列；

```sql
ALTER TABLE table_name
ADD column_name datatype
```

（2）删除列；

```sql
ALTER TABLE table_name
DROP COLUMN column_name
```

（3）修改列；

```sql
-- SQL Server / MS Access
ALTER TABLE table_name
ALTER COLUMN column_name datatype
-- My SQL / Oracle
ALTER TABLE table_name
MODIFY COLUMN column_name datatype
-- Oracle 10G 之后版本
ALTER TABLE table_name
MODIFY column_name datatype
```

（4）更改列名和属性

```sql
ALTER TABLE table_name
change column_name column_name datatype
```

### 13、约束

（1）SQL 约束用于规定表中的数据规则，如果存在违反约束的数据行为，行为会被约束终止，约束可以在创建表时规定（通过 CREATE TABLE 语句），或者在表创建之后规定（通过 ALTER TABLE 语句）；

```sql
CREATE TABLE table_name
(
column_name1 data_type(size) constraint_name,
column_name2 data_type(size) constraint_name,
column_name3 data_type(size) constraint_name,
....
);
```

（2）约束分类：

- **NOT NULL** - 指示某列不能存储 NULL 值。
- **UNIQUE** - 保证某列的每行必须有唯一的值。
- **PRIMARY KEY** - NOT NULL 和 UNIQUE 的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录。
- **FOREIGN KEY** - 保证一个表中的数据匹配另一个表中的值的参照完整性。
- **CHECK** - 保证列中的值符合指定的条件。
- **DEFAULT** - 规定没有给列赋值时的默认值。

（3）NOT NULL 约束，在默认的情况下，表的列接受 NULL 值，NOT NULL 约束强制列不接受 NULL 值这意味着，如果不向字段添加值，就无法插入新记录或者更新记录；

```sql
-- 样例，创建时规定NOT NULL约束
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255) NOT NULL,
    Age int
);
-- 样例，添加 NOT NULL 约束
ALTER TABLE Persons
MODIFY Age int NOT NULL;
-- 样例，删除 NOT NULL 约束
ALTER TABLE Persons
MODIFY Age int NULL;
```

（4）UNIQUE 约束，唯一标识数据库表中的每条记录，UNIQUE 和 PRIMARY KEY 约束均为列或列集合提供了唯一性的保证，PRIMARY KEY 约束拥有自动定义的 UNIQUE 约束，请注意，每个表可以有多个 UNIQUE 约束，但是每个表只能有一个 PRIMARY KEY 约束；

```sql
-- CREATE TABLE 时的 SQL UNIQUE 约束
-- MySQL
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
UNIQUE (P_Id)
)
-- SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
P_Id int NOT NULL UNIQUE,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
-- 如需命名 UNIQUE 约束，并定义多个列的 UNIQUE 约束，请使用下面的 SQL 语法
-- MySQL / SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
CONSTRAINT uc_PersonID UNIQUE (P_Id,LastName)
)
-- ALTER TABLE 时的 SQL UNIQUE 约束
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD UNIQUE (P_Id)
-- 如需命名 UNIQUE 约束，并定义多个列的 UNIQUE 约束，请使用下面的 SQL 语法
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD CONSTRAINT uc_PersonID UNIQUE (P_Id,LastName)
-- 撤销 UNIQUE 约束
-- MySQL
ALTER TABLE Persons
DROP INDEX uc_PersonID
-- SQL Server / Oracle / MS Access
ALTER TABLE Persons
DROP CONSTRAINT uc_PersonID
-- uc_PersonID 是一个约束名，可以自行拟定
```

（5）PRIMARY KEY 约束，唯一标识数据库表中的每条记录，主键必须包含唯一的值，主键列不能包含 NULL 值，每个表都应该有一个主键，并且每个表只能有一个主键；

```sql
-- CREATE TABLE 时的 SQL PRIMARY KEY 约束
-- MySQL
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
PRIMARY KEY (P_Id)
)
-- SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
P_Id int NOT NULL PRIMARY KEY,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
-- 如需命名 PRIMARY KEY 约束，并定义多个列的 PRIMARY KEY 约束，请使用下面的 SQL 语法
-- MySQL / SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName)
)
-- ALTER TABLE 时的 SQL PRIMARY KEY 约束
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD PRIMARY KEY (P_Id)
-- 如需命名 PRIMARY KEY 约束，并定义多个列的 PRIMARY KEY 约束，请使用下面的 SQL 语法
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName)
-- 如果使用 ALTER TABLE 语句添加主键，必须把主键列声明为不包含 NULL 值（在表首次创建时）
-- 撤销 PRIMARY KEY 约束
-- MySQL
ALTER TABLE Persons
DROP PRIMARY KEY
-- SQL Server / Oracle / MS Access
ALTER TABLE Persons
DROP CONSTRAINT pk_PersonID
```

（6）FOREIGN KEY 约束，指向另一个表中的 UNIQUE KEY(唯一约束的键)，用于预防破坏表之间连接的行为，也能防止非法数据插入外键列，因为它必须是它指向的那个表中的值之一；

```sql
-- CREATE TABLE 时的 SQL FOREIGN KEY 约束
-- MySQL
CREATE TABLE Orders
(
O_Id int NOT NULL,
OrderNo int NOT NULL,
P_Id int,
PRIMARY KEY (O_Id),
FOREIGN KEY (P_Id) REFERENCES Persons(P_Id)
)
-- SQL Server / Oracle / MS Access
CREATE TABLE Orders
(
O_Id int NOT NULL PRIMARY KEY,
OrderNo int NOT NULL,
P_Id int FOREIGN KEY REFERENCES Persons(P_Id)
)
-- 如需命名 FOREIGN KEY 约束，并定义多个列的 FOREIGN KEY 约束，请使用下面的 SQL 语法
-- MySQL / SQL Server / Oracle / MS Access
CREATE TABLE Orders
(
O_Id int NOT NULL,
OrderNo int NOT NULL,
P_Id int,
PRIMARY KEY (O_Id),
CONSTRAINT fk_PerOrders FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
)
-- ALTER TABLE 时的 SQL FOREIGN KEY 约束
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Orders
ADD FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
-- 如需命名 FOREIGN KEY 约束，并定义多个列的 FOREIGN KEY 约束，请使用下面的 SQL 语法
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Orders
ADD CONSTRAINT fk_PerOrders
FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
-- 撤销 FOREIGN KEY 约束
-- MySQL
ALTER TABLE Orders
DROP FOREIGN KEY fk_PerOrders
-- SQL Server / Oracle / MS Access
ALTER TABLE Orders
DROP CONSTRAINT fk_PerOrders
```

（7）CHECK 约束，用于限制列中的值的范围，如果对单个列定义 CHECK 约束，那么该列只允许特定的值，如果对一个表定义 CHECK 约束，那么此约束会基于行中其他列的值在特定的列中对值进行限制；

```sql
-- CREATE TABLE 时的 SQL CHECK 约束
-- MySQL
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
CHECK (P_Id>0)
)
-- SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
P_Id int NOT NULL CHECK (P_Id>0),
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
-- 如需命名 CHECK 约束，并定义多个列的 CHECK 约束，请使用下面的 SQL 语法
-- MySQL / SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
CONSTRAINT chk_Person CHECK (P_Id>0 AND City='Sandnes')
)
-- ALTER TABLE 时的 SQL CHECK 约束
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD CHECK (P_Id>0)
-- 如需命名 CHECK 约束，并定义多个列的 CHECK 约束，请使用下面的 SQL 语法
-- MySQL / SQL Server / Oracle / MS Access
ALTER TABLE Persons
ADD CONSTRAINT chk_Person CHECK (P_Id>0 AND City='Sandnes')
-- 撤销 CHECK 约束
-- SQL Server / Oracle / MS Access
ALTER TABLE Persons
DROP CONSTRAINT chk_Person
-- MySQL
ALTER TABLE Persons
DROP CHECK chk_Person
```

（8）DEFAULT 约束，用于向列中插入默认值，如果没有规定其他的值，那么会将默认值添加到所有的新记录；

```sql
-- CREATE TABLE 时的 SQL DEFAULT 约束
-- My SQL / SQL Server / Oracle / MS Access
CREATE TABLE Persons
(
    P_Id int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Address varchar(255),
    City varchar(255) DEFAULT 'Sandnes'
)
-- ALTER TABLE 时的 SQL DEFAULT 约束
-- MySQL
ALTER TABLE Persons
ALTER City SET DEFAULT 'SANDNES'
-- SQL Server / MS Access
ALTER TABLE Persons
ADD CONSTRAINT ab_c DEFAULT 'SANDNES' for City
-- Oracle
ALTER TABLE Persons
MODIFY City DEFAULT 'SANDNES'
-- 撤销 DEFAULT 约束
-- MySQL
ALTER TABLE Persons
ALTER City DROP DEFAULT
-- SQL Server / Oracle / MS Access
ALTER TABLE Persons
ALTER COLUMN City DROP DEFAULT
```

（9）Auto-increment 会在新记录插入表中时生成一个唯一的数字，这不是个约束，但是使用位置相同；

```sql
-- 用于 MySQL 的语法
CREATE TABLE Persons
(
ID int NOT NULL AUTO_INCREMENT, -- 默认开始为1，递增1，也可以规定起始值，例如=100
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
PRIMARY KEY (ID)
)
-- 用于 SQL Server 的语法
CREATE TABLE Persons
(
ID int IDENTITY(1,1) PRIMARY KEY, -- 可以将(1,1)改为(n,m)，表示以n开始，递增+m
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
-- 用于 Access 的语法
CREATE TABLE Persons
(
ID Integer PRIMARY KEY AUTOINCREMENT, -- 可以加上(n,m)，表示以n开始，递增+m
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255)
)
-- 用于 Oracle 的语法
CREATE SEQUENCE seq_person
MINVALUE 1
START WITH 1
INCREMENT BY 1
CACHE 10 -- 规定了为了提高访问速度要存储多少个序列值
-- 使用
INSERT INTO Persons (ID,FirstName,LastName)
VALUES (seq_person.nextval,'Lars','Monsen')
-- 给已经存在的colume添加自增语法
ALTER TABLE table_name CHANGE column_name column_name data_type(size) constraint_name AUTO_INCREMENT;
-- 例如
ALTER TABLE student CHANGE id id INT( 11 ) NOT NULL AUTO_INCREMENT;
```

### 14、视图

视图是基于 SQL 语句的结果集的可视化的表，视图中的字段就是来自一个或多个数据库中的真实的表中的字段，视图隐藏了底层的表结构，简化了数据访问操作，加强了安全性，使用户只能看到视图所显示的数据；

（1）创建视图；

```sql
CREATE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition
```

（2）更新视图

```sql
CREATE OR REPLACE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition
-- SQL Server
ALTER VIEW [ schema_name . ] view_name [ ( column [ ,...n ] ) ] 
[ WITH <view_attribute> [ ,...n ] ] 
AS select_statement 
[ WITH CHECK OPTION ] [ ; ]

<view_attribute> ::= 
{ 
    [ ENCRYPTION ]
    [ SCHEMABINDING ]
    [ VIEW_METADATA ]     
} 
/*
schema_name: 视图所属架构的名称。
view_name: 要更改的视图。
column: 将成为指定视图的一部分的一个或多个列的名称（以逗号分隔）。
*/
```

（3）撤销视图；

```sql
DROP VIEW view_name
```

### 15、NULL

NULL 值代表遗漏的未知数据，如果表中的某个列是可选的，那么我们可以在不向该列添加值的情况下插入新记录或更新已有的记录，这意味着该字段将以 NULL 值保存，NULL 值的处理方式与其他值不同，NULL 用作未知的或不适用的值的占位符，无法比较 NULL 和 0，它们是不等价的；

（1）IS NULL，查找 NULL 值；

```sql
-- 例如
SELECT LastName,FirstName,Address FROM Persons
WHERE Address IS NULL
```

（2）IS NOT NULL，查找非NULL 值；

```sql
-- 例如
SELECT LastName,FirstName,Address FROM Persons
WHERE Address IS NOT NULL
```

## 三、数据类型

### 1、通用数据类型

数据库表中的每个列都要求有名称和数据类型；

| 数据类型                           | 描述                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| CHARACTER(n)                       | 字符/字符串。固定长度 n。                                    |
| VARCHAR(n) 或 CHARACTER VARYING(n) | 字符/字符串。可变长度。最大长度 n。                          |
| BINARY(n)                          | 二进制串。固定长度 n。                                       |
| BOOLEAN                            | 存储 TRUE 或 FALSE 值                                        |
| VARBINARY(n) 或 BINARY VARYING(n)  | 二进制串。可变长度。最大长度 n。                             |
| INTEGER(p)                         | 整数值（没有小数点）。精度 p。                               |
| SMALLINT                           | 整数值（没有小数点）。精度 5。                               |
| INTEGER                            | 整数值（没有小数点）。精度 10。                              |
| BIGINT                             | 整数值（没有小数点）。精度 19。                              |
| DECIMAL(p,s)                       | 精确数值，精度 p，小数点后位数 s。例如：decimal(5,2) 是一个小数点前有 3 位数，小数点后有 2 位数的数字。 |
| NUMERIC(p,s)                       | 精确数值，精度 p，小数点后位数 s。（与 DECIMAL 相同）        |
| FLOAT(p)                           | 近似数值，尾数精度 p。一个采用以 10 为基数的指数计数法的浮点数。该类型的 size 参数由一个指定最小精度的单一数字组成。 |
| REAL                               | 近似数值，尾数精度 7。                                       |
| FLOAT                              | 近似数值，尾数精度 16。                                      |
| DOUBLE PRECISION                   | 近似数值，尾数精度 16。                                      |
| DATE                               | 存储年、月、日的值。                                         |
| TIME                               | 存储小时、分、秒的值。                                       |
| TIMESTAMP                          | 存储年、月、日、小时、分、秒的值。                           |
| INTERVAL                           | 由一些整数字段组成，代表一段时间，取决于区间的类型。         |
| ARRAY                              | 元素的固定长度的有序集合                                     |
| MULTISET                           | 元素的可变长度的无序集合                                     |
| XML                                | 存储 XML 数据                                                |

### 2、SQL 数据类型快速参考手册

| 数据类型            | Access                  | SQLServer                                            | Oracle           | MySQL       | PostgreSQL       |
| ------------------- | ----------------------- | ---------------------------------------------------- | ---------------- | ----------- | ---------------- |
| *boolean*           | Yes/No                  | Bit                                                  | Byte             | N/A         | Boolean          |
| *integer*           | Number (integer)        | Int                                                  | Number           | Int Integer | Int Integer      |
| *float*             | Number (single)         | Float Real                                           | Number           | Float       | Numeric          |
| *currency*          | Currency                | Money                                                | N/A              | N/A         | Money            |
| *string (fixed)*    | N/A                     | Char                                                 | Char             | Char        | Char             |
| *string (variable)* | Text (<256) Memo (65k+) | Varchar                                              | Varchar Varchar2 | Varchar     | Varchar          |
| *binary object*     | OLE Object Memo         | Binary (fixed up to 8K) Varbinary (<8K) Image (<2GB) | Long Raw         | Blob Text   | Binary Varbinary |

### 3、SQL 用于各种数据库的数据类型

（1）Microsoft Access 数据类型

![](/5.PNG)

（2）MySQL 数据类型

a、**Text 类型**

![](/6.PNG)

b、**Number 类型**

![](/7.PNG)

注意：以上的 size 代表的并不是存储在数据库中的具体的长度，如 int(4) 并不是只能存储4个长度的数字，实际上int(size)所占多少存储空间并无任何关系。int(3)、int(4)、int(8) 在磁盘上都是占用 4 btyes 的存储空间，就是显示的长度不一样而已 都是占用四个字节的空间；

c、**Date 类型**

![](/8.PNG)

（3）SQL Server 数据类型

a、**String 类型**

![](/9.PNG)

b、**Number 类型**

![](/10.PNG)

c、**Date 类型**

![](/11.PNG)

d、**其他数据类型**

![](/12.PNG)

## 四、函数

### 1、NULL

用于处理值为NULL，需要替换的情况；

```sql
-- SQL Server / MS Access，表示如果UnitsOnOrder列中值为NULL，替换为0进行计算
SELECT ProductName,UnitPrice*(UnitsInStock+ISNULL(UnitsOnOrder,0))
FROM Products
-- Oracle，没有ISNULL()函数，但是有NVL()，同上
SELECT ProductName,UnitPrice*(UnitsInStock+NVL(UnitsOnOrder,0))
FROM Products
-- MySQL
SELECT ProductName,UnitPrice*(UnitsInStock+IFNULL(UnitsOnOrder,0))
FROM Products
-- 或
SELECT ProductName,UnitPrice*(UnitsInStock+COALESCE(UnitsOnOrder,0))
FROM Products
```

### 2、Date

（1）MySQL 中最重要的内建日期函数：

| 函数                                                         | 描述                                |
| ------------------------------------------------------------ | ----------------------------------- |
| [NOW()](https://www.runoob.com/sql/func-now.html)            | 返回当前的日期和时间                |
| [CURDATE()](https://www.runoob.com/sql/func-curdate.html)    | 返回当前的日期                      |
| [CURTIME()](https://www.runoob.com/sql/func-curtime.html)    | 返回当前的时间                      |
| [DATE()](https://www.runoob.com/sql/func-date.html)          | 提取日期或日期/时间表达式的日期部分 |
| [EXTRACT()](https://www.runoob.com/sql/func-extract.html)    | 返回日期/时间的单独部分             |
| [DATE_ADD()](https://www.runoob.com/sql/func-date-add.html)  | 向日期添加指定的时间间隔            |
| [DATE_SUB()](https://www.runoob.com/sql/func-date-sub.html)  | 从日期减去指定的时间间隔            |
| [DATEDIFF()](https://www.runoob.com/sql/func-datediff-mysql.html) | 返回两个日期之间的天数              |
| [DATE_FORMAT()](https://www.runoob.com/sql/func-date-format.html) | 用不同的格式显示日期/时间           |

**MySQL** 使用下列数据类型在数据库中存储日期或日期/时间值：

- DATE - 格式：YYYY-MM-DD
- DATETIME - 格式：YYYY-MM-DD HH:MM:SS
- TIMESTAMP - 格式：YYYY-MM-DD HH:MM:SS
- YEAR - 格式：YYYY 或 YY

（2）SQL Server 中最重要的内建日期函数：

| 函数                                                        | 描述                             |
| ----------------------------------------------------------- | -------------------------------- |
| [GETDATE()](https://www.runoob.com/sql/func-getdate.html)   | 返回当前的日期和时间             |
| [DATEPART()](https://www.runoob.com/sql/func-datepart.html) | 返回日期/时间的单独部分          |
| [DATEADD()](https://www.runoob.com/sql/func-dateadd.html)   | 在日期中添加或减去指定的时间间隔 |
| [DATEDIFF()](https://www.runoob.com/sql/func-datediff.html) | 返回两个日期之间的时间           |
| [CONVERT()](https://www.runoob.com/sql/func-convert.html)   | 用不同的格式显示日期/时间        |

**SQL Server** 使用下列数据类型在数据库中存储日期或日期/时间值：

- DATE - 格式：YYYY-MM-DD
- DATETIME - 格式：YYYY-MM-DD HH:MM:SS
- SMALLDATETIME - 格式：YYYY-MM-DD HH:MM:SS
- TIMESTAMP - 格式：唯一的数字

（3）如果您希望使查询简单且更易维护，那么请不要在日期中使用时间部分，如果没有时间部分，默认时间为 00:00:00，所以可能查不到结果；

### 3、Aggregate

SQL Aggregate 函数计算从列中取得的值，返回一个单一的值；

（1）AVG() 函数返回数值列的平均值；

```sql
SELECT AVG(column_name) FROM table_name
```

（2）COUNT() 函数返回匹配指定条件的行数；

```sql
-- COUNT(column_name) 函数返回指定列的值的数目（NULL 不计入）
SELECT COUNT(column_name) FROM table_name;
-- COUNT(*) 函数返回表中的记录数
SELECT COUNT(*) FROM table_name;
-- COUNT(DISTINCT column_name) 函数返回指定列的不同值的数目
SELECT COUNT(DISTINCT column_name) FROM table_name;
-- COUNT(DISTINCT) 适用于 ORACLE 和 Microsoft SQL Server，但是无法用于 Microsoft Access
```

（3）FIRST() 函数返回指定的列中第一个记录的值；

```sql
-- 只有 MS Access 支持 FIRST() 函数
SELECT FIRST(column_name) FROM table_name;
-- SQL Server
SELECT TOP 1 column_name FROM table_name
ORDER BY column_name ASC;
-- MySQL
SELECT column_name FROM table_name
ORDER BY column_name ASC
LIMIT 1;
-- Oracle
SELECT column_name FROM table_name
ORDER BY column_name ASC
WHERE ROWNUM <=1;
```

（4）LAST() 函数返回指定的列中最后一个记录的值；

```sql
-- 只有 MS Access 支持 LAST() 函数
SELECT LAST(column_name) FROM table_name;
-- SQL Server
SELECT TOP 1 column_name FROM table_name
ORDER BY column_name DESC;
-- MySQL
SELECT column_name FROM table_name
ORDER BY column_name DESC
LIMIT 1;
-- Oracle
SELECT column_name FROM table_name
ORDER BY column_name DESC
WHERE ROWNUM <=1;
```

（5）MAX() 函数返回指定列的最大值；

```sql
SELECT MAX(column_name) FROM table_name;
```

（6）MIN() 函数返回指定列的最小值；

```sql
SELECT MIN(column_name) FROM table_name;
```

（7）SUM() 函数返回数值列的总数；

```sql
SELECT SUM(column_name) FROM table_name;
```

（8）GROUP BY 语句用于结合聚合函数，根据一个或多个列对结果集进行分组；

```sql
SELECT column_name, aggregate_function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;
```

（9）在 SQL 中增加 HAVING 子句原因是，WHERE 关键字无法与聚合函数一起使用，HAVING 子句可以让我们筛选分组后的各组数据；

```sql
SELECT column_name, aggregate_function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name
HAVING aggregate_function(column_name) operator value;
-- 实例
SELECT Websites.name, Websites.url, SUM(access_log.count) AS nums FROM (access_log
INNER JOIN Websites
ON access_log.site_id=Websites.id)
GROUP BY Websites.name
HAVING SUM(access_log.count) > 200;
```

（10）EXISTS 运算符用于判断查询子句是否有记录，如果有一条或多条记录存在返回 True，否则返回 False；

```sql
SELECT column_name(s)
FROM table_name
WHERE EXISTS
(SELECT column_name FROM table_name WHERE condition);
-- EXISTS 可以与 NOT 一同使用，查找出不符合查询语句的记录
SELECT Websites.name, Websites.url 
FROM Websites 
WHERE NOT EXISTS (SELECT count FROM access_log WHERE Websites.id = access_log.site_id AND count > 200);
```

### 4、Scalar

SQL Scalar 函数基于输入值，返回一个单一的值；

（1）UCASE() 函数把字段的值转换为大写；

```sql
SELECT UCASE(column_name) FROM table_name;
-- SQL Server
SELECT UPPER(column_name) FROM table_name;
```

（2）LCASE() 函数把字段的值转换为小写；

```sql
SELECT LCASE(column_name) FROM table_name;
-- 用于 SQL Server 的语法
SELECT LOWER(column_name) FROM table_name;
```

（3）MID() 函数用于从文本字段中提取字符；

```sql
SELECT MID(column_name,start[,length]) FROM table_name;
/*
column_name	必需。要提取字符的字段。
start	必需。规定开始位置（起始值是 1）。
length	可选。要返回的字符数。如果省略，则 MID() 函数返回剩余文本。
*/
-- Oracle 
select substr(("列名"，a,b) from <table_name>;
```

（4）LEN() 函数返回文本字段中值的长度；

```sql
SELECT LEN(column_name) FROM table_name;
-- MySQL
SELECT LENGTH(column_name) FROM table_name;
```

（5）ROUND() 函数用于把数值字段舍入为指定的小数位数；

```sql
SELECT ROUND(column_name,decimals) FROM table_name;
/*
column_name	必需。要舍入的字段。
decimals	必需。规定要返回的小数位数。
*/
-- ROUND(X)： 返回参数X的四舍五入的一个整数
-- ROUND(X,D)： 返回参数X的四舍五入的有 D 位小数的一个数字。如果D为0，结果将没有小数点或小数部分。
-- ROUND 返回值被变换为一个BIGINT
```

（6）FORMAT() 函数用于对字段的显示进行格式化；

```sql
SELECT FORMAT(column_name,format) FROM table_name;
/*
column_name	必需。要格式化的字段。
format	必需。规定格式。
*/
```

