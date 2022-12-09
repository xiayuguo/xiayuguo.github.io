---
title: Golang 中 crypto/rand（译文）
date: 2020-03-15T16:08:45+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/crypto-rand-int/

![](http://oss.yuguo.im/blog/202003/programmer.jpg)

Go 有两个用于随机数的包：

- [math/rand](https://golang.org/pkg/math/rand/ "math/rand") 实现了大量伪随机数生成器。 
- [crypto/rand](https://golang.org/pkg/crypto/rand/ "crypto/rand") 实现了具有受限接口的加密安全伪随机数生成器。

这两个包可以通过调用包 `math/rand` 中的 [rand.New](https://golang.org/pkg/math/rand/#New "rand.New") 与一个从 `crypto/rand` 获取数据的源来合并。

```golang
import (
    crand "crypto/rand"
    rand "math/rand"

    "encoding/binary"
    "fmt"
    "log"
)

func main() {
    var src cryptoSource
    rnd := rand.New(src)
    fmt.Println(rnd.Intn(1000)) // a truly random number 0 to 999
}

type cryptoSource struct{}

func (s cryptoSource) Seed(seed int64) {}

func (s cryptoSource) Int63() int64 {
    return int64(s.Uint64() & ^uint64(1<<63))
}

func (s cryptoSource) Uint64() (v uint64) {
    err := binary.Read(crand.Reader, binary.BigEndian, &v)
    if err != nil {
        log.Fatal(err)
    }
    return v
}
```
> 警告：如果系统底层调用失败，则 `crand.Reader` 调用将返回错误。例如，如果它在 `Unix` 系统上不能读取 `/dev/urandom`，或者在 `Windows` 系统上 [CryptAcquireContext](https://msdn.microsoft.com/en-us/library/windows/desktop/aa379886(v=vs.85).aspx "CryptAcquireContext") 调用失败。