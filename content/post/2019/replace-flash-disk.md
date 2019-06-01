---
title: 还在找U盘？别找了，我教你一招！
date: 2019-06-01T23:22:26+08:00
draft: false
comments: true
tags: 
- Tool
- Python
---

![flask disk](https://hugo-1256107396.cos.ap-chengdu.myqcloud.com/blog/2019/06/uflashdisk.webp?q-sign-algorithm=sha1&q-ak=AKIDU1piBG6dEhJE1i0nSW7PKxZU1PFe0GXq&q-sign-time=1559382903;1559386503&q-key-time=1559382903;1559386503&q-header-list=&q-url-param-list=&q-signature=845a2edf8a8524f36adea6c59da5ff4ae7540f98&x-cos-security-token=d1eda615123ee5845a52f41e96457e22dfdf3d0e10001)
最近有同事问我，有没有U盘，她要给别人拷下文件，或者是从别人那拷贝下文件。

这些文件普遍有个特点就是体积大（超出微信客户端中的上限 100M）

办公室里这种小事时常发生，时间就在指缝间悄悄流逝。

作为一名 “懒人”，这种事要是发生在身上，我会这么解决：

# 准备工作
- 发送方和接收方在一个局域网内
- 发送方的 PC 上有 `python` 环境

# 开始我的表演
> 以 Win10 系统演示，其他系统方法类似
## Step.1
发送方进入要传递文件的目录 
## Step.2
地址栏输入`cmd`

## Step.3
输入指令`ipconfig`，获得`ipv4`地址，假设你获得的是`192.168.1.10`

## Step.4
确认系统环境的`python`版本，不同版本输入不同的指令，请对号入座。

- python2
```
python -m SimpleHTTPServer
```

- python3

```
python -m http.server
```
## Step.5
此时防火墙会有提示，选择`允许访问`

## Step.6
接收方打开任意浏览器，输入`http:192.168.1.10:8000`，页面上立马会返回`Step.1`中看到的目录，此时就已经大功告成了。

# 新的旅程
技术的诞生是因为问题需要得到解决，然而解决问题的方案永远不止一种，此时应在特定的场景下寻求最优解，不局限某种方法，不固步自封，勇于尝试各种思路。从这周开始，每一周的周末，我都会和大家分享，日常工作生活学习中遇到的各种问题，以及我是怎么解决的，不求给大家多大的帮助，但求拓宽解决问题的思路。
