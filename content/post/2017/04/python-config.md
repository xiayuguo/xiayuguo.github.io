---
title: configparser(PY3) VS ConfigParser(PY2)
date: 2017-04-26 18:25:23
tags:
- Python
- configparser
---

> 关于使用ConfigParser,我的小伙伴想实现特殊的需求: 1.配置中的参数均大写; 2.配置文件中"="等号两侧无空格, 于是摸索中写下这篇文章, 方便有缘人

<!--more-->

范例: 配置文件config.ini长成这样
```
[test]
TEST=A
AUTO=B
HAHA=C
```

小伙伴使用的是python2的环境,所有优先考虑ConfigParser(ConfigParser库是自带的嘛)
```python
# https://github.com/hugoxia/Python/config-demo/config.py

# -*- coding: utf-8 -*-

from ConfigParser import ConfigParser


class CustomConfig(ConfigParser):
    def __init__(self, defaults=None):
        ConfigParser.__init__(self, defaults=defaults)

    def write(self, fp):
        """Write an .ini-format representation of the configuration state."""
        if self._defaults:
            fp.write("[%s]\n" % "DEFAULT")
            for (key, value) in self._defaults.items():
                fp.write("%s = %s\n" % (key, str(value).replace('\n', '\n\t')))
            fp.write("\n")
        for section in self._sections:
            fp.write("[%s]\n" % section)
            for (key, value) in self._sections[section].items():
                if key == "__name__":
                    continue
                if (value is not None) or (self._optcre == self.OPTCRE):
                    key = "=".join((key, str(value).replace('\n', '\n\t')))
                fp.write("%s\n" % (key))
            fp.write("\n")


class Config(object):
    def __init__(self, path):
        self.config_path = path
        self.conf = CustomConfig()
        self.conf.optionxform = str
        self.conf.read(self.config_path)
 
    def get(self, field, key):
        return self.conf.get(field, key)

    def set(self, field, key, value):
        return self.conf.set(field, key, value)

    def save(self):
        with open(self.config_path, 'w') as f:
            self.conf.write(f)


if __name__ == "__main__":
    config = Config("./config.ini")
    config.set("test", "HAHA", "D")
    config.save()

```
有没有觉得很麻烦,咱来看看configparser的表现
```python
# https://github.com/hugoxia/Python/config-demo/config_py3.py

# -*- coding: utf-8 -*-

import configparser


class Config(object):
    def __init__(self, path):
        self.config_path = path
        self.conf = configparser.ConfigParser()
        self.conf.optionxform = str
        self.conf.read(self.config_path)
 
    def get(self, field, key):
        return self.conf.get(field, key)

    def set(self, field, key, value):
        return self.conf.set(field, key, value)

    def save(self):
        with open(self.config_path, 'w') as f:
            self.conf.write(f, space_around_delimiters=False)


if __name__ == "__main__":
    config = Config("./config.ini")
    config.set("test", "HAHA", "D")
    config.save()
```
总结:
- 针对保留原大小写的问题, ConfigParser和configparser的处理方式一样
```python
self.optionxform = str # 中文的话,会出现乱码
```

- 针对空格的问题, ConfigParser需要复写write方法,而configparser的write方法自带一个参数space_around_delimiters, 通过修改这个参数可以直接实现.有兴趣的可以看下这两个库的原码.
