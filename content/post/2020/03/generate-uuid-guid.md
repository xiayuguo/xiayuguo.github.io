---
title: Golang 中生成唯一的字符串（UUID，GUID）(译文)
date: 2020-03-15T15:48:09+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/generate-uuid-guid/

![](http://oss.yuguo.im/blog/202003/fingerprint-keyboard.jpg)

通用唯一标识符 UUID（universally unique identifier）或全局唯一标识符 GUID （globally unique identifier）是用于标识信息的 128 位数字。
- UUID 是唯一的(出于实用目的)：它重复的概率非常接近零。
- UUID 不依赖中央机构的注册和分配。

UUID 的字符串表示形式由 32 个十六进制数字组成，以 5 个组显示，由连字符 `-` 分隔。例如：
```
123e4567-e89b-12d3-a456-426655440000
```

## UUID 生成器示例
您可以使用来自 [crypto/rand](https://golang.org/pkg/crypto/rand/ "crypto/rand") 包的 [rand.Read](https://golang.org/pkg/crypto/rand/#Read "rand.Read") 函数来生成基本的 UUID。
```golang
b := make([]byte, 16)
_, err := rand.Read(b)
if err != nil {
    log.Fatal(err)
}
uuid := fmt.Sprintf("%x-%x-%x-%x-%x",
    b[0:4], b[4:6], b[6:8], b[8:10], b[10:])
fmt.Println(uuid)
```
输出：
```golang
9438167c-9493-4993-fd48-950b27aad7c9
```

#### 局限性
此 UUID 不符合 [RFC4122](https://tools.ietf.org/html/rfc4122 "RFC4122")。特别是，它不包含任何版本号或变体号。
> 警告：如果系统底层调用失败，则 `rand.Read` 调用将返回错误。例如，如果它在 `Unix` 系统上不能读取 `/dev/urandom`，或者在 `Windows` 系统上 [CryptAcquireContext](https://msdn.microsoft.com/en-us/library/windows/desktop/aa379886(v=vs.85).aspx "CryptAcquireContext") 调用失败。

## 延伸阅读
![](http://oss.yuguo.im/blog/202003/dart.jpg)
[Golang 中生成随机数，字符和切片元素（译文）](https://yuguo.im/post/2020/03/generate-number-random-range "Golang 中生成随机数，字符和切片元素（译文）")