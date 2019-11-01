---
title: 通过命令行激活Win10
date: 2019-01-06T17:37:01+08:00
draft: false
comments: false
tags: 
- Win10
---

> 一直以来，网上各种激活工具，层出不穷，但是更多的是病毒和垃圾广告，
费心寻找之下，终于找到这种简单、免安装的方式来激活。 缺点是只有 `180` 天有效期。

### 方法
#### 1. 通过 slmgr
[slmgr](https://blog.csdn.net/yongyong169/article/details/79086382)(Software LiscenceManager)

```
// 1.检查是VL版本
slmgr /dlv
// 产品密钥通道: 包含VL的字符串 如果不包含则终止此方法
// 2.激活系统
slmgr /skms kms.03k.org
slmgr /ato
```
#### 2. 一键激活
参考这篇文章: http://kms.cangshui.net/

### 参考文章
- https://www.jianshu.com/p/5886e8cbbaf3
- https://03k.org/kms.html