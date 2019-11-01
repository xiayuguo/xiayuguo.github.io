---
title: Change Mysql Password
date: 2019-07-17T19:02:58+08:00
draft: false
comments: true
tags: 
- Mysql
---

> 去年的这个夏天，不像今年雨水很多，天气微热，朋友打电话过来说: “他的数据库被人黑了，对方扬言必须给某某账号下转xxx比特币，不然就删库”。修改数据库密码有三种常见方式：`mysqladmin`和`sql`。

修改数据库密码前，我们先来看下数据库里存储的用户和密码信息，此处示例中我使用的密码是`666`, 要修改为`999`。
```
mysql> select host,user,password from mysql.user where user="root";
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
| hugo      | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
| 127.0.0.1 | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
| ::1       | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
| %         | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
+-----------+------+-------------------------------------------+
5 rows in set (0.00 sec)

```


# 方式一
```
mysqladmin -uroot -p666 flush-privileges password "999"
```
可以通过下面的方式来验证：
```
root@hugo:~# mysqladmin -uroot -p999 ping
mysqld is alive
```
修改成功
```
mysql> select host,user,password from mysql.user where user="root";
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | root | *627B3E4116939F447D767EECFB048F52DFBF73A7 |
| hugo      | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
| 127.0.0.1 | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
| ::1       | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
| %         | root | *007D50CA06F69776D307B1BEC71CD73D0EA0999C |
+-----------+------+-------------------------------------------+
5 rows in set (0.00 sec)
```
这种方式因为没有指定`-h`参数，所以默认修改了`host=localhost`的那一行, 并不彻底。


# 方式二
```
mysql -uroot -p666 -e 'UPDATE mysql.user SET Password=PASSWORD('999') where user="root";flush privileges;'
```
同样我们来看下是否修改成功？
```
root@hugo:~# mysqladmin -uroot -p999 ping
mysqld is alive
```
没毛病, 修改成功
```
mysql> select host,user,password from mysql.user where user="root";
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | root | *627B3E4116939F447D767EECFB048F52DFBF73A7 |
| hugo      | root | *627B3E4116939F447D767EECFB048F52DFBF73A7 |
| 127.0.0.1 | root | *627B3E4116939F447D767EECFB048F52DFBF73A7 |
| ::1       | root | *627B3E4116939F447D767EECFB048F52DFBF73A7 |
| %         | root | *627B3E4116939F447D767EECFB048F52DFBF73A7 |
+-----------+------+-------------------------------------------+
5 rows in set (0.00 sec)
```
很明显，这种方式更加彻底，因为是通过`sql`的方式。

# 方式三
有时候我们并不知道原密码，怎么办呢？

参照网友给的[方法](https://blog.bihe0832.com/mysql-modify-root.html):

以安全模式启动mysql，可以直接以root身份登录，然后重设密码。下面是具体步骤(版本5.7.X)

停掉在运行的MySQL服务：
```
sudo service mysql stop
```
以安全模式启动mysql：
```
sudo mysqld_safe --skip-grant-tables --skip-networking &
```
直接用root登录，无需密码：
```
mysql -uroot
```
重设密码：
```
mysql> use mysql;
mysql> update user set authentication_string=password('password') where user='root';
mysql> flush privileges;
```
退出mysql
```
mysql > quit
```
重启mysql
```
sudo service mysql restart
```
