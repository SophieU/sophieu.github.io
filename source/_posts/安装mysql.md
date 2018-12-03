---
title: windows下安装mysql
date: 2018-11-23 14:07:09
tag: mysql
categories: 后端学习
---
最近在用node做试验项目，需要用到数据库，于是记录下mysql的安装使用过程。
- 参考文章：[菜鸟教程](http://www.runoob.com/mysql/mysql-install.html)
<!--more-->
## 下载
- [官网下载地址](https://dev.mysql.com/downloads/mysql/)
- 选择windows平台以及适合的版本，大多数用的是x64的。
- 下载时，它会提示你登录或者注册，选择下面的一行小字“No thanks,just start my download”就好
<img src="http://www.runoob.com/wp-content/uploads/2014/03/330405-20160709174941374-1821908969.png" alt=""/>

## 安装
#### 解压
1. 选择你想要安装mysql的目录，如：`D:\Program Files\mysql-8.0.13`

#### 设置配置文件
1. 在安装目录新建`mysql.ini`，并编辑配置文件
```bash
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=D:\Program Files\mysql-8.0.13
# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
# datadir=C:\\web\\sqldata
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

#### 安装mysql服务
1. 以管理员身份打开`CMD`
```bash
$ cd bin
# 安装
$ mysqld install

# 安装成功后提示
Service successfully installed.
```

#### 初始化mysql
```bash
# 初始化命令
$ mysqld --initialize --console
# 完成后提示
2018-11-23T06:15:33.870622Z 0 [System] [MY-013169] [Server] D:\Program Files\mysql-8.0.13\bin\mysqld.exe (mysqld 8.0.13) initializing of server in progress as process 11908
2018-11-23T06:15:34.044783Z 0 [Warning] [MY-013242] [Server] --character-set-server: 'utf8' is currently an alias for the character set UTF8MB3, but will be an alias for UTF8MB4 in a future release. Please consider using UTF8MB4 in order to be unambiguous.
2018-11-23T06:16:03.042791Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: Ei&itp1s2C:h
```
- 注意以上完成后提示中`Ei&itp1s2C:h`就是初始密码，后续登录需要用到，你也可以在登陆后修改密码。

#### 首次登录mysql
```bash
# -u 用户名，-p密码
$ mysql -u root -p
# 首次登录，初始密码即为之前安装时的密码
Enter password: 

# 登录成功后你将会看到 Welecome to the MySQL monitor... 的提示语。
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.13

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

# 退出
mysql> exit;

```
- 退出mysql命令：`exit;`或`quit;` 

#### 重置密码
- 初次登录使用的是mysql生成的初始密码，以后的操作，mysql会提示：`ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.`
```bash
# 重置密码
alter user root@localhost identified by '123456';
```
- 注意：MySQL的语句都需要以`;`结束




## MySQL管理
#### 启动及关闭MySQL服务器
- 以windows系统下为例:
- 进入到mysql安装目录中的bin文件下
```bash
# 启动
mysqld --console
# 或者
net start mysql

# 关闭：
mysqladmin -uroot shutdown
# 或者：
net stop mysql
```
#### MySQL用户设置
如果你需要添加 MySQL 用户，你只需要在 mysql 数据库中的 user 表添加新用户即可。

以下为添加用户的的实例，用户名为guest，密码为guest123，并授权用户可进行 SELECT, INSERT 和 UPDATE操作权限：
```bash
mysql> use mysql;
Database changed
# 新增guest用户
mysql> create user 'guest'@'localhost' identified by '123456';
# 授予权限
mysql> grant all on *.* to 'guest'@'localhost' ;
# 刷新权限
flush privileges;
# 退出重登
exit;
# 重登录
mysql -utest -p
password:****
```

## MySQL常用命令
命令|功能
---|---
create database <数据库名>|创建数据库
show databases;|显示数据库
drop database <数据库名>;|删除数据库
drop database if exists drop_database;|如果存在就删除
 use <数据库名>|使用数据库
create table <表名> ( <字段名1> <类型1> [,..<字段名n> <类型n>]);|创建表
drop table <表名>|删除表
insert into <表名> [( <字段名1>[,..<字段名n > ])] values ( 值1 )[, ( 值n )]|插入表
 select <字段1，字段2，...> from < 表名 > where < 表达式 >|查询所有行
 mysql> select * from MyClass order by id limit 0,2;|查询前几行数据
 delete from 表名 where 表达式|删除数据
 update 表名 set 字段=新值,… where 条件|更新数据
 ：alter table 表名 add 字段 类型 其他; |增加字段
 rename table 原表名 to 新表名;|修改表名

 #### 备份数据库
 命令在DOS的mysql安装目录下执行

1. 导出整个数据库
```bash
# 导出文件默认是存在mysql\bin目录下
    mysqldump -u 用户名 -p 数据库名 > 导出的文件名
    mysqldump -u user_name -p123456 database_name > outfile_name.sql
```
2. 导出一个表
```bash
  mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名
  mysqldump -u user_name -p database_name table_name > outfile_name.sql
```
  

3.导出一个数据库结构
```bash
mysqldump -u user_name -p -d –add-drop-table database_name > outfile_name.sql
```
* `-d` 没有数据 `–add-drop-table` 在每个create语句之前增加一个drop table

    

4.带语言参数导出
```bash
mysqldump -uroot -p –default-character-set=latin1 –set-charset=gbk –skip-opt database_name > outfile_name.sql

# 例如，将aaa库备份到文件back_aaa中：
　　[root@test1 root]# cd　/home/data/mysql
　　[root@test1 mysql]# mysqldump -u root -p --opt aaa > back_aaa
```

#### 一个建库和建表实例
```bash
create database school; //建立库SCHOOL
use school; //打开库SCHOOL
create table teacher //建立表TEACHER
(
    id int(3) auto_increment not null primary key,
    name char(10) not null,
    address varchar(50) default ‘深圳’,
    year date
); //建表结束

//以下为插入字段
insert into teacher values(”,’allen’,'成都一中’,'1976-10-10′);
insert into teacher values(”,’jack’,'成都二中’,'1975-12-23′);
```
* 如果你在mysql提示符键入上面的命令也可以，但不方便调试。
1. 你可以将以上命令原样写入一个文本文件中，假设为school.sql，然后复制到c:\\下，并在DOS状态进入mysql安装目录，然后键入以下命令：
    `mysql -uroot -p密码 < c:\\school.sql`
如果成功，空出一行无任何显示；如有错误，会有提示。（以上命令已经调试，你只要将//的注释去掉即可使用）。

2. 或者进入命令行后使用` mysql> source c:\\school.sql;` 也可以将school.sql文件导入数据库中。

