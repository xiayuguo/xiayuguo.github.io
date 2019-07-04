---
title: VSCode配置Cmder作为默认的终端(Shell)
date: 2018-05-04T19:09:05+08:00
draft: false
comments: true
tags: 
- Win10
---

> Windows系统中的默认终端不好用，于是使用了[Cmder](https://cmder.net/)。与此同时, VSCode中的默认终端也不招人喜欢，我要Cmder移植到VSCode中怎么做呢？

## 环境
- Cmder 安装目录 `C:\Software\cmder`
- Win10

## 配置
### 第一步: 准备启动脚本

`cmder`安装目录下新建文件`vscmd.bat`

使用`VSCode`打开`vscmd.bat`，写入下面代码

```
@echo off
"C:\Software\cmder\vendor\init.bat"
```

### 第二步: `VSCode`中配置启动项和参数

`VSCode`找到用户设置(使用快捷键Ctrl+,) 打开`settings.json` 添加下面配置

```
{
    "terminal.integrated.shell.windows": "cmd.exe",
    "terminal.integrated.shellArgs.windows": [
        "/K",
        "C:/Software/cmder/vscmd.bat"
    ],
}
```

### 第三步: 重新启动，检验成果
关闭`VSCode`，再重新打开，使用 Ctrl + 反引号(`) ，见证奇迹的时刻。
