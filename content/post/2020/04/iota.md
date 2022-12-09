---
title: Golang 中关于 iota 的 4 个例子
date: 2020-04-16T07:15:40+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/iota/

![](http://oss.yuguo.im/blog/202004/lane-numbers.jpg)

## iota 基本示例
- `iota` 关键字表示连续的整数常量0、1、2，...
- 每当源代码中出现 `const` 关键字时，它将重置为 0，
- 并在每个 `const` 后按规范递增。

```golang
const (
    C0 = iota
    C1 = iota
    C2 = iota
)
fmt.Println(C0, C1, C2) // "0 1 2"
```

这可以简写为

```golang
const (
	C0 = iota
	C1
	C2
)
```

这里我们依靠这样的事实，即表达式是在经过简化的 `const` 声明中隐式重复的 —— 这表示前面的表达式及其类型的重复。

### 从 1 开始
要以 1 而不是 0 开头的常量列表，可以在算术表达式中使用 `iota`。

```golang
const (
    C1 = iota + 1
    C2
    C3
)
fmt.Println(C1, C2, C3) // "1 2 3"
```

### 跳过某个值
您可以使用标识符 `_` 跳过常量列表中的值。

```golang
const (
    C1 = iota + 1
    _
    C3
    C4
)
fmt.Println(C1, C3, C4) // "1 3 4"
```

## 带有字符串的完整枚举类型[最佳实践]
这是一种实现枚举类型的惯用方式：
- 创建一个新的整数类型， 
- 使用 `iota` 列出其值， 
- 给类型一个 `String` 函数。

```golang
type Direction int

const (
    North Direction = iota
    East
    South
    West
)

func (d Direction) String() string {
    return [...]string{"North", "East", "South", "West"}[d]
}
```

使用：

```golang
var d Direction = North
fmt.Print(d)
switch d {
case North:
    fmt.Println(" goes up.")
case South:
    fmt.Println(" goes down.")
default:
    fmt.Println(" stays put.")
}
// Output: North goes up.
```

### 命名约定
标准命名约定是对常量也使用混合大写。例如，导出的常量为 `NorthWest`，而不是 `NORTH_WEST`。

## 延伸阅读
### `iota` 与 `<<`
`iota` 的另一个常见应用如下

```golang
const (
	B = 1 << (10 * iota) // 1                    (1 << 10 * 0)
	KB                   // 1024                 (1 << 10 * 1)
	MB                   // 1048576              (1 << 10 * 2)
	GB                   // 1073741824           (1 << 10 * 3)
	TB                   // 1099511627776        (1 << 40 * 4)
	PB                   // 1125899906842624     (1 << 50 * 5)
)
```