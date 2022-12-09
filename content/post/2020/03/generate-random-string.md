---
title: Golang 中生成密码（译文）
date: 2020-03-12T08:27:26+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/generate-random-string/

![](http://oss.yuguo.im/blog/202003/password-generator.jpg)

## 随机字符串
该代码从瑞典语字母（包括非 ASCII 字符 å， ä 和 ö ）中生成一个随机的数字和字符字符串。
```golang
rand.Seed(time.Now().UnixNano())
chars := []rune("ABCDEFGHIJKLMNOPQRSTUVWXYZÅÄÖ" +
    "abcdefghijklmnopqrstuvwxyzåäö" +
    "0123456789")
length := 8
var b strings.Builder
for i := 0; i < length; i++ {
    b.WriteRune(chars[rand.Intn(len(chars))])
}
str := b.String() // E.g. "ExcbsVQs"
```
> 警告：要生成密码，应使用加密安全的伪随机数。请参阅 [User-friendly access to crypto/rand](https://yuguo.im/post/2020/03/crypto-rand-int "User-friendly access to crypto/rand")。

## 有限制的随机字符串
此代码生成一个随机的 ASCII 字符串，该字符串至少包含一个数字和一个特殊字符。
```golang
rand.Seed(time.Now().UnixNano())
digits := "0123456789"
specials := "~=+%^*/()[]{}/!@#$?|"
all := "ABCDEFGHIJKLMNOPQRSTUVWXYZ" +
    "abcdefghijklmnopqrstuvwxyz" +
    digits + specials
length := 8
buf := make([]byte, length)
buf[0] = digits[rand.Intn(len(digits))]
buf[1] = specials[rand.Intn(len(specials))]
for i := 2; i < length; i++ {
    buf[i] = all[rand.Intn(len(all))]
}
rand.Shuffle(len(buf), func(i, j int) {
    buf[i], buf[j] = buf[j], buf[i]
})
str := string(buf) // E.g. "3i[g0|)z"
```
#### Go 1.10 之前
在 Go 1.10 之前的代码中，用以下代码替换对rand.Shuffle的调用：
```golang
for i := len(buf) - 1; i > 0; i-- { // Fisher–Yates shuffle
    j := rand.Intn(i + 1)
    buf[i], buf[j] = buf[j], buf[i]
}
```

## 延伸阅读
![](http://oss.yuguo.im/blog/202003/dart.jpg)
[Golang 中生成随机数，字符和切片元素（译文）](https://yuguo.im/post/2020/03/generate-number-random-range "Golang 中生成随机数，字符和切片元素（译文）")
