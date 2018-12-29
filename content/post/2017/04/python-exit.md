---
title: Python 中的 exit()、sys.exit() 和 os._exit()的区别
date: 2017-04-24 11:21:54
tags:
- Python
---

## Python官方解释
### exit()
{% blockquote Python Docs https://docs.python.org/2/library/constants.html#exit exit([code=None]) %}
Objects that when printed, print a message like “Use quit() or Ctrl-D (i.e. EOF) to exit”, and when called, raise SystemExit with the specified exit code.
{% endblockquote %}
<!--more-->
{% note success %}
简言之: 抛出 SystemExit 异常. 用于给交互式Shell退出.
{% endnote %}

### sys.exit()
{% blockquote Python Docs https://docs.python.org/2/library/sys.html#sys.exit sys.exit([arg]) %}
Exit from Python. This is implemented by raising the SystemExit exception, so cleanup actions specified by finally clauses of try statements are honored, and it is possible to intercept the exit attempt at an outer level.
{% endblockquote %}
简言之: 抛出 SystemExit 异常, 可以用try捕获异常执行清理工作. 用于程序内部.

### os._exit()
{% blockquote Python Docs https://docs.python.org/2/library/os.html?highlight=os#os._exit os._exit(n) %}
Exit the process with status n, without calling cleanup handlers, flushing stdio buffers, etc.
Note: The standard way to exit is [sys.exit(n)](https://docs.python.org/2/library/sys.html#sys.exit). _exit() should normally only be used in the child process after a fork().
{% endblockquote %}
简言之: 直接退出, 不抛出异常. 用于子进程的退出.

## 参考文档

- [python 中 os._exit()， sys.exit()， exit() 的区别是什么(知乎)](https://www.zhihu.com/question/21187839)
- [The difference between exit() and sys.exit() in Python?(stackoverflow)](http://stackoverflow.com/questions/6501121/difference-between-exit-and-sys-exit-in-python)
- [Python 中的 exit() 和 sys.exit()(jaminzhang's blog)](http://jaminzhang.github.io/python/exit-and-sys-exit-in-python/)

