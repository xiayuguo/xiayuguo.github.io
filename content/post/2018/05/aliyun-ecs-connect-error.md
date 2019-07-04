---
title: 阿里云ECS无法访问
date: 2018-05-23T21:32:30+08:00
draft: false
comments: true
tags: 
- Linux
---

> 今天有一朋友让我帮忙看下他的云服务器，他说服务器无法远程访问，即使重启了也不行。说来挺惭愧，我也搞了半天没查出来问题在哪！于是我帮他申请工单，没想到阿里云工程师反馈很快，给了他最大权限，三下五除二，问题被解决了。


```
工程师 XXXXX 号: 因为您服务器的网络是专有网络，专有网络服务器的 公网ip 是通过 nat 映射到 内网网卡的，所以您服务器内是无法直接监听 公网ip 的。还请您知晓，谢谢  
```

```
服务器sshd服务无法启动，报错: error: Bind to port 22 on 101.132.36.203 failed: Cannot assign requested address. fatal: Cannot bind any address.
```

其实他说的这个原因，我也查到了，但是我误解了`assign`的意思。`assign` 此处的含义是“指定”, 而不是“分配”。

解决方案: 
编辑 `/etc/ssh/sshd_config` 注释 `#ListenAddress 101.132.36.203`

