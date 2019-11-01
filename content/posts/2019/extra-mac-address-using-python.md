---
title: Extracting MAC address using Python
date: 2019-06-29T15:11:07+08:00
draft: false
comments: true
tags: 
- Python
---

> 使用 `Python` 来提取计算机的 `MAC` 地址。翻译来源: https://www.geeksforgeeks.org/extracting-mac-address-using-python/


MAC地址也称为物理地址，是分配给计算机网卡(NIC)的唯一标识符。NIC有助于将计算机与网络中的其他计算机连接。 MAC地址对于所有NIC都是唯一的。

MAC地址的用途：

- 在IP地址频繁更改的地方很有用。帮助网络管理员。获取有关网络流量的信息。
- 帮助我们配置哪些计算机可以连接到我们的计算机。通过这种方式我们可以过滤潜在的垃圾邮件/病毒攻击。
- 帮助从世界各地的其他计算机中唯一识别计算机。

```
Example MAC Address
00 16 2C 99 0B DB
|______| |______|
    |        |
Vendor No Serial No
```

## 方法1：使用 `uuid.getnode()`

`getnode()` 可用于提取计算机的MAC地址。该方法在`uuid`模块中定义。
下面给出的插图代码显示了如何使用uuid1（）函数为给定主机生成UUID，该UUID由其MAC地址标识。

#### 代码:
```
## Python Program to compute 
## MAC address of host 
## using UUID module 

import uuid 

## printing the value of unique MAC 
## address using uuid and getnode() function 
print(hex(uuid.getnode()))
```

#### 输出:
```
0x163e990bdb
```
#### 缺点:
- 显而易见的缺点是“输出不是格式化的形式”。


## 方法2：使用`getnode()` + `format()`[为了更好的格式化形式]

#### 代码:
```
## Python 3 code to print MAC 
## in formatted way. 
  
import uuid

## joins elements of getnode() after each 2 digits. 
  
print ("The MAC address in formatted way is : ", end="") 
print (':'.join(['{:02x}'.format((uuid.getnode() >> ele) & 0xff) 
for ele in range(0,8*6,8)][::-1])) 
```

#### 输出:
```
The MAC address in formatted way is : 00:16:3e:99:0b:db
```
#### 缺点:
- 该代码表现似乎很复杂。

## 方法3：使用`getnode()` + `findall()` + `re()`[为了降低复杂性]

#### 代码:
```
## Python 3 code to print MAC 
## in formatted way and easier 
## to understand 
  
import re, uuid 
  
## joins elements of getnode() after each 2 digits. 
## using regex expression 
print ("The MAC address in formatted and less complex way is : ", end="") 
print (':'.join(re.findall('..', '%012x' % uuid.getnode())))
```

#### 输出:
```
The MAC address in formatted and less complex way is : 00:16:3e:99:0b:db
```

## 方法4: 使用`getnode()` + `for...range...`

#### 代码:
```
## Python 3 code to print MAC 
## in formatted way and easier 
## to understand 
  
import uuid 
  
## joins elements of getnode() after each 2 digits. 
## using slice expression 
print ("The MAC address in formatted and less complex way is : ", end="")
mac = '%012x' % uuid.getnode()
print(':'.join(mac[i:i+2] for i in range(0, len(mac), 2)))
```

#### 输出:
```
The MAC address in formatted and less complex way is : 00:16:3e:99:0b:db
```





