# 一些mysql的基本操作
## 1. 显示所有的数据库
```
show databases;
```
这样的操作会显示出所有的数据库，如下：
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```
`information_schema`：这是一个特殊的数据库，它包含了关于其他数据库、表、列和其他数据库对象的元数据信息。这些信息可以用于查询和获取关于数据库对象的详细信息。

`mysql`：这是 MySQL 数据库的核心数据库，它包含了 MySQL 服务器使用的系统级别的元数据信息，例如用户账户、权限信息、数据库配置等。

`performance_schema`：这是一个用于监控 MySQL 服务器性能的数据库。它提供了有关查询执行、锁定和线程活动的性能统计信息。

`sys`：这是一个用于系统监控和诊断的数据库，通常在 MySQL 5.7 及更高版本中可用。它提供了有关查询性能、系统资源和锁定的详细信息。
## 2.新建一个数据库
```
create database test;
```
此处的test就是新建的**数据库名**
现在我们在显示一下所有的数据库，看看变成了什么样子：
```
show databases;
```
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
5 rows in set (0.00 sec)
```
可以看出：新建的数据库已经添加成功了
