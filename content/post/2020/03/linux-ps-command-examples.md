---
title: 14 个重要的 Linux ps 命令实战
date: 2020-03-24T08:01:37+08:00
draft: false
comments: true
tags: 
- Linux
---

> 作者：Pungki Arianto
> 
> 原文网址：https://linoxide.com/how-tos/linux-ps-command-examples/

Linux ps 命令是用于捕获系统上当前进程的内置工具。它将一次捕获系统状况。如果您想实时进行刷新，可以使用 top 命令。

在本教程中，我将向您展示 ps 命令的常见实际示例，该示例有助于您的日常linux系统管理。

## 1. 不带任何选项运行 ps
这是非常基本的 ps 用法。只需在控制台上键入 ps 即可查看其结果。
```
root@ParkW:~# ps
  PID TTY          TIME CMD
28269 pts/1    00:00:00 bash
28307 pts/1    00:00:00 ps
```

默认情况下，它将向我们显示 4 列信息。

- PID 是运行命令（CMD）的进程 ID
- TTY 是运行命令的地方
- TIME 是运行命令时 CPU 使用了多少时间
- CMD 是进程运行的命令

这里的信息展示是**无序**的。

## 2. 显示当前所有进程
为此，我们可以使用 `-a` 选项。我们可以猜到, `-a` 是 `all` 的简写。选项 `x` 将显示所有进程，即使当前进程也未与任何TTY（终端）相关联
```
$ ps -ax
```

该结果可能会比较多。为了使其更易于阅读，请将其与 `less` 命令结合使用。
```
$ ps -ax | less
```

```
  PID TTY      STAT   TIME COMMAND
    1 ?        Ss     0:12 /sbin/init
    2 ?        S      0:00 [kthreadd]
    3 ?        S      1:02 [ksoftirqd/0]
    5 ?        S<     0:00 [kworker/0:0H]
    7 ?        S     28:50 [rcu_preempt]
    8 ?        S      0:00 [rcu_sched]
    9 ?        S      0:00 [rcu_bh]
   10 ?        S      0:04 [migration/0]
   11 ?        S      0:04 [migration/1]
   12 ?        S      0:09 [ksoftirqd/1]
   14 ?        S<     0:00 [kworker/1:0H]
   15 ?        S      0:04 [migration/2]
   16 ?        S      0:09 [ksoftirqd/2]
   18 ?        S<     0:00 [kworker/2:0H]
   19 ?        S      0:04 [migration/3]
   20 ?        S      0:09 [ksoftirqd/3]
:
```

## 3. 按用户过滤进程
在某些情况下，我们可能希望按用户筛选进程。为此，我们可以使用 `-u` 选项。假设我们要查看用户 `root` 运行哪些进程。所以命令如下：
```
$ ps -u root
```

```
root@ParkW:~# ps -u root
  PID TTY          TIME CMD
    1 ?        00:00:12 init
    2 ?        00:00:00 kthreadd
    3 ?        00:01:02 ksoftirqd/0
    5 ?        00:00:00 kworker/0:0H
    7 ?        00:28:50 rcu_preempt
```

## 4. 按 CPU 或内存使用情况过滤进程
您可能希望看到的另一件事是按 CPU 或内存使用情况过滤结果。这样，您可以获取哪些进程消耗了资源的信息。为此，我们可以使用 `aux` 选项。这是一个例子：
```
$ ps -aux | less
```

```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1   3264  2460 ?        Ss    3月12   0:12 /sbin/init
root         2  0.0  0.0      0     0 ?        S     3月12   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S     3月12   1:02 [ksoftirqd/0]
root         5  0.0  0.0      0     0 ?        S<    3月12   0:00 [kworker/0:0H]
root         7  0.1  0.0      0     0 ?        S     3月12  28:51 [rcu_preempt]
root         8  0.0  0.0      0     0 ?        S     3月12   0:00 [rcu_sched]
root         9  0.0  0.0      0     0 ?        S     3月12   0:00 [rcu_bh]
root        10  0.0  0.0      0     0 ?        S     3月12   0:04 [migration/0]
root        11  0.0  0.0      0     0 ?        S     3月12   0:04 [migration/1]
root        12  0.0  0.0      0     0 ?        S     3月12   0:09 [ksoftirqd/1]

```
显示所有信息

