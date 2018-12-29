---
title: wkhtmltopdf 中文显示有误, 显示“口”方框的解决方案
date: 2018-10-15 17:54:24
tags: 
- Linux
categories:
---

## 运行环境
+ Ubuntu 14.04.2 LTS
+ wkhtmltopdf 0.9.9
+ test.html 测试文件的编码为 UTF-8，文件内部标记编码也是UTF-8，如下所示：

```
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
```

## 执行过程
```
$ wkhtmltopdf test.html test.pdf
Loading page (1/2)
Printing pages (2/2)
Done
```
检查转化后的 pdf 文件，中文显示有问题

## 解决方案

+ 在 Windows 目录( `C:\WINDOWS\Fonts\` )中找到文件 simsun.ttc 大概 17M 左右
+ 拷贝至 `/usr/share/fonts/chinese/TrueType/simsun.ttc`，如果目录 `chinese/TrueType` 不存在，则手动创建
+ 加载缓存 `fc-cache -fv`
+ 再次执行 `wkhtmltopdf test.html test.pdf`
+ 检查显示，完美。

> 有些博客中会建议拷贝 `*.ttf` 之类的文件，目前我没有亲测通过，暂不加入此。
