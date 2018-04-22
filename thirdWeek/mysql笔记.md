#mysql笔记
————————————————————————
##一：基础操作
```
net start mysql  启动
net stop mysql   关闭
mysql -uroot -p (-p - h) 登陆
mysql -V  输出版本信息并且退出
PROMTPT \D 完整日期
PROMTPT \d 当前数据库
PROMTPT \h 服务器名称
PROMTPT \u 当前用户
SELECT VERSION（） 显示版本
SELECT NOW（）     显示时间
SELECT USER（）    显示用户
```

#二：数据库
————————————————————————

```
基本操作：
SHOW WARNINGS 显示错误信息
SHOW DATABASES 显示数据库列表
SELECT * FROM.(bd.name)；

创建：
CREATE DATABASE (IF NOT EXISTS) db_name CHARACTER SET = charset_name
（若无CHARACTER STE = charset_name则默认为utf-8）

修改
ALTER DATABASE db_name CHARACTER SET = charset_name
（若无CHARACTER STE = charset_name则默认为utf-8）

删除
DROP DATABASE bd_name (IF EXISTS) bd_name
```

#三：数据类型
————————————————————————
整型
浮点型
日期时间型
字符型

#四：数据表
————————————————————————
##基本操作：
```
USE ‘basedata_name’  打开数据表 
SHOW TABLES （FROM db_name） 查看数据表列表
SHOW COLUMNS FROM tbl_name;  查看数据表结构

创建：
CREATE TABLE (IF NOT EXISTS) table_name
(column_name data_type,
....
);	

插入：
①INSERT [INTO] tb1_name [
(col_name,...)][VALUES|VALUE]({expr|DEFAULT},...),(...),...;
可以使用子查询
②INSERT [INTO] tb1_name SET COL_NAME = {expr|DEFAULT},..
可以将查询结果插入指定数据表
③INSERT [INTO] tb1_name[(col_name,...)]SELECT..

查找：
SELECT expr ...FROM tbl_name;

添加单列
ALTER TABLE tb1_name ADD col_name;

添加多列
ALTER TABLE th1_name ADD (col_name ...);

删除列
ALTER TABLE tb1_name DROP col_name;

添加主键约束
ALTER TABLE tb1_name ADD [CONSTRAINT [symbol]] PRIMARY KEY [index_type] (index_col_name);

删除主键约束
ALTER TABLE tb1_name DROP PRIMARY KEY

添加唯一约束
ALTER TABLE tb1_name ADD[CONSTRAINT [symbol] UNIQUE [INDEX|KEY] [index_name] [index_type] (index_col_name,....)]

删除唯一约束
ALTER TABLE tb1_name DROP {INDEX | KEY} index_name

添加外键约束
ALTER TABLE tb1_name ADD [CONSTRAINT[symbol]] FOREIGN KEY [index_name] (index_col_name,...) reference_definition;

删除外键约束
ALTER TABLE t1_name DROP FOREIGN KEY fk_symbol

添加/删除外键约束
ALTER TABLE tb1_name ALTER [COLUMN] col_name {SET DEFAULT literal | DROP DEFAULT}

修改列定义
ALTER TABLE tb1_name MODIFY [COLUMN] col_name column_definition [FIRST | AFTER col_name]

修改列名称
ALTER TABLE tb1_name CHANGE [COLUMN]old_col_name new_col_name column_definition [FIRST | AFTER col_name]

数据表更名
①ALTER TABLE tb1_name RENAME[TO|AS] new_tbl_name
②RENAME TABLE tb1_name TO new_tb1_name [,tb1_name2 TO new_tb1_name2]....

更新记录(单表更新)
UPDATE[LOW_PRIORITY][IGNORE]table_reference SET col_name1 = {expr1 | DEFAULT}[,col_name2 = {expr2 | DEFAULT}[WHERE where_condition]

删除记录（单单删除）
DELETE FROM tb1_name [WHERE where_condition]

SELECT select_expr[,select_expr...]
[
	FORM table_references
	[WHERE where_condition]
	[GROUP BY{col_name|position}[ASC|DESC],...]
	[HAVING where_condition]
	[ORDER BY{col_name|expr|position}[ASC|DESC],...]
	[LIMIT{[offset,]row_count|row_count OFFSET offset}]
]

查询结果分组
[GROUP BY {col_name|position}[ASC|DESC],...]
group by 1按查询结果的第一组分组

分组条件
[HAVING where_condition]

对查询结果进行排序
[ORDER BY {col_name|expr|position}[ASC|DESC],....]

限制查询结果返回的数量
[LIMIT{[offset,]row_count|row_count OFFSET offset}]

```
![Alt text](./1524033740732.png)

##默认约束名
主键（Primary Key）约束：如 PK_表名
唯一（Unique Key）约束：如 UQ_表名
默认（Default Key）约束：如 DF_表名
检查（Check Key）约束：如 CK_表名
外键（Foreign Key）约束：如 FK_表名

##PRIMARY KEY
主键约束
每张数据表只能有一个主键
记录唯一
自动为NOT NULL
```
CREATE TABLE tb3(
id SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY
);
```
##UNIQUE KEY
唯一约束
每张数据表可以存在多个唯一约束
保证几率的唯一性
可以有且最多有一个NULL字段
```
username VARCHAR(20) NOT NULL UNIQUE KEY,
```
##DEFAULT
默认约束
```
sex ENUM('1','2','3') DEFAULT '3'
```
#五：约束
————————————————————————
##基本信息
保证数据的完整性和一致性
分为表级约束和列级约束
类型包括：
NOT NULL    非空约束
PRIMARY KEY   主键约束
UNIQUE KEY	唯一约束
DEFAULT	默认约束
FOREIGN KEY	外键约束
##FOREIGN KEY
保持数据一致性，完整性
实现一对一或一对多关系
##外键约束的要求
![Alt text](./1523717221454.png)
![Alt text](./1523717251479.png)
![Alt text](./1523717254053.png)


#六:子查询与链接
子查询是指在另一个查询语句中的SELECT子句。
例句：SELECT * FROM t1 WHERE column1 = (SELECT column1 FROM t2);
子查询是嵌套在外查询内部。而事实上它有可能在子查询内部再嵌套子查询。
子查询必须出现在圆括号之间。

行级子查询
  SELECT * FROM t1 WHERE (col1,col2) = (SELECT col3, col4 FROM t2 WHERE id = 10);
  SELECT * FROM t1 WHERE ROW(col1,col2) = (SELECT col3, col4 FROM t2 WHERE id = 10);
  行级子查询的返回结果最多为一行。

输入中文数据时，使用SET NAMES GBK 以防中文乱码
```
求某表表列得到的平均值四舍五入并以二位小数输出
SELECT ROUND(AVG(表列),2) AS ‘vg_price’ FROM 表;

```