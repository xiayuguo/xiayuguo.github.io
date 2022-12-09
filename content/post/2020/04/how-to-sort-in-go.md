---
title: Golang 中的 3 种排序方式（译文）
date: 2020-04-08T07:57:31+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/how-to-sort-in-go/

![](http://oss.yuguo.im/blog/202004/sorted-ring-binders.jpg)

## 对整数，浮点数或字符串的切片进行排序

使用下面与其类型对应的方法

- [sort.Ints](https://golang.org/pkg/sort/#Ints "sort.Ints")
- [sort.Float64s](https://golang.org/pkg/sort/#Float64s "sort.Float64s")
- [sort.Strings](https://golang.org/pkg/sort/#Strings "sort.Strings")

```
s := []int{4, 2, 3, 1}
sort.Ints(s)
fmt.Println(s) // [1 2 3 4]
```

> 包 [radix](https://godoc.org/github.com/yourbasic/radix "radix") 是对 sort.Strings 的一种替代方案。在某些情况, 它可以快两倍以上。


## 用自定义比较器排序

- 使用函数 [sort.Slice](https://golang.org/pkg/sort/#Slice "sort.Slice")。它使用提供的功能 `less(i, j int) bool` 对切片进行排序。
- 要在保留相等元素的原始顺序的同时对切片进行排序，请改用 [sort.SliceStable](https://golang.org/pkg/sort/#SliceStable "sort.SliceStable")。

```
family := []struct {
    Name string
    Age  int
}{
    {"Alice", 23},
    {"David", 2},
    {"Eve", 2},
    {"Bob", 25},
}

// Sort by age, keeping original order or equal elements.
sort.SliceStable(family, func(i, j int) bool {
    return family[i].Age < family[j].Age
})
fmt.Println(family) // [{David 2} {Eve 2} {Alice 23} {Bob 25}]
```

## 自定义数据结构排序

- 使用通用的 [sort.Sort](https://golang.org/pkg/sort/#Sort "sort.Sort") 和 [sort.Stable](https://golang.org/pkg/sort/#Stable "sort.Stable") 函数。
- 他们对实现 [sort.Interface](https://golang.org/pkg/sort/#Interface "sort.Interface") 接口的任何集合进行排序。

```
type Interface interface {
        // Len is the number of elements in the collection.
        Len() int
        // Less reports whether the element with
        // index i should sort before the element with index j.
        Less(i, j int) bool
        // Swap swaps the elements with indexes i and j.
        Swap(i, j int)
}
```

这里有一个示例。
```
type Person struct {
    Name string
    Age  int
}

// ByAge implements sort.Interface based on the Age field.
type ByAge []Person

func (a ByAge) Len() int           { return len(a) }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age }
func (a ByAge) Swap(i, j int)      { a[i], a[j] = a[j], a[i] }

func main() {
    family := []Person{
        {"Alice", 23},
        {"Eve", 2},
        {"Bob", 25},
    }
    sort.Sort(ByAge(family))
    fmt.Println(family) // [{Eve 2} {Alice 23} {Bob 25}]
}
```

## 福利: 按键或值对 Map 排序
map 是键值对的无序集合。如果需要稳定的迭代顺序，则必须维护单独的数据结构。

下面的代码示例：使用键构造一个切片, 再按键顺序对 map 进行排序。
```
m := map[string]int{"Alice": 2, "Cecil": 1, "Bob": 3}

keys := make([]string, 0, len(m))
for k := range m {
    keys = append(keys, k)
}
sort.Strings(keys)

for _, k := range keys {
    fmt.Println(k, m[k])
}
// Output:
// Alice 2
// Bob 3
// Cecil 1
```

> 同样，从 [Go 1.12](https://tip.golang.org/doc/go1.12 "Go 1.12 Tip") 开始，[fmt](https://golang.org/pkg/fmt/ "fmt") 包以键排序的顺序打印 map 以简化测试。


## 性能和实现
Go 排序包中的所有算法在最坏的情况下都进行时间复杂度为 O(nlogn) 的比较，其中 n 是要排序的元素数。

大多数功能是使用快速排序的优化版本实现的。
