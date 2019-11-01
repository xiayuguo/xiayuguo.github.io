---
title: "Golang何时该使用指针?"
date: 2018-12-25T21:05:31+08:00
draft: false
tags:
- Golang
---

[Github 原文](https://github.com/golang/go/wiki/CodeReviewComments#receiver-type)

> 一个函数何时该用指针类型做receiver对初学者而言一直是个头疼的问题。如果不知道该如何取舍，选择指针类型的receiver。但有些时候value receiver更加合适，比如对象是一些轻量级的不变的structs，使用value receiver会更加高效。下面是列举了一些常用的判断指导。

- 如果receiver是map、func或者chan，不要使用指针
- 如果receiver是slice并且该函数并不会修改此slice，不要使用指针
- 如果该函数会修改receiver，此时一定要用指针
- 如果receiver是struct并且包含互斥类型sync.Mutex，或者是类似的同步变量，receiver必须是指针，这样可以避免对象拷贝
- 如果receiver是较大的struct或者array，使用指针则更加高效。多大才算大？假设struct内所有成员都要作为函数变量传进去，如果觉得这时数据太多，就是struct太大
- 如果receiver是struct，array或者slice，并且其中某个element指向了某个可变量，则这个时候receiver选指针会使代码的意图更加明显
- 如果receiver使较小的struct或者array，并且其变量都是些不变量、常量，例如time.Time，value receiver更加适合，因为value receiver可以减少需要回收的垃圾量
- 最后，如果不确定用哪个，使用指针类的receiver