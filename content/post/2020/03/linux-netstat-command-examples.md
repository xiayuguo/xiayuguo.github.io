---
title: "10 个 Linux netstat 命令实战"
date: 2020-03-26T07:47:50+08:00
draft: false
comments: true
tags: 
- Linux
---

> 作者：Silver Moon
> 
> 原文网址：https://www.binarytides.com/linux-netstat-command-examples/

# Netstat
Netstat 是一个命令行实用程序，可用于列出系统上的所有网络（socket）连接。它列出了所有的 tcp，udp socket 连接和 unix socket 连接。

除了已连接的套接字外，它还可以列出正在等待传入连接的侦听套接字。因此，通过验证开放端口 80，可以确认 Web 服务器是否在系统上运行。这使得它成为网络和系统管理员非常有用的工具。

在本教程中，我们将查证一些示例，说明如何使用 netstat 查找有关网络连接和系统上开放端口的信息。

这是手册（man pages）中对 netstat 的简介
```
netstat - Print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships
```

## 1. 列出所有连接
第一个也是最简单的命令是列出所有当前连接。只需使用 `a` 选项运行 `netstat` 命令。
```
$ netstat -a

Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 enlightened:domain      *:*                     LISTEN     
tcp        0      0 localhost:ipp           *:*                     LISTEN     
tcp        0      0 enlightened.local:54750 li240-5.members.li:http ESTABLISHED
tcp        0      0 enlightened.local:49980 del01s07-in-f14.1:https ESTABLISHED
tcp6       0      0 ip6-localhost:ipp       [::]:*                  LISTEN     
udp        0      0 enlightened:domain      *:*                                
udp        0      0 *:bootpc                *:*                                
udp        0      0 enlightened.local:ntp   *:*                                
udp        0      0 localhost:ntp           *:*                                
udp        0      0 *:ntp                   *:*                                
udp        0      0 *:58570                 *:*                                
udp        0      0 *:mdns                  *:*                                
udp        0      0 *:49459                 *:*                                
udp6       0      0 fe80::216:36ff:fef8:ntp [::]:*                             
udp6       0      0 ip6-localhost:ntp       [::]:*                             
udp6       0      0 [::]:ntp                [::]:*                             
udp6       0      0 [::]:mdns               [::]:*                             
udp6       0      0 [::]:63811              [::]:*                             
udp6       0      0 [::]:54952              [::]:*                             
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ACC ]     STREAM     LISTENING     12403    @/tmp/dbus-IDgfj3UGXX
unix  2      [ ACC ]     STREAM     LISTENING     40202    @/dbus-vfs-daemon/socket-6nUC6CCx
```
上面的命令显示了来自不同协议（例如 tcp，udp 和 unix 套接字）的所有连接。但是，这不是很有用。管理员通常希望例如根据协议或端口号选择特定的连接。

## 2. 仅列出 TCP 或 UDP 连接
要仅列出 tcp 连接，请使用 `t` 选项。
```
$ netstat -at
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 enlightened:domain      *:*                     LISTEN     
tcp        0      0 localhost:ipp           *:*                     LISTEN     
tcp        0      0 enlightened.local:36310 del01s07-in-f24.1:https ESTABLISHED
tcp        0      0 enlightened.local:45038 a96-17-181-10.depl:http ESTABLISHED
tcp        0      0 enlightened.local:37892 ABTS-North-Static-:http ESTABLISHED
.....
```

与仅列出 udp 连接类似，请使用 `u` 选项。
```
$ netstat -au
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
udp        0      0 *:34660                 *:*                                
udp        0      0 enlightened:domain      *:*                                
udp        0      0 *:bootpc                *:*                                
udp        0      0 enlightened.local:ntp   *:*                                
udp        0      0 localhost:ntp           *:*                                
udp        0      0 *:ntp                   *:*                                
udp6       0      0 fe80::216:36ff:fef8:ntp [::]:*                             
udp6       0      0 ip6-localhost:ntp       [::]:*                             
udp6       0      0 [::]:ntp                [::]:*
```
上面的输出显示了ipv4和ipv6连接。

