---
title: Microsoft Visual C++ 14.0 is required
date: 2019-08-26T18:46:44+08:00
draft: false
comments: true
tags: 
- Python
---

> 在Windows系统上安装某些需要编译的包时经常会遇到这个问题，经常使用的办法是找一个编译好的版本直接通过pip安装，但是找不到怎么办?

## 省时省力，第一优先级
Python Extension Packages for Windows:  https://www.lfd.uci.edu/~gohlke/pythonlibs/

## 安装C编译环境


### 失败经历
先附上我的失败经历，希望直接看到成功的，直接下拉到最后。

#### 1. 利用`python`包的错误提示信息
```
Microsoft Visual C++ 14.0 is required. Get it with "Microsoft Visual C++ Build Tools": https://visualstudio.microsoft.com/downloads/
```
这个网站把2015、2017版本给隐藏起来，找起来比较麻烦。
看这里所有的旧版本都在这: https://visualstudio.microsoft.com/zh-hans/vs/older-downloads/
![](https://hugo-1256107396.cos.ap-chengdu.myqcloud.com/blog/2019/08/m_cplusplus.png)

#### 2. python版本和对应的C++编译版本

https://wiki.python.org/moin/WindowsCompilers

|Visual C++|CPython|
|:--|:--|
|14.X|3.5, 3.6, 3.7, 3.8|
|10.0|3.3, 3.4|
|9.0|2.6, 2.7, 3.0, 3.1, 3.2|

#### 3. 我把2019, 2017, 2015版本(均高于14.0版本)都安装一遍后仍然不行

### 成功经历
参考博客 https://www.hongweipeng.com/index.php/archives/1532/ 

资源下载链接: `https://pan.baidu.com/s/19CkNu5ZiifNrYNHj77tbCg` 

提取码: `ggdv`

默认安装2015，注意根据您的操作系统选择Windows 8.1 / 10 SDK。这个时间比较长，耐心等到安装结束之后，再重试安装，我这边成功了，那么你呢？
如果有更好的方案，请在下方留言，感激不尽。