由于结果可以在很长的列表中，因此我们可以将pipe较少的命令转换为ps命令。解释结果中的每个术语。

1. `USER - Username` ：与进程关联的用户名。
2. `PID - Process ID` ：分配给进程的唯一数字标识符。
3. `%CPU - Percentage of CPU` ：CPU 使用时间的百分比（CPU 总时间除以进程已运行的时间长度）。
4. `%MEM - Percentage of RAM Memory` 内存使用百分比（已用内存除以可用总内存）。
5. `VSZ - Virtual Memory Size` ：虚拟内存中进程的大小, 以 KiB 表示。
6. `RSS - Resident Set Size`。
7. `TTY - Terminal controlling the process`。
8. `STAT - Process State - Possible values`。
    * R - Running。
    * S - Sleeping（可能会中断）。
    * D - Sleeping（可能不会被打断）- 用于表示进程正在处理输入/输出。
    * T - Stopped or being traced。
    * Z - Zombie or "hung" process. - 僵尸或“挂起”进程。
9. `START` ：进程开始的日期或时间。
10. `TIME` ：该进程以及该进程启动的子进程使用累积的CPU时间。
11. `COMMAND` ：启动进程的命令。

默认情况下，结果是无序的。如果要按特定列排序，可以将 `--sort` 选项添加到 ps 命令中。

按最高 `CPU utilization` 升序排列

```
$ ps -aux --sort -pcpu | less
```

```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      1116  2.3  0.8  31916 18036 ?        S     3月12 410:07 /usr/bin/python /usr/local/bin/xxx_serviceA
root     14106  1.4  2.2 199660 45748 ?        Sl    3月23  15:16 /usr/bin/python /usr/local/bin/xxx_serviceB
mongodb   5844  0.7  1.9 236996 39820 ?        Ssl   3月16  82:20 /usr/bin/mongod --config /etc/mongodb.conf
mysql      721  0.4  3.9 349636 82448 ?        Ssl   3月12  70:29 /usr/sbin/mysqld
root     14095  0.4  2.6  74296 55280 ?        S     3月23   4:43 /usr/bin/python /usr/local/bin/xxx_serviceC
redis      787  0.3  0.1  26836  2332 ?        Ssl   3月12  66:04 /usr/bin/redis-server 0.0.0.0:6379
```

按最高 `Memory utilization` 升序排序
```
$ ps -aux --sort -pmem | less
```

```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root       930  0.2 21.2 1213260 437348 ?      Sl    3月12  40:13 /root/xxx/javaService start
mysql      721  0.4  3.9 349636 82448 ?        Ssl   3月12  70:31 /usr/sbin/mysqld
root     14099  0.0  3.1 116364 64776 ?        S     3月23   0:46 /usr/bin/python /usr/local/bin/xxx_Service
root     14095  0.4  2.6  74296 55280 ?        S     3月23   4:44 /usr/bin/python /usr/local/bin/xxx_ServiceB
```

或者我们可以将其组合为一个命令，并仅显示结果的前十位：
```
$ ps -aux --sort -pcpu,+pmem | head -n 10
```


## 5. 前十大内存消耗的进程
这显示了系统上运行的前 10 个内存消耗进程。查看哪些进程最消耗内存是很有用的。

```
Options:
--sort spec specifies sorting order. Sorting syntax is [+|-]key[,[+|-]key[,...]]. Choose a multi-letter key from the STANDARD FORMAT SPECIFIERS section. The "+" is optional since default direction is increasing numerical or lexicographic order. Identical to k. For example: ps jax --sort=uid,-ppid,+pid
```

