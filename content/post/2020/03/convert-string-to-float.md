---
title: Golang 中字符串和浮点数互转（译文）
date: 2020-03-11T19:33:24+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/convert-string-to-float/

<pre style="font-size:50%;line-height:1em;border:0;">     3.141592653589793238462643383279
   5028841971693993751058209749445923
  07816406286208998628034825342117067
  9821    48086         5132
 823      06647        09384
46        09550        58223
17        25359        4081
          2848         1117
          4502         8410
          2701         9385
         21105        55964
         46229        48954
         9303         81964
         4288         10975
        66593         34461
       284756         48233
       78678          31652        71
      2019091         456485       66
     9234603           48610454326648
    2133936            0726024914127
    3724587             00660631558
    817488               152092096
</pre>

## 字符串转为浮点数
使用 [strconv.ParseFloat](https://golang.org/pkg/strconv/#ParseFloat "strconv.ParseFloat") 函数将字符串解析为浮点数, 通过指定参数 bitSize 的值来确定精度：float32 为 32，float64 为 64。
```
func ParseFloat(s string, bitSize int) (float64, error)
```
当 bitSize 为 32 时，结果仍为 float64 类型，但可以将其转换为 float32 而无需更改其值。
```
f := "3.14159265"
if s, err := strconv.ParseFloat(f, 32); err == nil {
    fmt.Println(s) // 3.1415927410125732
}
if s, err := strconv.ParseFloat(f, 64); err == nil {
    fmt.Println(s) // 3.14159265
}
```

## 浮点数转为字符串
使用 [fmt.Sprintf](https://golang.org/pkg/fmt/#Sprintf "fmt.Sprintf") 方法将浮点数格式化为字符串。
```
s := fmt.Sprintf("%f", 123.456) // s == "123.456000"
```

|格式化输出|	说明	|占位符|
|:--|:--|:--|
|1.234560e+02|科学计数法|	%e|
|123.456000|有小数点而无指数|   %f|
|123.46|	默认宽度，精度 2	|%.2f|
|␣␣123.46|	宽度 8, 精度 2	|%8.2f|
|123.456|	(根据情况选择 %e 或 %f 以产生更紧凑的（无末尾的0）输出)	|%g|


## Fmt 备忘单
![](http://oss.yuguo.im/blog/202003/format-thumb.jpg)

[How to format with fmt](https://yourbasic.org/golang/fmt-printf-reference-cheat-sheet/ "How to format with fmt")