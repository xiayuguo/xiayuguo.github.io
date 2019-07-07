---
title: Difference Between The 'history' Command and 'cat ~/.bash_history'
date: 2019-07-05T20:35:16+08:00
draft: false
comments: true
tags: 
- Linux
---


> 寻找这个问题的答案时，无意间看到1999年的[一段对话](https://www.redhat.com/archives/redhat-list/1999-September/msg03175.html)，完美的解释了我的疑问。通过操作验证，`David` 说的 100% 正确。
> 如果别人问起我这个问题，我也会以他的原话来回答，因为我找不到比他回答得更完美的答案了。
>
> History for your current shell is retained in RAM.  It is appended to
> .bash_history (subject to the length you define for that 
> file) when you exit
> the shell.  Therefore, cat ~/.bash_history will always be as 
> current as the
> LAST shell you ran, and its last entry should be "exit".  
> Commands run in
> the current shell won't appear there yet.


## History for your current shell is retained in RAM.

终端(shell)的操作历史保存在内存(Random Access Memory)中。

```
并且只有当前终端可以看到。与此同时，你再打开一个终端，是看不到前一个的操作记录，除非在你打开第二个终端前，第一个打开的终端已经关闭了。
```

## It is appended to .bash_history (subject to the length you define for that file) when you exit the shell.

当退出终端时，操作历史才会被添加到`.bash_history`文件中，`.bash_history`中存储的记录数量取决于你定义的值(HISTFILESIZE)。

```
这里顺便解释一个问题：HISTFILESIZE 和 HISTSIZE 的差别？

在linux系统中，history命令可以输出历史命令，历史命令默认保存在文件`.bash_history`中。

HISTFILESIZE 定义了在 .bash_history 中保存命令的记录总数，可以理解为.bash_history文件中最多只有 HISTFILESIZE 行

HISTSIZE 定义了 history 命令输出的记录数，即输出 .bash_history 文件中的最后 HISTSIZE 行
```

## Therefore, cat ~/.bash_history will always be as current as the LAST shell you ran, and its last entry should be "exit". 

所以查看文件`.bash_history`内容时，看到的总是最近一次终端运行的命令，并且它的最后一条命令是`exit`。

## Commands run in the current shell won't appear there yet.

当前终端中运行过的命令还不会出现在这个文件`.bash_history`中。

