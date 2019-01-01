---
title: MySQL的limit用法和分页查询的性能优化
date: 2017-04-27 15:16:27
tags:
- Mysql
---

> 学习任何东西都可以按照3W的框架进行，容器技术也是一样，先回答 What、Why 和 How 这三个问题。

CloudMan-[每天5分钟玩转容器技术（6）](http://www.cnblogs.com/CloudMan6/p/6751516.html)


> 我非常赞同CloudMan的观点, 下面咱就以这三个问题来讨论今天的主题

# What-Mysql中的LIMIT是干啥的?

在我们使用查询语句的时候，经常要返回前几条或者中间某几行数据，这个时候怎么办呢？不用担心，mysql已经为我们提供了这样一个功能。

```sql
SELECT * FROM table LIMIT [offset,] rows | rows OFFSET offset
```
LIMIT 子句可以被用于强制 SELECT 语句返回指定的记录数。LIMIT 接受一个或两个数字参数。如果给定一个参数(rows), 偏移量offset默认为0, 指定返回[:rows]的数据; 如果给定两个参数(offset, rows)，offset指定第一个返回记录行的偏移量，rows指定返回记录行的最大数目。
```bash
mysql> SELECT * FROM table LIMIT 5,10; // 检索记录行 6-15 
```
获得开头的几行数据
```bash
mysql> SELECT * FROM table LIMIT 10; // 检索记录行 0-9
```
获得结尾的几行数据
```bash
mysql> SELECT * FROM table LIMIT 100,-1; // 检索记录行 100-last.
```

# Why-为什么需要为分页查询做优化?
+ 随着数据量的增加，页数会越来越多，查看后几页的SQL就可能类似下面示例。总而言之，越往后分页，LIMIT语句的偏移量(offset)就会越大，速度也会明显变
+ 随着业务的复杂度越来越高, 查询条件不断增多, 可想而知, 速度也会越来越慢
```sql
SELECT * FROM users WHERE age = 12 ORDER BY id LIMIT 10000, 20
```

# How-分页查询如何优化?

- 提速原理:
避免全表扫描,而是通过索引找到指定位置,于是就相当于只是扫描了指定数量(rows)的数据

- 实现方式:
    
    - 子查询分页方式
    ```
    SELECT * FROM users WHERE  id >=  
    (SELECT id FROM users  WHERE age = 12 ORDER BY id LIMIT 10000, 1) LIMIT 20
    ```

    - JOIN分页方式
    ```
    暂缺示例
    ```
