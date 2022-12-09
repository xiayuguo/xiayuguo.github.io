---
title: How to use Excel with Go
date: 2020-04-15T18:52:12+08:00
draft: false
comments: true
tags: 
- Golang
---
Microsoft Excel™ (XLSX) 是广泛使用的一种电子表格格式。

![](http://oss.yuguo.im/blog/202004/golang-excel.png)

Github 上有两个 Golang 包可以方便读写 Excel 文件。

||[360EntSecGroup-Skylar/excelize](https://github.com/360EntSecGroup-Skylar/excelize "360EntSecGroup-Skylar/excelize")|[tealeg/xlsx](https://github.com/tealeg/xlsx "tealeg/xlsx")|
|:-|:-|:-|
|Watch|170|180|
|Star|6k|4k|
|Fork|652|657|

包 `360EntSecGroup-Skylar/excelize` 比 `tealeg/xlsx` 关注度更高，并且 [README.md](https://github.com/360EntSecGroup-Skylar/excelize/blob/master/README_zh.md "README.md") 中有很多示例代码，下面就来看看这个包。

## 360EntSecGroup-Skylar/excelize

### 安装

```
go get github.com/360EntSecGroup-Skylar/excelize
```

### 创建 Excel 文档

#### 官方示例
```
package main

import (
    "fmt"
    "github.com/360EntSecGroup-Skylar/excelize"
)

func main() {
    f := excelize.NewFile()
    // 创建一个工作表
    index := f.NewSheet("Sheet2")
    // 设置单元格的值
    f.SetCellValue("Sheet2", "A2", "Hello world.")
    f.SetCellValue("Sheet1", "B2", 100)
    // 设置工作簿的默认工作表
    f.SetActiveSheet(index)
    // 根据指定路径保存文件
    if err := f.SaveAs("Book1.xlsx"); err != nil {
        fmt.Println(err)
    }
}
```

`func (f *File) SetCellValue(sheet, axis string, value interface{})` 这个方法只能设置一个单元格的值，而通过查看源码，我们可以看到其实有一个方法 `func (f *File) SetSheetRow(sheet, axis string, slice interface{})` 可以设置一行单元格的数据。
```
// SetSheetRow writes an array to row by given worksheet name, starting
// coordinate and a pointer to array type 'slice'. For example, writes an
// array to row 6 start with the cell B6 on Sheet1:
//
//     xlsx.SetSheetRow("Sheet1", "B6", &[]interface{}{"1", nil, 2})
//
```

#### Gin 中导出 Excel
```
package main

import (
    "strconv"
	"github.com/360EntSecGroup-Skylar/excelize"
	"github.com/gin-gonic/gin"
)
 
// Export 导出 Excel
func Export(c *gin.Context, head []string, body [][]interface{}, filename string)  {
	xlsx := excelize.NewFile()
	xlsx.SetSheetRow("Sheet1", "A1", &head)
	for index, rowData :=  range body{
		xlsx.SetSheetRow("Sheet1", "A" + strconv.Itoa(index + 2), &rowData)
	}
	c.Header("Content-Type", "application/octet-stream")
	c.Header("Content-Disposition", "attachment; filename=" + filename)
	c.Header("Content-Transfer-Encoding", "binary")
	_ = xlsx.Write(c.Writer)
}

func main() {
	r := gin.Default()
	r.GET("/testExport", func(c *gin.Context) {
        head := []string{"表头一", "表头二", "表头三"}
        body := [][]interface{}{{1, "2020", ""}, {2, "2019", ""}, {3, "2018", ""}}
        filename := "test.xlsx"
		Export(c, head, body, filename)
	})
	r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```
打开浏览器访问 `http://localhost:8080/testExport`, 直接下载文件 `text.xlsx`。
![](http://oss.yuguo.im/blog/202004/golang-excel-result.png)

### 读取 Excel 文档
#### 官方示例
```
package main

import (
    "fmt"
    "github.com/360EntSecGroup-Skylar/excelize"
)

func main() {
    f, err := excelize.OpenFile("Book1.xlsx")
    if err != nil {
        fmt.Println(err)
        return
    }
    // 获取工作表中指定单元格的值
    cell, err := f.GetCellValue("Sheet1", "B2")
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println(cell)
    // 获取 Sheet1 上所有单元格
    rows, err := f.GetRows("Sheet1")
    for _, row := range rows {
        for _, colCell := range row {
            fmt.Print(colCell, "\t")
        }
        fmt.Println()
    }
}
```


