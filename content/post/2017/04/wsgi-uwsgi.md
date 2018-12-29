---
title: WSGI，uwsgi和uWSGI的区别
date: 2017-04-25 16:00:13
tags:
- Python
- WSGI
categories:
- 通信协议
---
## WSGI
[Web服务器网关接口](https://zh.wikipedia.org/wiki/Web%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%BD%91%E5%85%B3%E6%8E%A5%E5%8F%A3) 

> WSGI(Python Web Server Gateway Interface, Web服务器网关接口)是为Python语言定义的Web服务器和Web应用程序或框架之间的一种简单而通用的接口

## uwsgi
[uwsgi协议](http://www.baike.com/wiki/uwsgi)

> uwsgi同WSGI一样是一种通信协议。uwsgi协议是一个uWSGI服务器自有的协议，它用于定义传输信息的类型（type of information），每一个uwsgi packet前4byte为传输信息类型描述，它与WSGI相比是两样东西。

## uWSGI
[uWSGI](http://www.baike.com/wiki/uwsgi)

> uWSGI是一个Web服务器，它实现了WSGI协议、uwsgi、http等协议。Nginx中HttpUwsgiModule的作用是与uWSGI服务器进行交换。

- 最流行的 uWSGI 服务器是 [uwsgi](https://uwsgi-docs.readthedocs.io/en/latest/)