## 3. 禁用反向 DNS 查找以加快输出速度
默认情况下，netstat 命令尝试通过反向 DNS 查找来找出连接中每个 IP 地址的主机名。这会减慢输出速度。如果您不需要知道主机名，仅使用 ip 地址就足够了，那么请使用 `n` 选项禁止查找主机名。
```
$ netstat -ant
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN     
tcp        0      0 192.168.1.2:49058       173.255.230.5:80        ESTABLISHED
tcp        0      0 192.168.1.2:33324       173.194.36.117:443      ESTABLISHED
tcp6       0      0 ::1:631                 :::*                    LISTEN
```
上面的命令显示了所有没有 DNS 解析的 TCP 连接。明白了么？很好，我们继续。

## 4. 仅列出监听连接
任何网络守护程序/服务都会保留一个开放端口以侦听传入的连接。这些也类似于套接字连接，并由 netstat 列出。要仅查看监听端口，请使用 `l` (助记单词 listen) 选项。
```
$ netstat -tnl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN     
tcp6       0      0 ::1:631                 :::*                    LISTEN
```
现在，我们只能看到正在监听的 tcp 端口/连接。如果要查看所有监听端口，请删除 `t` 选项。如果只想监听 udp 端口，请使用 `u` 选项而不是 `t`。确保删除 `a` 选项，否则将列出所有连接，而不仅仅是监听连接。

## 5. 获取进程名称 / pid和用户 ID
在查看打开/监听端口和连接时，了解打开该端口或连接的进程名称 / pid 通常很有用。例如，Apache httpd 服务器打开了端口 80。因此，如果要检查是否正在运行任何 http 服务器，或者正在运行的是 apache 或 nginx，则要查找哪个 http 服务器，然后查找进程名称。

可以通过 `p` 选项获得过程详细信息。
```
$ sudo netstat -nlpt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN      1144/dnsmasq    
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      661/cupsd       
tcp6       0      0 ::1:631                 :::*                    LISTEN      661/cupsd
```

使用 `p` 选项时，netstat 必须以 root 特权运行，否则 netstat 无法检测以 root 特权运行的进程的 pid，大多数服务（如 http 和 ftp）通常以 root 特权运行。

连同进程名称 / pid 一起使用，对于拥有该特定进程的用户名 / uid 更为有用。同时使用 `e` 选项和 `p` 选项来获取用户名。
```
$ sudo netstat -ltpe
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode       PID/Program name
tcp        0      0 enlightened:domain      *:*                     LISTEN      root       11090       1144/dnsmasq    
tcp        0      0 localhost:ipp           *:*                     LISTEN      root       9755        661/cupsd       
tcp6       0      0 ip6-localhost:ipp       [::]:*                  LISTEN      root       9754        661/cupsd
```
上面的示例列出了 Tcp 类型的监听连接以及进程信息和扩展信息。

扩展信息包含进程的用户名和索引节点。这对网络管理员是有用的命令。

**注** - 如果将 `n` 选项与 `e` 选项一起使用，则将列出 uid 而不是用户名。

## 6. 打印统计
netstat 命令还可以打印出网络统计信息，例如按协议类型接收和发送的数据包总数等。

列出所有数据包类型的统计信息
```
$ netstat -s
Ip:
    32797 total packets received
    0 forwarded
    0 incoming packets discarded
    32795 incoming packets delivered
    29115 requests sent out
    60 outgoing packets dropped
Icmp:
    125 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 125
    125 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 125
... OUTPUT TRUNCATED ...
```
要仅输出诸如 TCP 或 UDP 之类的选定协议的统计信息，请使用相应的选项（如 `t` 和 `u` 以及 `s` 选项）。简单！

## 7. 显示内核路由信息
可以使用 `r` 选项来打印内核路由信息。它与 route 命令给定的输出相同。我们还使用 `n` 选项禁用主机名查找。
```
$ netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         192.168.1.1     0.0.0.0         UG        0 0          0 eth0
192.168.1.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
```