```
# ps -auxf | sort -nr -k 4 | head -10
root 3025 3.2 13.0 1600004 245276 ? Sl 10:00 2:01 \_ /usr/bin/gnome-shell
root 2261 0.5 1.8 188108 34540 tty1 Rs+ 09:49 0:25 \_ /usr/bin/Xorg :0 -background none -verbose -auth /run/gdm/auth-for-gdm-fTksZM/database -seat seat0 -nolisten tcp vt1
root 3089 0.0 1.2 1060728 22608 ? Sl 10:00 0:00 /usr/bin/nautilus --no-default-window
root 2957 0.0 1.2 998384 23244 ? Sl 10:00 0:00 \_ /usr/libexec/gnome-settings-daemon
root 3482 0.0 1.0 632688 19344 ? Rl 10:07 0:02 /usr/libexec/gnome-terminal-server
root 3181 0.1 1.0 349960 20280 ? S 10:00 0:07 /usr/bin/vmtoolsd -n vmusr
root 802 0.0 0.9 550008 18036 ? Ssl 09:49 0:01 /usr/bin/python -Es /usr/sbin/tuned -l -P
root 950 0.0 0.8 102312 15580 ? S 09:49 0:00 \_ /sbin/dhclient -d -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-eno16777736.pid -lf /var/lib/NetworkManager/dhclient-42456ebc-a752-4950-9adf-1b8bc29f43eb-eno16777736.lease -cf /var/lib/NetworkManager/dhclient-eno16777736.conf eno16777736
root 3154 0.0 0.8 898940 15300 ? Sl 10:00 0:00 /usr/libexec/evolution-calendar-factory
root 3020 0.0 0.8 551480 16420 ? Sl 10:00 0:00 /usr/libexec/goa-daemon
```
![](http://oss.yuguo.im/blog/202003/ps-memory.webp)

## 6. 按名称或进程ID过滤进程
为此，我们可以使用 `-C` 选项, 关键字放在它后面。假设我们要显示名为 getty 的进程。我们可以输入：
```
$ ps -C getty
```

```
root@ParkW:~# ps -C getty
  PID TTY          TIME CMD
  628 ttySAC0  00:00:00 getty
```

如果要显示有关结果的更多详细信息，可以添加 `-f` 选项, 以在完整格式列表中显示它。上面的命令如下所示：
```
$ ps -f -C getty
```

```
root@ParkW:~# ps -f -C getty
UID        PID  PPID  C STIME TTY          TIME CMD
root       628     1  0  3月12 ttySAC0 00:00:00 /sbin/getty 115200 ttySAC0
```


## 7. 通过进程的线程过滤进程
如果我们需要了解特定进程的线程，可以使用 `-L`选项, 其后跟进程 ID（PID）。这是一个实际的例子：
```
$ ps -L 1213
```

可以看到，PID保持相同的值，但是显示线程数的LWP显示不同的值。

## 8. 显示特定进程 ID 的所有线程
这将显示特定进程pid的所有线程。

```
Options:
-L show threads possibly with LWP and NLWP columns.

# ps -Lf -p 3482
UID PID PPID LWP C NLWP STIME TTY TIME CMD
root 3482 1 3482 0 4 10:07 ? 00:00:03 /usr/libexec/gnome-terminal-server
root 3482 1 3483 0 4 10:07 ? 00:00:00 /usr/libexec/gnome-terminal-server
root 3482 1 3484 0 4 10:07 ? 00:00:00 /usr/libexec/gnome-terminal-server
root 3482 1 3487 0 4 10:07 ? 00:00:00 /usr/libexec/gnome-terminal-server
```

## 9. 显示父进程的子进程
这将显示一个进程的所有子进程，并且对于找出从该主要进程中派生了哪些子进程非常有用。

```
# ps -o pid,pcpu,pmem,uname,comm -C apache2
PID %CPU %MEM USER COMMAND
2642 0.0 3.4 www-data apache2
4185 0.0 3.6 www-data apache2
4186 0.0 3.3 www-data apache2
4187 0.0 3.3 www-data apache2
5359 0.0 3.3 www-data apache2
13343 0.0 3.5 www-data apache2
17228 0.0 3.5 www-data apache2
21037 0.0 3.4 www-data apache2
```

## 10. 分层结构中显示进程
有时我们希望以分层形式查看进程。为此，我们可以使用 `-axjf` 选项。
```
$ ps -axjf
```

![按层次显示](http://oss.yuguo.im/blog/202003/ps_axjf.webp)

或者，我们可以使用的另一个命令是 `pstree`

$ pstree

![按层次显示信息](http://oss.yuguo.im/blog/202003/pstree.webp)

## 11. 显示进程持续运行时间
这将显示一个进程在系统上运行了多长时间。
```
# ps -e -o pid,comm,etime | grep mysql
3107 mysqld_safe 18-07:01:53
3469 mysqld 18-07:01:52
```

我们甚至可以使用以下两个关键字来查找进程实时的正常运行时间。

- `etime`：自进程启动以后经过的时间，格式为 `[[DD-]hh:]mm:ss`。
- `etimes`：自进程启动以后经过的时间（以秒为单位）。

首先，您需要找出进程的 PID。以下命令显示 apache2 的 PID。
```
# pidof apache2
21774
```

现在，我们可以使用以下命令查找此进程已运行多长时间：
```
# ps -p 21774 -o etime
ELAPSED
2-23:31:39
```

您还可以使用 etimes 关键字查看运行时间（以秒为单位），如下所示：
```
# ps -p 21774 -o etimes
ELAPSED
257895
```

## 12. 显示安全信息
如果要查看谁当前登录到您的服务器，可以使用 ps 命令查看它。我们可以使用一些选项来满足我们的需求。这里有些例子 ：

```
$ ps -eo pid,user,args
```
`-e` 选项将显示所有进程，同时 `-o` 选项将控制输出。Pid, User 和 Args 将向您显示 进程 ID, 运行应用的用户和正在运行的应用。

![显示安全信息](http://oss.yuguo.im/blog/202003/ps_security_1.webp)

可以与 `-e` 选项一起使用的关键字/用户定义格式为 `args，cmd，comm，command，fname，ucmd，ucomm，lstart，bsdstart and start`。

现在，ps命令可以通过以下命令将 pid 映射到它们各自的systemd单元。
```
$ ps -e -o pid，unit，cmd
```

## 13. 以用户格式显示以 root 身份运行的每个进程（真实和有效ID）
系统管理员可能想查看 root 正在运行哪些进程以及与之相关的其他信息。使用ps命令，我们可以通过以下简单命令进行操作：
```
$ ps -U root -u root u
```

`-U` 参数将通过实际用户 ID（RUID）选择。它选择真实用户名或 ID 在用户列表中的进程。真实的用户 ID 标识创建进程的用户。

而 `-u` 参数将通过有效用户ID（EUID）选择

最后一个参数 `u` 将以面向用户的格式显示输出，其中包含 `User, PID, %CPU, %MEM, VSZ, RSS, TTY, STAT, START, TIME and COMMAND` 列。

这是上面命令的输出。

![显示真实有效的用户ID](http://oss.yuguo.im/blog/202003/ps_root_real_effective_ID.webp)

## 14. 在实时进程查看器中使用 PS

ps 将显示有关系统中发生的情况的报告。结果将是静态报告。
假设我们要像上面第 4 点那样按CPU和内存使用情况筛选进程。我们希望报告每1秒更新一次。我们可以组合 `watch` 命令在  Linux 上做到这一点。

这是命令：
```
$ watch -n 1 'ps -aux --sort -pmem,-pcpu'
```

![](http://oss.yuguo.im/blog/202003/watch-ps-1.png)

如果您认为报告太长，我们可以将其限制为前 20 个流程，例如：我们可以添加 `head` 命令来做到这一点。
```
$ watch -n 1 'ps -aux --sort -pmem,-pcpu | head -n 20'
```

![](http://oss.yuguo.im/blog/202003/watch-ps-20.png)

这位现场记者当然不像 top 或 htop。但是使用 ps 进行实时报告的优点是您可以自定义字段。您可以选择要查看的字段。

例如，如果只需要显示 pungki 用户，则可以将命令更改为如下所示：
```
$ watch -n 1 'ps -aux -U pungki u --sort -pmem,-pcpu | head -n 20'
```

![](http://oss.yuguo.im/blog/202003/ps_watch_3.webp)
对于更多文档，您可以在 Linux 控制台上键入 `man ps` 来探索更多选项。