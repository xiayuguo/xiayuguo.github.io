---
title: Pip Install Pipenv Fail
date: 2018-05-21T21:15:15+08:00
draft: false
comments: true
tags: 
- Python
---

> 提示: 'install_requires' must be a string or list of strings containing valid project/version requirement specifiers

解决方式([pipenv版本管理](https://github.com/pypa/pipenv/releases))

思路: 先装次新版本，再装最新版本
```
pip install pipenv==11.10.4
pip install pipenv
```