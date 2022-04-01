# 介绍

[MySql](https://www.mysql.com/) 是一个关系型数据库管理系统。



## 常见问题

### 1、Navicat 连接 MySQL 8 出现 2059 错误

##### 错误：

使用Navicat Premium 连接MySQL时出现如下错误：

![2059](../..//images/20190408093327.png)

##### 原因：

`mysql 8` 之前的版本中加密规则是 `mysql_native_password` ，而在 `mysql 8` 之后，加密规则是 `caching_sha2_password` 。

##### 解决：

更改加密规则

```mysql
$ mysql -uroot -ppassword #登录

> use mysql; #选择数据库

> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER; #更改加密方式 localhost 可以是 %

> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password'; #更新用户密码 localhost 可以是 %

> FLUSH PRIVILEGES; #刷新权限
```



### 2、Mysql 数据表中删除重复的数据，并保留其中一条数据

执行 `sql` ：

```mysql
DELETE FROM `qimen_deliveryorder` WHERE `id` IN (
	SELECT a.`id`,`trade_id`  FROM (
    	SELECT id,trade_id,COUNT(*) as c   FROM `qimen_deliveryorder` GROUP BY `trade_id` HAVING COUNT(*) >= 2 ORDER BY c DESC
    ) as a 
)
```



### 3、 `Mysql binlog` 日志

```shell
/www/server/mysql/bin/mysqlbinlog --start-datetime="2021-01-14 01:00:00" --stop-datetime="2021-01-14 14:00:00" /www/server/data/mysql-bin.000084 -r afind1.sql
```

