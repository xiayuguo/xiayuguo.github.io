---
title: "Plugin Statistic"
date: 2019-01-06T16:01:48+08:00
draft: false
tags:
- Pycharm
- Plugin
---

> 统计项目中的有效代码行数和注释是一件很有趣的事, 安利大家一款统计代码行数的插件 [statistic](https://plugins.jetbrains.com/plugin/4509-statistic)

### 安装
1. 进入 `File` -> `Settings` -> `Plugins` 
2. 搜索关键字 `statistic` -> `Search in repositories`
3. 选中插件 -> `Install`
4. 安装成功后重启 `Pycharm`


### 使用
> 以 `Flask` 为例

1. 底边栏 `statistic`
![statistic-1](https://hugo-1256107396.cos.ap-chengdu.myqcloud.com/blog/2019/statistic-1.png)

2. `Refresh` 
![statistic-2](https://hugo-1256107396.cos.ap-chengdu.myqcloud.com/blog/2019/statistic-2.png)

3. Detail
![statistic-3](https://hugo-1256107396.cos.ap-chengdu.myqcloud.com/blog/2019/statistic-3.png)

|表头|描述|
|:---|:---|
|Source File|单个文件名|
|Total Lines|单个文件总行数|
|Source code Lines|纯代码行数|
|Source code Lines[%]|纯代码行数百分比|
|Comment Lines|注释行数|
|Comment Lines[%]|注释行数百分比|
|Blank Lines|空行行数|
|Blank Lines[%]|空行行数百分比|