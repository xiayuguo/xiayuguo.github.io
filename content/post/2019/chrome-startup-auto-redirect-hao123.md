---
title: 无法将所做的改动保存到 chrome lnk
date: 2019-06-30T21:25:07+08:00
draft: false
comments: true
tags: 
- Chrome
- Life
---

> 昨天把旧电脑取出来，给它充放电。恰巧要查点资料，顺手打开谷歌浏览器，我去，怎么一上来就是hao123？

我的第一反应就是某个软件改写了`Chrome`的设置。

## 1. 查询设置

#### 启动时

- 打开新标签页
- 从上次停下的地方继续
- 打开特定网页或一组网页
    - 添加新网页
    - 使用当前网页

没有任何异常，没有植入`hao123`网址

## 2. 右键`Chrome`快捷方式查看属性
```
目标(T): 
"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" http://www.hao123.com


起始位置(S): 
"C:\Program Files (x86)\Google\Chrome\Application"
```
果然罪魁祸首在这里，把后面的网址删除后，提示: `无法将所做的改动保存到 chrome lnk`

## 3. 解决方案
1. 删除`Chrome`快捷方式
2. 拷贝 `起始位置(S)` 到地址栏
3. 找到 `chrome.exe`, 重新创建快捷方式