## 8. 打印网络接口
netstat 命令还可以打印出有关网络接口的信息。 `i` 选项可以实现这个任务。
```
$ netstat -i
Kernel Interface table
Iface   MTU Met   RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
eth0       1500 0     31611      0      0 0         27503      0      0      0 BMRU
lo        65536 0      2913      0      0 0          2913      0      0      0 LRU
```

上面的输出包含非常原始格式的信息。要获得更人性化的输出版本，请使用 `e` 选项和 `i` 选项。
```
$ netstat -ie
Kernel Interface table
eth0      Link encap:Ethernet  HWaddr 00:16:36:f8:b2:64  
          inet addr:192.168.1.2  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::216:36ff:fef8:b264/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:31682 errors:0 dropped:0 overruns:0 frame:0
          TX packets:27573 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:29637117 (29.6 MB)  TX bytes:4590583 (4.5 MB)
          Interrupt:18 Memory:da000000-da020000 

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:2921 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2921 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:305297 (305.2 KB)  TX bytes:305297 (305.2 KB)
```
上面的输出类似于 `ifconfig` 命令显示的输出

## 9. 连续获取 netstat 输出
Netstat 可以使用 `c` 选项连续输出连接信息。
```
$ netstat -ct
```
上面的命令将连续输出 tcp 连接。

## 10. 显示多播组信息
`g` 选项将显示 IPv4 和 IPv6 协议的多播组信息。
```
$ netstat -g
IPv6/IPv4 Group Memberships
Interface       RefCnt Group
--------------- ------ ---------------------
lo              1      all-systems.mcast.net
eth0            1      224.0.0.251
eth0            1      all-systems.mcast.net
lo              1      ip6-allnodes
lo              1      ff01::1
eth0            1      ff02::fb
eth0            1      ff02::1:fff8:b264
eth0            1      ip6-allnodes
eth0            1      ff01::1
wlan0           1      ip6-allnodes
wlan0           1      ff01::1
```

## Netstat 命令的更多示例
好的，我们涵盖了上面的 netstat 命令的基本示例。现在是时候用样式来做一些怪胎了。

### 打印活动连接
活动的套接字连接处于 “ESTABLISHED” 状态。因此，要获得所有当前的活动连接，请使用 `netstat` 和 `grep`，如下所示
```
$ netstat -atnp | grep ESTA
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 192.168.1.2:49156       173.255.230.5:80        ESTABLISHED 1691/chrome     
tcp        0      0 192.168.1.2:33324       173.194.36.117:443      ESTABLISHED 1691/chrome
```

要监视连续的活动连接列表，请使用watch命令以及netstat和grep
```
$ watch -d -n0 "netstat -atnp | grep ESTA"
```

### 检查服务是否正在运行
如果要检查 http，smtp 或 ntp 之类的服务器是否正在运行，请再次使用 `grep`。
```
$ sudo netstat -aple | grep ntp
udp        0      0 enlightened.local:ntp   *:*                                 root       17430       1789/ntpd       
udp        0      0 localhost:ntp           *:*                                 root       17429       1789/ntpd       
udp        0      0 *:ntp                   *:*                                 root       17422       1789/ntpd       
udp6       0      0 fe80::216:36ff:fef8:ntp [::]:*                              root       17432       1789/ntpd       
udp6       0      0 ip6-localhost:ntp       [::]:*                              root       17431       1789/ntpd       
udp6       0      0 [::]:ntp                [::]:*                              root       17423       1789/ntpd       
unix  2      [ ]         DGRAM                    17418    1789/ntpd
```
因此，我们发现 ntp 服务器正在运行。 http 或 smtp 或任何您正在寻找服务都可以使用 `grep` 来查看。

好吧，这就是 netstat 的用途。如果您正在寻找更高级的信息或想深入了解，请阅读 netstat 手册（man netstat）。

并在下面的评论框中留下您的反馈和建议。

最后更新时间：2014年12月25日