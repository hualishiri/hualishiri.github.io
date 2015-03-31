title: MYSQL
date: 2014-12-16 18:34:26
categories:
- 常用工具
tags:
---
## 1.连接MYSQL
`mysql -uroot -p`		使用密码登录mysql数据库
`mysql -h localhost -uroot -p 123`	连接远程服务器

## 2.修改密码
`mysqladmin -uroot -password ab12`	给root加个密码
`mysqladmin -uroot -p ab12 -password abce`	将root密码改成abce
`update user set password=password('') weher user='root';` 修改user表修改器名称
`flush privileges;`	强制刷新权限内存控制

## 3.增加新用户
`grant select on 数据库.* to 用户名@登录主机 identified by \"密码"`
`grant select,insert,update,delete on *.* to test1@\"%\" identified by \"abc\"` 新增用户;

## 4.显示命令
`show databases;`	显示数据库
`use mysql;`	使用数据库
`show tables;`	显示数据库中的表
`describe 表名;`	显示表的描述
`create database 库名;`	创建数据库
`use 库名;`	使用数据库;
`create table 表名;` 创建表

`drop database 库名;`	删除数据库
`drop table 表名;`	删除表

`delete from 表名;`	将表中的记录清空
`select * from 表名;`	查询表

## 5.备份数据库
`mysqldump --opt school>school.bbb`	备份数据库到school.bbb文件中
`msyqldump -uroot -p21center meeting > db_meeting.sql` 备份数据库
`mysqldump -uroot -p21center meeting < xxx.sql` 	恢复数据库

##6.执行sql语句
`mysql -uroot -p < /home/db_meeting.sql` 	//.sql为大量的sql语句

## 7.全局管理权限
`FILE` 在MySQL服务器上读写文件。
`PROCESS` 显示或杀死属于其它用户的服务线程。
`RELOAD` 重载访问控制表，刷新日志等。
`SHUTDOWN` 关闭MySQL服务。
数据库/数据表/数据列权限：
`ALTER` 修改已存在的数据表(例如增加/删除列)和索引。
`CREATE` 建立新的数据库或数据表。
`DELETE` 删除表的记录。
`DROP` 删除数据表或数据库。
`INDEX` 建立或删除索引。
`INSERT` 增加表的记录。
`SELECT` 显示/搜索表的记录。
`UPDATE` 修改表中已存在的记录。
特别的权限：
`ALL` 允许做任何事(和root一样)。
`USAGE` 只允许登录–其它什么也不允许做。

