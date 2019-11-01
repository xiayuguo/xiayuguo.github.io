---
title: Too Many Open Files
date: 2019-07-03T22:28:57+08:00
draft: true
comments: false
tags: 
- Linux
---

![](https://hugo-1256107396.cos.ap-chengdu.myqcloud.com/blog/2019/07/pexels-photo-1893264.jpeg)

> 今天同事定位线上问题: 图片意外失踪 ！！！
> 
> 通过查阅日志时发现了它，*Too many open files* 又一次出现在我的视野里，我记不清上一次它是怎么蹦出来？以及我是怎么把它消灭的？ 但是这次我要将它连根拔起。


```
查看所有进程的文件打开数
lsof |wc -l

查看某个进程打开的文件数
lsof -p pid |wc -l
```


