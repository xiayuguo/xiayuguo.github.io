---
title: Pycharm配置autopep8到菜单
date: 2017-04-12 22:27:15
tags:
- Pycharm
- PEP8
categories:
- Pycharm
---
{% note success %}
Pycharm可以自动检测PEP8规范, 菜单`Code`–>`Reformat Code`可以自动调整代码,但是效果一般,只能实现简单的规范代码; 为了满足需要, 可以安装autopep8来自动修改文件实现PEP8规范.
{% endnote %}

<!--more-->
### 安装autopep8
```python
pip install autopep8
```
### Pycharm中进行设置
- `Settings`–>`Tools`–>`External Tools`, 点击`+`按钮
    - Name: autopep8 (可以自定义)
    - Tools settings:
        - Programs: autopep8([Ubuntu](#注)和[Windows](#注))
        - Parameters: `--in-place --aggressive --aggressive $FilePath$`
        - Working directory: `$ProjectFileDir$`
- 点击`Output Filters...`
    - 点击`+`，名称可以任意填写
    - Regular expression to match output: `$FILE_PATH$\:$LINE$\:$COLUMN$\:.*`

### 使用

- 方法一: 代码上右击–>`External Tool`–>`autopep8`
- 方法二: `Tools`->`External`->`autopep8`

### 注
- Ubuntu: 示例绝对路径(/home/hugo/.virtualenvs/test/bin/autopep8)
- Windows: 示例绝对路径(C:\Users\hugo\AppData\Local\Programs\Python\Python35\Scripts\autopep8.exe)
