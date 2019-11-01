---
title: Go学习笔记
date: 2017-04-22 22:53:29
tags:
- Go
- Note
categories:
- Go
---

# Go笔记
{% blockquote Go https://golang.org Welcome to golang.org %}
The Go programming language is an open source project to make programmers more productive.
{% endblockquote %}
> Go编程语言是一个开源项目，使程序员更有成效

<!--more-->
## Go简介
{% blockquote Go Docs https://golang.org/doc/ %}
Go is expressive, concise, clean, and efficient. Its concurrency mechanisms make it easy to write programs that get the most out of multicore and networked machines, while its novel type system enables flexible and modular program construction. Go compiles quickly to machine code yet has the convenience of garbage collection and the power of run-time reflection. It's a fast, statically typed, compiled language that feels like a dynamically typed, interpreted language.
{% endblockquote %}
- Go语言的优点
    - 脚本化的语法, 非常容易编写程序, 新手容易上手
    - 静态类型+编译型, 程序运行速度有保障
    - 原生的支持并发编程, 降低开发、维护成本、程序更好的执行
- Go语言的缺点
    - 语法糖没有Python和Ruby那么多
    - 目前的程序运行速度还不及C
    - 第三方函数库暂时不像绝对主流的编程语言那样多(随着Go的流行，这不是问题)

## Go的安装和配置
可以到[官网](https://golang.org/dl/)下载相应系统的go版本,下面说说Linux下的安装方式
- Linux(源码安装)
```bash
$ sudo curl -O https://storage.googleapis.com/golang/go1.8.1.linux-amd64.tar.gz
$ sudo tar -zxf go1.8.1.linux-amd64.tar.gz -C /usr/local
$ cd /usr/local/go && bin/go version # 验证安装结果
go version go1.8.1 linux/amd64
```
- Linux配置

    在`~/.profile`中最后添加下面语句，添加完之后执行`source ~/.profile`
```bash
export PATH=$PATH:/usr/local/go/bin
```


