---
title: Change Redis Password
date: 2019-07-22T18:36:32+08:00
draft: false
comments: true
tags: 
- Redis
---

# Ubuntu
## 修改配置文件 `/etc/redis/redis.conf`
```
requirepass 你的密码
```
## 重启 redis 服务
```
service redis restart
```

# Win10
## 修改配置文件 `C:\Program Files\Redis\redis.windows-service.conf`
```
requirepass 你的密码
```
## 重启 redis 服务
```
net stop redis
net start redis
```
注: 这两个操作都需要以管理员身份权限

# 实时生效(免重启)
## 启动 redis 客户端 `redis-cli`
```
127.0.0.1:6379> config set requirepass 你的密码
```
注: 这种方式确实是实时生效，但是一旦重启后，这种配置就丢失了，以配置文件为准, 因为它并没有被持久化到文件中。

# 参考文档
1. [set a password for redis](https://stackoverflow.com/questions/7537905/redis-set-a-password-for-redis)
2. [莫博客](https://www.cnblogs.com/sheepswallow/p/4856751.html)

