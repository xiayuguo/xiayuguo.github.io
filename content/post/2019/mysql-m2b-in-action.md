---
title: "Mysql M2B in Action with Docker"
date: 2019-01-11T18:05:10+08:00
draft: false
tags:
- Docker
- Mysql
---


> M2B: 主主备份(DIY, 哈哈)。最近和数据库备份杠上了，碍于手上没有足够的服务器，于是在一个服务器上使用 Docker 运行两个 Mysql 实例, 下面是主主备份的具体流程, 每一步都不能跳过，请仔细阅读。


### 1. 运行两个 mysql 实例: mysql-1 和 mysql-2

> 确保两台服务器上有相同的数据。

```
root@cloud:~# docker run -p 10028:3306 --name mysql-1 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
root@cloud:~# docker run -p 10029:3306 --name mysql-2 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```

### 2. 两个实例分别修改配置文件和创建复制账号
> 启用二进制日志，选择唯一的服务器ID，并创建复制账号

```
# mysql-1
# 进入实例 mysql-1
root@cloud:~# docker exec -it mysql-1 bash
# 修改配置文件 my.cnf
root@343cef5a6f96:/# vim /etc/mysql/my.cnf
[mysqld]
server-id                = 128
log_bin                  = mysql-bin
relay-log                = mysql-relay-bin
# replicate-wild-do-table=scheme.%
# replicate-wild-ignore-table=schema.%
log-slave-updates        = on
slave-skip-errors        = all
auto-increment-offset    = 1
auto-increment-increment = 2
binlog_format            = mixed
expire_logs_days         = 10
max_binlog_size          = 100

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/

# mysql-2
# 进入实例 mysql-2
root@cloud:~# docker exec -it mysql-2 bash
# 修改配置文件 my.cnf
root@343cef5a6f96:/# vim /etc/mysql/my.cnf
[mysqld]
server-id                = 129
log_bin                  = mysql-bin
relay-log                = mysql-relay-bin
# replicate-wild-do-table=scheme.%
# replicate-wild-ignore-table=schema.%
log-slave-updates        = on
slave-skip-errors        = all
auto-increment-offset    = 2
auto-increment-increment = 2
binlog_format            = mixed
expire_logs_days         = 10
max_binlog_size          =

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/

# 退出容器, 重启两个 mysql 实例
root@cloud:~# docker retart mysql-1 mysql-2
```



|参数   | 描述   |
|:----|:----|
| server-id   | 必须唯一, 建议使用服务器IP   | 
| log_bin   | 开启二进制日志功能，mysql-bin是命名格式，会生成文件名为mysql-bin.000001、mysql-bin.000002等日志文件   | 
| relay-log   | 作为从服务器时的中继日志   | 
| replicate-wild-do-table=scheme.%     | 限制slave只同步那些匹配指定模式的数据表  | 
| replicate-wild-ignore-table=scheme.%     | 是复制过滤选项，可以过滤不需要复制同步的数据库或表，如“scheme.%”指不复制MySQL库下的所有表，依此类推，多个数据库就多写几行   | 
| log-slave-updates   | slave 将复制事件写进自己的二进制日志   | 
| slave-skip-errors   | 跳过主从复制中遇到的所有错误或指定类型的错误,避免 slave 端复制中断   | 
| auto-increment-offset=1   | 主键自增规则，自增偏移（从1开始），单数   | 
| auto-increment-increment=2   | 主键自增规则，自增因子（每次加2）   | 
| binlog_format   | 主从复制的格式(mixed,statement,row,默认格式是 statement)   | 
| expire_logs_days = 10   | expire_logs_days = 10日志保存天数：10天   | 

