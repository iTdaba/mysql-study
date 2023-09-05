# 学习mysql的第三天
>今天学习了表的一些操作
>
* ## 加载数据到表中
```mysql
load data local infile '文件路径' into table table_name;
```
* 遇到的问题  
  * LOAD DATA LOCAL INFILE file request rejected due to restrictions on access.数据导不进去->权限不够 解决方式：`在登陆时加上这样的语句：mysql -uroot -p --local-infile`
  * text文件中由于数据之间的空格导致最后导入数据库中的数据出现错误->**因为mysql读取数据时默认的分割符是制表符（tab字符  \t）**,解决方式
    * 改变原来text文件中数据之间的分割方式：改为tab键分割
    * 改变mysql读取数据时的分隔符 `load data local infile '文件路径' into table table_name fields terminated by ','`使用逗号作为字段分隔符 也可以使用其他数据分隔符
    * 还可以尝试预处理文本文件。例如，使用Python读取文本文件，并在加载到数据库之前清除或处理行之间的空行
* ## 选择表中特定行，特定列的数据
  代码格式
```mysql
SELECT what_to_select
FROM which_table
WHERE conditions_to_satisfy;
```

* 选择所有的数据
```
select * from table_name;
```
> 这种形式的SELECT使用*，这是“选择所有列”的简写。如果您想要查看整个表，例如，在加载了初始数据集之后，这是非常有用的
> 
+ 修改错误数据
  + 编辑文件pet.txt来纠正错误，然后清空表并使用DELETE和LOAD DATA重新加载它:
  ```mysql
  delete from table_name;
  load data local infile '文件路径' into table pet;
  ```
  + 使用UPDATE语句只修复错误的记录:
  ```mysql
  updata table_name set col1=xxx where col2=xxx;
  ```
>这个updata语句只改变错误数据，不需要重新加载数据

+ 选择特定的行
> 检索整个表格很容易，只要省略掉where关键词，但是在特定的情况下，我们不需要整个表的数据，所以我们就需要用where来对数据进行约束，从而得到我们想要的数据
```mysql
mysql> SELECT * FROM pet WHERE name = 'Bowser';
+--------+-------+---------+------+------------+------------+
| name   | owner | species | sex  | birth      | death      |
+--------+-------+---------+------+------------+------------+
| Bowser | Diane | dog     | m    | 1989-08-31 | 1995-07-29 |
+--------+-------+---------+------+------------+------------+
```
在这个语句中：我们限制了name为Bowser，所以也就只选择了Bowser这一行的数据
下面还有几个对数据进行限制的例子：
```mysql
mysql> SELECT * FROM pet WHERE birth >= '1998-1-1';
+----------+-------+---------+------+------------+-------+
| name     | owner | species | sex  | birth      | death |
+----------+-------+---------+------+------------+-------+
| Chirpy   | Gwen  | bird    | f    | 1998-09-11 | NULL  |
| Puffball | Diane | hamster | f    | 1999-03-30 | NULL  |
+----------+-------+---------+------+------------+-------+
```
```mysql
mysql> SELECT * FROM pet WHERE species = 'dog' AND sex = 'f';
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
```
```mysql
mysql> SELECT * FROM pet WHERE species = 'snake' OR species = 'bird';
+----------+-------+---------+------+------------+-------+
| name     | owner | species | sex  | birth      | death |
+----------+-------+---------+------+------------+-------+
| Chirpy   | Gwen  | bird    | f    | 1998-09-11 | NULL  |
| Whistler | Gwen  | bird    | NULL | 1997-12-09 | NULL  |
| Slim     | Benny | snake   | m    | 1996-04-29 | NULL  |
+----------+-------+---------+------+------------+-------+
```
```mysql
mysql> SELECT * FROM pet WHERE (species = 'cat' AND sex = 'm')
       OR (species = 'dog' AND sex = 'f');
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | NULL  |
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
```
> 注意：AND和OR可以混合使用，但AND的优先级高于OR。如果你同时使用两个操作符，最好使用括号来明确地指出条件应该如何分组

+ 选择特定的列
 ```mysql
mysql> SELECT name, birth FROM pet;
+----------+------------+
| name     | birth      |
+----------+------------+
| Fluffy   | 1993-02-04 |
| Claws    | 1994-03-17 |
| Buffy    | 1989-05-13 |
| Fang     | 1990-08-27 |
| Bowser   | 1989-08-31 |
| Chirpy   | 1998-09-11 |
| Whistler | 1997-12-09 |
| Slim     | 1996-04-29 |
| Puffball | 1999-03-30 |
+----------+------------+
 ```
注意，查询只是从每条记录中检索所有者列，其中一些记录出现了不止一次。为了最小化输出，只需通过添加关键字`DISTINCT`检索每个唯一的输出记录一次:
```mysql
mysql> SELECT DISTINCT owner FROM pet;
+--------+
| owner  |
+--------+
| Benny  |
| Diane  |
| Gwen   |
| Harold |
+--------+
```
可以使用WHERE子句将行选择与列选择结合起来。例如，要获取狗和猫的出生日期，使用以下查询:
```mysql
mysql> SELECT name, species, birth FROM pet
       WHERE species = 'dog' OR species = 'cat';
+--------+---------+------------+
| name   | species | birth      |
+--------+---------+------------+
| Fluffy | cat     | 1993-02-04 |
| Claws  | cat     | 1994-03-17 |
| Buffy  | dog     | 1989-05-13 |
| Fang   | dog     | 1990-08-27 |
| Bowser | dog     | 1989-08-31 |
+--------+---------+------------+
```
