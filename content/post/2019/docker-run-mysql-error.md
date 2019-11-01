---
title: "Cannot open shared object file: Permission denied"
date: 2019-01-09T20:42:10+08:00
draft: false
tags:
- Docker
- Mysql
- Ubuntu
---

> 我想通过 `docker` 来运行 `mysql` 实例, 网上教程一大堆，结果以为很简单的事，搞了大半天, 记录下来，希望能帮到大家。

### 环境
- Ubuntu 14.04
- Docker 18.09
- Mysql 5.7

### 问题
```
root@cloud:~# docker run --name mysql -d -e MYSQL_ROOT_PASSWORD=12345 mysql:5.7
11637add4a41b827a9691fe6e77fba13ae63319d74713c64ef2e92276df6583f
root@cloud:~# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
root@cloud:~# docker logs mysql
ERROR: mysqld failed while attempting to check config
command was: "mysqld --verbose --help --log-bin-index=/tmp/tmp.m22w4ZgLhP"

mysqld: error while loading shared libraries: libpthread.so.0: cannot open shared object file: Permission denied
```

### 解决方案

解决过程中, 尝试各种以 `权限` 为中心的解决办法，均无效，有的网站上把 `/usr/bin` 的权限放开后就可以了，但是本人尝试无效。大家也可以试试。

**终极方案: 升级 `Ubuntu` 系统到 `16.04`**, 具体升级方法，大家可以参考这篇[文章](https://www.digitalocean.com/community/tutorials/how-to-upgrade-to-ubuntu-16-04-lts), 写的很细。

