---
title: 关于Python中json库的一些整理
date: 2017-04-19 15:42:53
tags:
- Json
- Python
---

### json.dump和json.dumps的区别([来自Stackoverflow的释疑](http://stackoverflow.com/questions/36059194/what-is-the-difference-between-json-dump-and-json-dumps-in-python))
- json.dump
> Serialize ``obj`` as a JSON formatted stream to ``fp`` (a ``.write()``-supporting file-like object).
> If ``ensure_ascii`` is ``False``, some chunks written to ``fp`` may be ``unicode`` instances.

- json.dumps
> Serialize ``obj`` to a JSON formatted ``str``.
> If ``ensure_ascii`` is ``False``, all non-ASCII characters are not escaped, and the return value may be a ``unicode`` instance.

<!--more-->
### ensure_ascii的神奇之处
[Python Docs](https://docs.python.org/2/library/json.html?highlight=ensure_ascii#basic-usage)

> If `ensure_ascii` is true (the default), all non-ASCII characters in the output are escaped with `\uXXXX` sequences, and the result is a `str` instance consisting of ASCII characters only.  If `ensure_ascii` is `False`, some chunks written to `fp` may be `unicode` instances. This usually happens because the input contains unicode strings or the `encoding` parameter is used. Unless `fp.write()` explicitly understands `unicode` ( as in `codecs.getwriter` ) this is likely to cause an error.

#### 实战
```python
>>> import json
>>> data = [u"她", u"是", u"美女"]
>>> output1 = json.dumps(data)
>>> output1
'["\\u5979", "\\u662f", "\\u7f8e\\u5973"]'
>>> print(output1)
["\\u5979", "\\u662f", "\\u7f8e\\u5973"]
>>> output2 = json.dumps(data, ensure_ascii=False)
>>> outout2
u'["\u5979", "\u662f", "\u7f8e\u5973"]'
>>> print(output2)
["她", "是", "美女"]

```

### 参数cls的妙用之处理时间
```python
import json
from datetime import datetime

class DateTimeEncoder(json.JSONEncoder):
    """convert datetime to string in json.dumps"""

    def default(self, obj):
        if isinstance(obj, datetime):
            encoded_object = obj.strftime('%Y-%m-%d %H:%M:%S')
        elif isinstance(obj, date):
            encoded_object = obj.strftime('%Y-%m-%d')
        else:
            encoded_object = json.JSONEncoder.default(self, obj)
        return encoded_object

if __name__ == "__main__":
    data = {"now": datetime.now()}
    print(json.dumps(data, cls=DateTimeEncoder))
    # output: {"now": "2017-04-19 16:32:53"}
```

