---
title: mysql
date: 2017-03-31 23:36:41
tags:
    - Mysql
    - Database
categories:
    - Database
---

## 初涉Mysql

### MySQL概述
- MySQL由瑞典MySQL AB公司开发，目前属于Oracle公司.
- MySQL是一个开源的关系型数据库管理系统.
- MySQL分为社区版和企业版.

<!--more-->
### MySQL的安装与配置

### 启动和停止MySQL
- windows
    ```bash
    net start/stop mysql
    ```
- linux
    ```bash
    service mysql start/stop
    ```

### MySQL登录和退出
- MySQL登录

|参数|描述|
|:-:|:-:|
|-D, --database=name|打开指定数据库|
|--delimiter = name|指定分隔符|
|-h, --host=name|服务器名称|
|-p, --password[=name]|密码|
|-P, --port=#|端口号|
|--prompt=name|设置提示符|
|-u, --user=name|打开指定数据库|
|-V, --version|输出版本信息并且退出|

- MySQL退出
    - mysql > exit;
    - mysql > quit;
    - mysql > \q;

### 修改MySQL提示符
- MySQL提示符

|参数|描述|
|:-:|:-:|
|\D|完整的日期|
|\d|当前数据库|
|\h|服务器名称|
|\u|当前用户|

- 连接客户端时通过参数指定
    ```bash
    shell>mysql -uroot -proot --prompt 提示符
    ```

- 连接上客户端后，通过prompt命令修改
    ```bash
    mysql>prompt 提示符
    ```

### MySQL常用命令
- 显示当前服务器版本
    ```
    SELECT VERSION();
    ```

- 显示当前日期时间
    ```
    SELECT NOW();
    ```

- 显示当前用户
    ```
    SELECT USER();
    ```

> MySQL语句的规范
- 关键字和函数名称全部大写
- 数据库名称 表名称 字段名称全部小写
- SQL语句必须以分号结尾

### 操作数据库
- 创建数据库
    ```
    CREATE {DATABASE | SCHEMA} {IF NOT EXISTS} db_name [DEFAULT] CHARACTER SET [=] charset_name
    ```
- 查看当前服务器下的数据表列表
    ```
    SHOW {DATABASES | SCHEMAS} [LIKE 'pattern' | WHERE expr]
    ```
- 修改数据库
    ```
    ALTER {DATABASE | SCHEMA} [db_name] [DEFAULT] CHARACTER SET [=] charset_name
    ```
- 删除数据库
    ```
    DROP {DATABASE | SCHEMA} [IF EXISTS] db_name
    ```
## 数据类型和操作数据表

### 数据类型
> 数据类型是指列、存储过程参数、表达式和局部变量的数字特征，它决定了数据的存储格式，代表了不同的信息类型。

#### 整型
|数据类型|存储范围|字节|
|:-:|-----------|:-:|
|TINYINT|有符号值: -128到127(-2<sup>7</sup>到2^<sup>7</sup>-1)<br>无符号值: 0到255(0到2<sup>8</sup>-1)|1|
|SMALLINT|有符号值: -32768到32767(-2<sup>15</sup>到2^<sup>15</sup>-1)<br>无符号值: 0到65535(0到2<sup>16</sup>-1)|2|
|MEDIUMINT|有符号值: -83886088到8388607(-2<sup>23</sup>到2^<sup>23</sup>-1)<br>无符号值: 0到16777215(0到2<sup>24</sup>-1)|3|
|INT|有符号值: -2147483648到2147483648(-2<sup>31</sup>到2^<sup>31</sup>-1)<br>无符号值: 0到4294967295(0到2<sup>32</sup>-1)|4|
|BIGINT|有符号值: -9223373036854775808到9223373036854775807(-2<sup>63</sup>到2^<sup>63</sup>-1)<br>无符号值: 0到18446744073709551615(0到2<sup>64</sup>-1)|8|

