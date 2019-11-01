---
title: History Usage
date: 2019-07-04T22:28:57+08:00
draft: false
comments: true
tags: 
- Linux
---

> `history` 命令用于显示指定数目的指令命令，读取历史命令文件中的目录到历史命令缓冲区和将历史命令缓冲区中的目录写入命令文件。

> 该命令单独使用时，仅显示历史命令，在命令行中，可以使用符号!执行指定序号的历史命令。例如，要执行第2个历史命令，则输入 `!2`。

> 历史命令是被保存在内存中的，当退出或者登录`shell`时，会自动保存或读取。在内存中，历史命令仅能够存储1000条历史命令，该数量是由环境变量`HISTSIZE`进行控制。

## 语法
```
history(选项)(参数)
```

## 选项
```
-c：清空当前历史命令；
-a：将历史命令缓冲区中命令写入历史命令文件中；
-r：将历史命令文件中的命令读入当前历史命令缓冲区；
-w：将当前历史命令缓冲区命令写入历史命令文件中。
```

## 参数
```
n：打印最近的n条历史命令。
```

## 实例
使用history命令显示最近使用的10条历史命令，输入如下命令：
```
[root@localhost ~]# history 10
   92  ls
   93  cd ..
   94  ls
   95  exit
   96  ls -a
   97  cd .ssh/
   98  ls
   99  cat known_hosts
  100  exit
  101  history 10
```

##  其他特殊技巧
### 使用 HISTTIMEFORMAT 显示时间戳
```
export HISTTIMEFORMAT='%F %T '
```
注意：这个功能只能用在当 HISTTIMEFORMAT 这个环境变量被设置之后，之后的那些新执行的 bash 命令才会被打上正确的时间戳。在此之前的所有命令，都将会显示成设置 HISTTIMEFORMAT 变量的时间。可以在 `.bash_profile` 中把这条设置指令添加进去，后面就可以一直使用了。

### 使用 Ctrl + R 查询历史记录
动手试试，超级便利。

### 更多关于`history`和`.bash_history`的内幕可以看下我整理的这篇[博客](https://blog.hugoxia.com/post/2019/07/history-and-history-file/)

