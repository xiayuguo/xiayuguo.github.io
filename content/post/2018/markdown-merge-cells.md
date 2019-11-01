---
title: Markdown中实现合并单元格
date: 2018-12-10T21:30:28+08:00
draft: false
comments: false
tags: 
- Markdown
---

### Method
- 使用 `<br>` 换行符(隐性换行)
- 使用 `html` 标签 `rowspan` 和 `colspan` [excel 转 html](http://pressbin.com/tools/excel_to_html_table/index.html)

### Raw
```
<table>
    <tr>
        <th rowspan="2">值班人员</th>
        <th colspan="2">星期一</th>
        <th>星期二</th>
    </tr>
    <tr>
        <td>李强</td>
        <td>张明</td>
        <td>王平</td>
    </tr>
</table>
```

### Preview
<table>
    <tr>
        <th rowspan="2">值班人员</th>
        <th colspan="2">星期一</th>
        <th>星期二</th>
    </tr>
    <tr>
        <td>李强</td>
        <td>张明</td>
        <td>王平</td>
    </tr>
</table>