#### 浮点型
|数据类型|存储范围|
|:-:|:----|
|FLOAT[(M,D)]|-3.402823466E+38到-1.175494351E-38; 0和1.175494351E-38到3.402823466E+38.<br>M是数字总位数, D是小数点后面的位数.如果M和D被省略, 根据硬件允许的限制来保存值.<br>单精度浮点数精确到大约7位小数位.|
|DOUBLE[(M,D)]|-1.7976931348623157E+308到-2.2250738585072014E-308; <br>0和2.2250738585072014E-308到1.7976931348623157E+308.|

#### 日期时间型
|列类型|存储需求|
|:-|:-|
|YEAR|1|
|TIME|3|
|DATE|3|
|DATETIME|8|
|TIMESTAMP|4|

#### 字符型
|列类型|存储需求|
|:-|----|
|CHAR(M)|M个字节, 0 <= M <= 255|
|VARCHAR(M)|L+1个字节, 其中L <= M且0 <= M <= 65535|
|TINYTEXT|L+1个字节, 其中L < 2<sup>8</sup>|
|TEXT|L+2个字节, 其中L < 2<sup>16</sup>|
|MEDIUMTEXT|L+3个字节, 其中L < 2<sup>24</sup>|
|LONGTEXT|L+4个字节, 其中L < 2<sup>32</sup>|
|ENUM('value1', 'value2', ...)|1或2个字节, 取决于枚举值的个数(最多65535个值)|
|SET('value1', 'value2', ...)|1,2,3,4或者8个字节, 取决于set成员的数目(最多64个成员)|

### 操作数据表

#### 概述
- 数据表(或称表)是数据库最重要的组成部分之一，是其他对象的基础。

#### USE
- 打开数据库
- USE 数据库名称;

#### 创建数据表
```sql
CREATE TABLE [IF NOT EXISTS] table_name (
    column_name data_type, 
    ...
)
```

#### 查看数据表列表
```sql
SHOW TABLES [FROM db_name]
[LIKE 'pattern' | WHERE expr]
```

#### 查看数据表结构
```sql
SHOW COLUMNS FROM tbl_name
```

#### 插入记录
```sql
INSERT [INTO] tbl_name [(col_name,...)] VALUES (val,...)
```

#### 记录查找
```sql
SELECT expr,... FROM tbl_name
```

#### 空值与非空
- NULL, 字段值可以为空
- NOT NULL, 字段值禁止为空

#### AUTO_INCREMENT
- 自动编号，且必须与主键组合使用
- 默认情况下，起始值为1，每次增量为1

#### PRIMARY KEY
- 主键约束
- 每张数据表只能存在一个主键
- 主键保证记录的唯一性
- 主键自动为NOT NULL

#### UNIQUE KEY
- 唯一约束
- 唯一约束可以保证记录的唯一性
- 唯一约束的字段可以为空值(NULL)
- 每张数据表可以存在多个唯一约束

#### DEFAULT
- 默认值
- 当插入记录时，如果没有明确为字段赋值，则自动赋予默认值。

## 约束以及修改数据表
### 约束
- 约束保证数据的完整性和一致性。
- 约束分为表级约束和列级约束。
- 约束类型包括:
    - NOT NULL(非空约束)
    - PRIMARY KEY(主键约束)
    - UNIQUE KEY(唯一约束)
    - DEFAULT(默认约束)
    - FOREIGIN KEY(外键约束)

#### FOREIGN KEY
- 保持数据一致性，完整性
- 实现一对一或一对多关系。

#### 外键约束的要求
- 父表和子表必须使用相同的存储引擎，而且禁止使用临时表。
- 数据表的存储引擎只能为InnoDB。
- 外键列和参照列必须具有相似的数据类型。其中数字的长度或是否有符号位必须相同;而字符的长度则可以不同。
- 外键列和参照列必须创建索引。如果外键列不存在索引的话，MySQL将自动创建索引。
#### 编辑数据表的默认存储引擎
MySQL配置文件
    `default-storage-engine=INNODB`


### 数据表的操作
## 操作数据表中的记录

## 子查询和连接

## 运算符和函数

## 自定义函数

## MySQL存储过程

## MySQL存储引擎

## MySQL图形化管理工具
