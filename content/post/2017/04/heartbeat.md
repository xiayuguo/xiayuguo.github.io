---
title: 玩的就是“心跳”
date: 2017-04-18 09:49:26
tags:
- Python
- Socket
categories: 架构
---

### 心跳检测是什么？
这里的“心跳”，可不是男女之间心动啥的“砰砰砰”。
- 心跳检测是判断对方(设备，进程或其它网元)是否正常进行，一般采用定时发送简单的通讯包，如果长时间未收到对方响应，则判断对方已经挂掉。
- [心跳机制](http://www.baike.com/wiki/%E5%BF%83%E8%B7%B3%E6%9C%BA%E5%88%B6)是服务端和客户端检测对方是否在线的一种方式。
- 心跳包就是客户端定时发送简单的信息给服务端告诉它我还在，服务端视情况而定给客户单返回合适信息，一般情况下是客户端给服务端发心跳包。

<!--more-->

### 为什么要用心跳机制？
- 服务端不能有效判断客户端是否在线。
- 心跳包可以用于长连接的保活和断线处理。

### 怎么代码实现心跳机制？
 1. 客户端每隔一个时间间隔发生一个探测包给服务器。
 2. 客户端发包时启动一个超时定时器。
 3. 服务器端接收到检测包，应该回应一个包。
 4. 如果客户机收到服务器的应答包，则说明服务器正常，删除超时定时器。
 5. 如果客户端的超时定时器超时，依然没有收到应答包，则说明服务器挂了。

#### Python代码实现心跳检测
> 具体代码参见[Github](https://github.com/hugoxia/Python/tree/master/heartbeat)

- server

```python
#!/usr/bin/python
#encoding:utf-8

import socket, sys, json
from thread import *
BUF_SIZE = 4096

HOST = socket.gethostname()
PORT = 7878
try:
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
except socket.error, e:
    print "Error creating socket: %s" % e
    sys.exit()

try:
    server.bind((HOST, PORT))
except socket.error:
    print "Bind failed!"
    sys.exit()
print "Socket bind complete"

server.listen(10)
print "Socket now listening"


def clientthread(coon):
    coon.send("Welcome to the server!")
    while True:
        try:
            data = coon.recv(BUF_SIZE)
            data_loaded = json.loads(data)
            print "ip: {}|status: {}|pid: {}".format(
                str(data_loaded['ip']), data_loaded['status'], str(data_loaded['pid']
            )
        except socket.error:
            print "One Client (IP: %s) Connected over!" % data_loaded['ip']
            break
    coon.close()


while True:
    coon, addr = server.accept()
    print "Connected with %s: %s " % (addr[0], str(addr[1]))
    start_new_thread(clientthread, (coon,))

server.close()
```

- client

```python
#!/usr/bin/python
#encoding:utf-8

import socket, sys, os
import time, json

host = socket.gethostname()  # 这里获得的是本地，视情况而定
port = 7878
BUF_SIZE = 4096

try:
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
except socket.error, e:
    print "Error creating socket: %s" % e
    sys.exit()

try:
    remote_ip = socket.gethostbyname(host)
except socket.gaierror:
    print "Hostname couldn't be resolved. Exciting"

    sys.exit()

try:
    client.connect((remote_ip, port))
    client.setblocking(0)   # set the socket is not blocking
    print "Socket connected to %s on ip %s" % (host, remote_ip)
except socket.gaierror, e:  #address related error
    print "connected to server error%s" % e
    sys.exit()

#send heart_beat
while True:
    host_name = socket.gethostname()
    data_to_server = {
        'ip': socket.gethostbyname(host_name), 
        'status': 'alive', 'pid': os.getpid()
    }
    data_dumped = json.dumps(data_to_server)
    try:
        client.sendall(data_dumped)
    except socket.error:
        print "Send failed!!"
        sys.exit()

    print 'I - ', os.getpid(), '- am alive.'
    time.sleep(50)
client.close()
```
