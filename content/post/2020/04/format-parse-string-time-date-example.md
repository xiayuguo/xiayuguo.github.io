---
title: Golang 中格式化时间或日期[完整指南]
date: 2020-04-13T06:16:30+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/format-parse-string-time-date-example/

![](http://oss.yuguo.im/blog/202004/stopwatch.png)

## 基本例子
Go 不使用 `yyyy-mm-dd` 模式来格式化时间。而是，格式化一个特殊的参数 

```Mon Jan 2 15:04:05 MST 2006```

与指定的时间或日期格式相同。(当写为 `01/02 03:04:05 PM 06 -0700` 时，这个日期更容易记住。美式的时间格式 月，日，时，分，秒，年排列起来依次是 1 2 3 4 5 6)

```
const (
    layoutISO = "2006-01-02"
    layoutUS  = "January 2, 2006"
)
date := "1999-12-31"
t, _ := time.Parse(layoutISO, date)
fmt.Println(t)                  // 1999-12-31 00:00:00 +0000 UTC
fmt.Println(t.Format(layoutUS)) // December 31, 1999
```

## 标准时间和日期格式

|Go 格式模板|注释|
|:--- |:--- |
|January 2, 2006|Date|
|01/02/06||
|Jan-02-06||
|15:04:05|Time|
|3:04:05 PM||
|Jan _2 15:04:05|Timestamp|
|Jan _2 15:04:05.000000|with microseconds|
|2006-01-02T15:04:05-0700|[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601 "ISO 8601") ([RFC 3339](https://www.ietf.org/rfc/rfc3339.txt "RFC 3339"))|
|2006-01-02||
|15:04:05||
|02 Jan 06 15:04 MST|[RFC 822](https://www.ietf.org/rfc/rfc822.txt "RFC 822")|
|02 Jan 06 15:04 -0700|with numeric zone|
|Mon, 02 Jan 2006 15:04:05 MST|[RFC 1123](https://www.ietf.org/rfc/rfc1123.txt "RFC 1123")|
|Mon, 02 Jan 2006 15:04:05 -0700|with numeric zone|

以下预定义的日期和时间戳[格式常量](https://golang.org/pkg/time/#pkg-constants "格式常量")也可用。

```
ANSIC       = "Mon Jan _2 15:04:05 2006"
UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
RFC822      = "02 Jan 06 15:04 MST"
RFC822Z     = "02 Jan 06 15:04 -0700"
RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700"
RFC3339     = "2006-01-02T15:04:05Z07:00"
RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
Kitchen     = "3:04PM"
// Handy time stamps.
Stamp      = "Jan _2 15:04:05"
StampMilli = "Jan _2 15:04:05.000"
StampMicro = "Jan _2 15:04:05.000000"
StampNano  = "Jan _2 15:04:05.000000000"
```

## 模式选项

|Type|Options|
|--- |--- |
|Year|06   2006|
|Month|01   1   Jan   January|
|Day|02   2   _2     (width two, right justified)|
|Weekday|Mon   Monday|
|Hours|03   3   15|
|Minutes|04   4|
|Seconds|05   5|
|ms μs ns|.000   .000000   .000000000|
|ms μs ns|.999   .999999   .999999999    (trailing zeros removed)|
|am/pm|PM   pm|
|Timezone|MST|
|Offset|-0700   -07   -07:00   Z0700   Z07:00|

## 个别案例
- 无法指定以 24 小时制显示不带前导零的小时。

- 无法将午夜指定为 24:00，而不是 00:00。这样做的典型用法是提供开放时间至午夜，例如 07:00 - **24**:00。

- 无法指定包含临界秒的时间：23:59:**60**。实际上，该时间包采用的是公历日历，没有临界秒。