**创建复制账号并授权**
```
# mysql-1 创建复制账号
root@cloud:~# mysql -uroot -h127.0.0.1 -p123456 -P10028
mysql> GRANT REPLICATION SLAVE,REPLICATION CLIENT ON *.* TO repl@'%' IDENTIFIED BY '123456';
Query OK, 0 rows affected, 1 warning (0.01 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

# mysql-2 创建复制账号
root@cloud:~# mysql -uroot -h127.0.0.1 -p123456 -P10029
mysql> GRANT REPLICATION SLAVE,REPLICATION CLIENT ON *.* TO repl@'%' IDENTIFIED BY '123456';
Query OK, 0 rows affected, 1 warning (0.01 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```
### 3.将每个主库设置为对方的备库，使用新创建的二进制日志开始工作

> 将每个主库设置为对方的备库，使用新创建的二进制日志开始工作

```
# mysql-2 增加 mysql-1 
root@cloud:~# mysql -uroot -h127.0.0.1 -p123456 -P10028
mysql> show master status;
+------------------+----------+
| File             | Position | 
+------------------+----------+
| mysql-bin.000001 |      1080 |
1 row in set (0.00 sec)

root@cloud:~# mysql -uroot -h127.0.0.1 -p123456 -P10029
mysql> CHANGE MASTER TO MASTER_HOST='Your_Server_IP',MASTER_USER='repl', MASTER_PASSWORD='123456', MASTER_PORT=10028, MASTER_LOG_FILE='mysql-bin.00001', MASTER_LOG_POS=1080;
Query OK, 0 rows affected, 1 warning (0.03 sec)
mysql> show master status;
+------------------+----------+
| File             | Position | 
+------------------+----------+
| mysql-bin.000001 |      154 |
1 row in set (0.00 sec)

root@cloud:~# mysql -uroot -h127.0.0.1 -p123456 -P10028
mysql> CHANGE MASTER TO MASTER_HOST='Your_Server_IP',MASTER_USER='repl', MASTER_PASSWORD='123456', MASTER_PORT=10029, MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=154;
Query OK, 0 rows affected, 1 warning (0.03 sec)

mysql> start slave;
Query OK, 0 rows affected (0.00 sec)

mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: Your_Server_IP
                  Master_User: repl
                  Master_Port: 10028
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 2451
               Relay_Log_File: mysql-relay-bin.000002
                Relay_Log_Pos: 1234
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes

# 看到 Slave_IO_Running 和 Slave_SQL_Running 均为 Yes 表明配置成功
```

### 4. 最后验证主主备份是否成功
```
# mysql-1 上创建 schema test table sync
root@cloud:~# mysql -h127.0.0.1 -p123456 -P10028
mysql> CREATE DATABASE test;
mysql> USE test;
mysql> CREATE TABLE `test`.`sync` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(45) NULL COMMENT '名称',
  `remarks` VARCHAR(45) NULL COMMENT '备注',
  PRIMARY KEY (`id`))
ENGINE = InnoDB
DEFAULT CHARACTER SET = utf8;
Query OK, 0 rows affected (0.01 sec)
# 插入一条数据
mysql> insert into sync (`name`, `remarks`) values ('cloud', '平台');
Query OK, 1 row affected (0.00 sec)

# mysql-2 上查询记录和再新增两天记录，验证 id 是否按规则生成
root@cloud:~# mysql -h127.0.0.1 -p123456 -P10029
mysql> USE test;
mysql> select * from sync;
+----+-------+---------+
| id | name  | remarks |
+----+-------+---------+
|  1 | cloud | 平台    |
+----+-------+---------+
1 row in set (0.00 sec)

mysql> insert into sync (`name`, `remarks`) values ('a', 'A');
Query OK, 1 row affected (0.00 sec)

mysql> insert into sync (`name`, `remarks`) values ('b', 'B');
Query OK, 1 row affected (0.01 sec)

mysql> select * from sync;
+----+-------+---------+
| id | name  | remarks |
+----+-------+---------+
|  1 | cloud | 平台    |
|  2 | a     | A       |
|  4 | b     | B       |
+----+-------+---------+
3 rows in set (0.01 sec)

mysql> 此处应该有掌声！！！
```

注: mysql 的镜像中没有 vim , 遇到 vim 不存在时， 需要安装一下
```
apt-get update && apt-get install vim -y
```