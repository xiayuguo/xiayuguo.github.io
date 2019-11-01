---
title: View Memory Information Using the Command Line in Win10
date: 2019-03-21T18:39:28+08:00
draft: false
comments: true
tags: 
- Win10
---


> 通过命令行查看内存条信息

## 步骤一: 打开命令行模式

- 方式一: Win + R 打开运行，输入cmd回车;
- 方式二: Win + S 打开搜索，输入cmd回车;

## 步骤二: 命令行模式下输入`wmic`
```
C:\Users\Hugo>wmic
wmic:root\cli>
```

## 步骤三: 继续输入`memorychip`
```
wmic:root\cli>memorychip
...  Capacity    ConfiguredClockSpeed  ConfiguredVoltage  Manufacturer 
...  8589934592      2133                 1200             Kingston 
...  8589934592      2133                 1200             Samsung  

```
一般购买内存条就关心上面几个参数: 容量, 频率, 电压，生产商；其余参数由于版面有限，这里就不再展示了。

## 附录: 内存条参数说明

|参数|描述|
|:--|:--|
|Capacity               |获取内存容量（单位KB） |
|Caption                |物理内存还虚拟内存|
|ConfiguredClockSpeed   |   配置时钟速度|
|ConfiguredVoltage      | 配置电压|
|CreationClassName      | 创建类名|
|DataWidth              |  获取内存带宽|
|Description            |  描述|
|DeviceLocator          |     获取设备定位器|
|FormFactor             |    构成因素|
|HotSwappable           |    是否支持热插拔|
|InstallDate            |   安装日期|
|InterleaveDataDepth    |   数据交错深度|
|InterleavePosition     |   交错的位置|
|Manufacturer           |      生产商|
|MaxVoltage             |  最大电压|
|MemoryType             |  内存类型|
|MinVoltage             |  最小电压|
|Model                  |    型号|
|Name                   |   名字|
|OtherIdentifyingInfo   | 其他识别信息|
|PartNumber             | 零件编号|
|PositionInRow          |    行位置|
|PoweredOn              |是否接通电源|
|Removable              |是否可拆卸|
|Replaceable            |  是否可更换|
|SerialNumber           |   编号|
|SKU                    |  SKU号|
|SMBIOSMemoryType       |  SMBIOS内存类型|
|Speed                  |    速率|
|Status                 |     状态|
|Tag                    |  唯一标识符的物理存储器|
|TotalWidth             |     总宽|
|TypeDetail             |     类型详细信息|
|Version                |     版本信息|
