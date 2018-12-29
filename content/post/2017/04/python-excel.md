---
title: Python和Excel的不解之缘
date: 2017-04-10 18:48:49
tags:
    - Python
    - Excel
---

## 表格处理模块
- [xlwt (writing xls files)](http://xlwt.readthedocs.io/en/latest/)
- [xlrd (reading xls/xlsx files)](http://xlrd.readthedocs.io/en/latest/)
- [openpyxl (reading/writing xlsx files)](https://openpyxl.readthedocs.io/en/default/)
- [xlsxwriter (writing xlsx files)](https://xlsxwriter.readthedocs.io)
- [more details](http://www.python-excel.org/)

<!--more-->
## xlwt
直接上干货, 重点看export_excel函数
```python
# -*- coding: utf-8 -*-

import xlwt
import functools

from datetime import datetime


def cost_time(method):
    @functools.wraps(method)
    def wrapper(self, *args, **kwargs):
        start_time = datetime.now()
        num = method(self, *args, **kwargs)
        end_time = datetime.now()
        print(u"导出%s条数据, 共消耗%s时间" % (num, str(end_time - start_time)))
    return wrapper


@cost_time
def export_excel(filename, header, content, *args, **kwargs):
    wb = xlwt.Workbook()
    if len(content) > 65536:
        raise Exception(u"你的数据量为%s, 超过65536, 拒绝通过xlwt导出" % len(content))
    ws = wb.add_sheet(u"报表")
    ws1 = wb.add_sheet(u"表2")

    # 写表头
    map(lambda h: ws.write(0, h[0], h[1]), enumerate(header))

    # 写主体内容
    map(lambda c: map(lambda sub_c: ws.write(c[0] + 1, sub_c[0], sub_c[1]), enumerate(c[1])), enumerate(content))

    ws1.write(0, 0, u"test")

    wb.save(filename)
    return len(content)


if __name__ == "__main__":
    header = ("A", "B", "C", "D", "E", "F", "G")
    a = xrange(0, 77777*1)
    content = [(a[i], a[i+1], a[i+2], a[i+3], a[i+4], a[i+5], a[i+6]) for i in range(len(a))[::7]]
    export_excel("test.xls", header=header, content=content)

```

## xlrd
后续更新, 敬请期待...
## openpyxl
后续更新, 敬请期待...
## xlsxwriter
后续更新, 敬请期待...

