---
title: Export Mysql Table to Excel
date: 2019-07-01T12:13:24+08:00
draft: false
comments: false
tags: 
- Mysql
---


> 今天有同事问我怎么把`Mysql`某个表中的数据导出到`Excel`中？本以为`so easy`的事，结果踩了很多坑，记录下。

## 方法一: 使用`MySQL Workbench`导出`CSV`
`MySQL Workbench` 这个客户端软件是我一直使用的一款 MySQL 客户端, 超级强大。自然也少不了导入和导出的功能。具体导出的步骤请移步至[官方文档](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-table.html)(图文并茂, 美滴很)

在准备享受胜利果实的时候，使用`Excel`打开导出的`CSV`文件，我去，中文全都是乱码，怎么办呢？

甭捉急，两步搞定乱码问题:

1. 使用记事本打开上述的CSV文件
2. 文件 => 另存为 => 选中编码为`ANSI` => 最后覆盖原文件，就好了

## 方法二: 使用原生`SQL`导出`CSV`
万变不离其宗，`SQL` 是一种更加原始粗暴的方案，但是更加实用，即使再花哨的工具，最后归根结底还是会回到这个位置。

### 语法
```
SELECT * FROM 表名
INTO OUTFILE '输出的文件位置'
FIELDS TERMINATED BY '字段分隔符(,)'
ENCLOSED BY '字段包围符(")'
LINES TERMINATED BY '行间分隔符(\n)';
```

理想很丰满，现实很骨感。有了语法，执行时又遇到问题了。
#### 问题一: secure-file-priv选项问题
```
ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement
```
使用的`MySQL`版本`5.7`会遇到`--secure-file-priv`这个选项问题，`5.6`不会。

- secure-file-priv 选项值解释

|属性值|解释|
|:--|:--|
|NULL|限制mysqld不允许导入或导出|
|/tmp|限制mysqld只能在/tmp目录中执行导入导出，其他目录不能执行|
|无|不限制mysqld在任意目录的导入导出|

通过`show global variables like '%secure_file_priv%';`查询到我的配置属于限定目录导出，于是导出时指定到那个目录下就好了。


#### 问题二: 没有表头
没有表头，工具类导出做了此项优化，一般不存在这个问题。
```
/* Add column headers */
SELECT 'A', 'B', 'C'

UNION ALL

/* Now the actual query */
SELECT a, b, c 

FROM `TABLENAME`

/* Save the query results to a file */
INTO OUTFILE '/tmp/orders.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```
怎么做到的呢？通过`UNION ALL`将自定义表头和结果集合并。


## 反思
永远不要我以为，我以为的就是我以为的吗？





