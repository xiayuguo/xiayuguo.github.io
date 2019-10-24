---
title: Tar压缩命令小结
date: 2019-07-02T18:40:09+08:00
draft: false
comments: true
tags: 
- Tool
---

> 今天遇到一个麻烦，从生产环境备份的一个`SQL`脚本，体积是`705M`，希望下载到本地做测试，由于网络不稳定、带宽有限等问题，无法下载成功，于是怎么办？压缩！

```
root@localhost:/home/test# ls -lh
total 705M
-rw-r--r-- 1 root root 705M 7月   2 09:48 bf_2019_07-02.sql
```

我们先来回顾下压缩/解压缩相关常用命令

## Tar
- `-c` create 简写, 创建压缩
- `-x` extract 简写, 提取压缩
- `-f` 生成的文件名
- `-z` 以gzip方式压缩
- `-j` 以bzip2方式压缩
- `-J` 以xz方式压缩
- `-t` list 简写, 列出压缩的内容
- `-v` verbose 简写, 详细列出已处理的文件

### 示例

- Tar 压缩
```
tar -czf a.tar.gz a.sql
tar -cjf a.tar.bz2 a.sql
tar -cJf a.tar.xz a.sql
```

- Tar 解压缩
```
tar -xzf a.tar.gz
tar -xjf a.tar.bz2
tar -xJf a.tar.xz
```

- Tar 解压指定文件
1. 查找指定文件是否存在
```
tar -ztvf a.tar.gz | grep "keyword"
```
2. 解压时增加完整文件名(含路径)
```
tar -zxvf a.tar.gz a/keyword
```

### 压缩算法效率对比
```
root@localhost:/home/test# time tar czf backup.tar.gz bf_2019_07-02.sql
real    0m36.603s
user    0m35.226s
sys     0m4.191s
root@localhost:/home/test# time tar cjf backup.tar.bz2 bf_2019_07-02.sql
real    7m57.828s
user    7m55.994s
sys     0m4.707s
root@localhost:/home/test# time tar cJf backup.tar.xz bf_2019_07-02.sql
real    17m22.529s
user    17m19.340s
sys     0m24.694s
root@localhost:/home/test# ls -lh
total 875M
-rw-r--r-- 1 root root  54M 7月   2 16:08 backup.tar.bz2
-rw-r--r-- 1 root root  69M 7月   2 15:59 backup.tar.gz
-rw-r--r-- 1 root root  49M 7月   2 18:19 backup.tar.xz
-rw-r--r-- 1 root root 705M 7月   2 09:48 bf_2019_07-02.sql
```

|压缩方式|压缩耗时|压缩后大小(M)|
|:--|:--|:--|
|gzip|36.603s|69|
|bz2|7m57.828s|54|
|xz|17m22.529s|49|

总体来说: gzip性价比最高，bz2和xz可以把文件压得更小，但是耗时太长。

### 问题小结
#### 问题一：`tar: Removing leading '/' from member names`
##### 原因: tar 在压缩文件时，默认会取相对路径，无法处理绝对路径，需要使用参数`P`来指定。
通过`tar --help|grep leading`，可以查到下面的提示信息：
```
-P, --absolute-names       don't strip leading '/'s from file names
    --strip-components=NUMBER   strip NUMBER leading components from file

```
##### 示例：
```
tar czf target.tgz -P /a/b/source
```