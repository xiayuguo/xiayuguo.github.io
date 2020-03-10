---
title: "如何在 Go 中使用 JSON【最佳实践】（译文）"
date: 2020-03-03T10:42:18+08:00
draft: false
comments: true
tags: 
- Golang
---

JSON 数据交换格式对人类来说很容易读写，对机器进行解析和生成也非常有效。

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/json-example/

![bits-man](http://q6gt3ane0.bkt.clouddn.com/bits-man.jpg)

## 默认类型
用于解码和编码 JSON 的默认 Go 类型是

|Go|JSON|
|:--|:--|
|bool|booleans|
|float64|numbers|
|string|strings|
|nil|null|

另外，可以将 [math.big](https://golang.org/pkg/math/big/ "math.big") 包中的 [time.Time](https://golang.org/pkg/time/#Time "time.Time") 和数字类型自动编码为JSON字符串。

请注意，JSON不支持基本整数类型。它们通常可以用浮点数来近似。

> 由于实现 IEEE 754-2008 binary64（双精度）数字的软件普遍可用并得到广泛使用，因此，期望其精度或范围不超过其提供的精度的实现可以实现良好的互操作性。
> 
> 请注意，使用此类软件时，在实现将完全一致于其数值的意义上，整数（整数）且在 $\ce{[-2^53 + 1, 2^53 - 1]}$ 范围内是可以互操作的。
> 
> [RFC 7159: JSON数据交换格式](https://tools.ietf.org/html/rfc7159#section-6 "JSON数据交换格式")

## 编码(marshal) struct 转 JSON
包 [encoding/json](https://golang.org/pkg/encoding/json/ "encoding/json") 中的 [json.Marshal](https://golang.org/pkg/encoding/json/#Marshal "json.Marshal") 函数生成JSON数据。

```
type FruitBasket struct {
    Name    string
    Fruit   []string
    Id      int64  `json:"ref"`
    private string // 私有字段不会被编码
    Created time.Time
}

basket := FruitBasket{
    Name:    "Standard",
    Fruit:   []string{"Apple", "Banana", "Orange"},
    Id:      999,
    private: "Second-rate",
    Created: time.Now(),
}

var jsonData []byte
jsonData, err := json.Marshal(basket)
if err != nil {
    log.Println(err)
}
fmt.Println(string(jsonData))
```

输出：

```
{"Name":"Standard","Fruit":["Apple","Banana","Orange"],"ref":999,"Created":"2018-04-09T23:00:00Z"}
```

只有可以表示为JSON的数据才会被编码；有关完整规则，请参阅 `json.Marshal`。
- 一个结构体中，只有可导出(公有)的字段可以在 JSON 中输出。**其他字段将被忽略**。
- 具有`json`标签的字段：结构体中声明的字段以其标签后名称存储, 而不是其变量名称。
- 指针将被编码为它们指向的值，如果指针为nil，则值为null。

## 漂亮的输出(格式化输出)
在上面的示例中，将 json.Marshal 替换为 [json.MarshalIndent](https://golang.org/pkg/encoding/json/#MarshalIndent "json.MarshalIndent") 以缩进JSON输出。

```
jsonData, err := json.MarshalIndent(basket, "", "    ")
```
输出：
```
{
    "Name": "Standard",
    "Fruit": [
        "Apple",
        "Banana",
        "Orange"
    ],
    "ref": 999,
    "Created": "2018-04-09T23:00:00Z"
}
```

## 解码(unmarshal) JSON 转 struct
包 `encoding/json` 中的 [json.Unmarshal](https://golang.org/pkg/encoding/json/#Unmarshal "json.Unmarshal")函数解析JSON数据。

```
type FruitBasket struct {
    Name    string
    Fruit   []string
    Id      int64 `json:"ref"`
    Created time.Time
}

jsonData := []byte(`
{
    "Name": "Standard",
    "Fruit": [
        "Apple",
        "Banana",
        "Orange"
    ],
    "ref": 999,
    "Created": "2018-04-09T23:00:00Z"
}`)

var basket FruitBasket
err := json.Unmarshal(jsonData, &basket)
if err != nil {
    log.Println(err)Todo
}
fmt.Println(basket.Name, basket.Fruit, basket.Id)
fmt.Println(basket.Created)
```
输出：
```
Standard [Apple Banana Orange] 999
2018-04-09 23:00:00 +0000 UTC
```
请注意，Unmarshal自行分配了一个新的切片。这就是切片, Map 和指针的解码工作的方式。


对于给定的 JSON 键名 Foo，Unmarshal将尝试按以下顺序匹配结构体中的字段：
1. 结构体中具有标签`json："Foo"`的可导出（公有）字段
2. 结构体中命名为 Foo 的字段
3. 一个名为 FOO，FoO 或其他不区分大小写的匹配项的可导出（公有）字段

只有在目标类型中找到的字段才会被解码：
- 当您只希望选择几个特定字段时，这很有用。
- 特别是，目标结构中任何未导出（私有）的字段都不会受到影响。

## 任意对象和数组
**encoding/json** 包使用
- `map[string]interface{}` 以存储任意 JSON 对象
- `[]interface {}` 用于存储任意 JSON 数组

它将把任何有效的 JSON 数据解析到 `interface{}` 值中

考虑以下 JSON 数据：
```
{
    "Name": "Eve",
    "Age": 6,
    "Parents": [
        "Alice",
        "Bob"
    ]
}
```
json.Unmarshal 函数会将其解析为一个映射，该映射的键为字符串，其值本身存储为空接口值：
```
map[string]interface{}{
    "Name": "Eve",
    "Age":  6.0,
    "Parents": []interface{}{
        "Alice",
        "Bob",
    },
}
```
我们可以使用 range 语句遍历 Map，并使用类型转换访问其值。
```
jsonData := []byte(`{"Name":"Eve","Age":6,"Parents":["Alice","Bob"]}`)

var v interface{}
json.Unmarshal(jsonData, &v)
data := v.(map[string]interface{})

for k, v := range data {
    switch v := v.(type) {
    case string:
        fmt.Println(k, v, "(string)")
    case float64:
        fmt.Println(k, v, "(float64)")
    case []interface{}:
        fmt.Println(k, "(array):")
        for i, u := range v {
            fmt.Println("    ", i, u)
        }
    default:
        fmt.Println(k, v, "(unknown)")
    }
}
```
输出：
```
Name Eve (string)
Age 6 (float64)
Parents (array):
     0 Alice
     1 Bob
```
## JSON 文件示例
包 `encoding/json` 中的 [json.Decoder](https://golang.org/pkg/encoding/json/#Decoder "json.Decoder") 和 [json.Encoder](https://golang.org/pkg/encoding/json/#Encoder "json.Encoder") 类型支持读取和写入流，例如文件，JSON数据。

代码演示如下功能：

- 从 Reader（[strings.Reader](https://golang.org/pkg/strings/#Reader "strings.Reader")）读取 JSON 对象流 
- 从每个对象中移除字段 Age
- 然后将对象写入 Writer（[os.Stdout](https://golang.org/pkg/os/#pkg-variables "os.Stdout")）

```
const jsonData = `
    {"Name": "Alice", "Age": 25}
    {"Name": "Bob", "Age": 22}
`
reader := strings.NewReader(jsonData)
writer := os.Stdout

dec := json.NewDecoder(reader)
enc := json.NewEncoder(writer)

for {
    // 读取一个 JSON 对象, 并把它存入 Map 类型中
    var m map[string]interface{}
    if err := dec.Decode(&m); err == io.EOF {
        break
    } else if err != nil {
        log.Fatal(err)
    }

    // 移除所有键值对中键名为 Age 的数据
    for k := range m {
        if k == "Age" {
            delete(m, k)
        }
    }

    // 将 Map 数据写为 JSON 对象
    if err := enc.Encode(&m); err != nil {
        log.Println(err)
    }
}
```
输出：
```
{"Name":"Alice"}
{"Name":"Bob"}
```


