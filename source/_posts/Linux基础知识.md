---
title: Linux基础知识
date: 2020-10-28 12:05:45
tags: Linux
categories: 后端运维
comment: true
---

# Linux基础知识

Linux，全称GNU/Linux，是一种免费使用和自由传播的类UNIX操作系统，其内核由林纳斯·本纳第克特·托瓦兹于1991年10月5日首次发布，它主要受到Minix和Unix思想的启发，是一个基于POSIX和Unix的多用户、多任务、支持多线程和多CPU的操作系统。它能运行主要的Unix工具软件、应用程序和网络协议。它支持32位和64位硬件。Linux继承了Unix以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。Linux有上百种不同的发行版，如基于社区开发的debian、archlinux，和基于商业开发的Red Hat Enterprise Linux、SUSE、oracle linux等。

## 1.连接服务器

```bash
// 1. Xshell/SecureCRT……连接
输入用户名/密码

// 2. 命令行连接
// -p 后面接端口
// PORT：端口号（默认22）
// serverIP：服务器实例IP
// ~ 表示home目录

// 连接
$ ssh -p PORT root@serverIP 
$ ssh -p 22 root@140.143.236.***

// 输入密码
$ root@serverIP's password:

// 连接成功
$ [root@VM-0-15-centos ~]#

```

## 2.显示服务器发行版本信息

```bash
// 示例
$ LSB Version:    :core-4.1-amd64:core-4.1-noarch
$ Distributor ID: CentOS
$ Description:    CentOS Linux release 7.7.1908 (Core)
$ Release:        7.7.1908
$ Codename:       Core

// LSB：Linux Standard Base
$ lsb_release -a

// 如果-bash: lsb_release: command not found => 安装即可
$ yum -y install redhat-lsb-core-4.1-27.el7.centos.1.x86_64
```

## 3.服务器内核信息

```bash
uname: unix name

// 口令
$ uname -a

// 结果
$ Linux VM-0-15-centos 3.10.0-1062.18.1.el7.x86_64 #1 SMP Tue Mar 17 23:49:17 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

## 4.磁盘空间

```bash
df: disk free

// 查看口令
$ df

// 结果
Filesystem     1K-blocks     Used Available Use% Mounted on
devtmpfs          930176        0    930176   0% /dev
tmpfs             941096       24    941072   1% /dev/shm
tmpfs             941096      556    940540   1% /run
tmpfs             941096        0    941096   0% /sys/fs/cgroup
/dev/vda1       51473868 12049648  37226976  25% /
overlay         51473868 12049648  37226976  25% /var/lib/docker/overlay2/77da3edd538fa0654c1bdf44244a13ec55e901458aedcff550bff08ef2beda32/merged
overlay         51473868 12049648  37226976  25% /var/lib/docker/overlay2/7135b642c5bb06ea053d90c0ddd70c9339705e31b2fb5403fae2fddb097bce0e/merged
tmpfs             188220        0    188220   0% /run/user/0

// 显示文件系统的形式，并以人类可读的形式输出
$ df -Th

// 结果
Filesystem     Type      Size  Used Avail Use% Mounted on
devtmpfs       devtmpfs  909M     0  909M   0% /dev
tmpfs          tmpfs     920M   24K  920M   1% /dev/shm
tmpfs          tmpfs     920M  556K  919M   1% /run
tmpfs          tmpfs     920M     0  920M   0% /sys/fs/cgroup
/dev/vda1      ext4       50G   12G   36G  25% /
overlay        overlay    50G   12G   36G  25% /var/lib/docker/overlay2/77da3edd538fa0654c1bdf44244a13ec55e901458aedcff550bff08ef2beda32/merged
overlay        overlay    50G   12G   36G  25% /var/lib/docker/overlay2/7135b642c5bb06ea053d90c0ddd70c9339705e31b2fb5403fae2fddb097bce0e/merged
tmpfs          tmpfs     184M     0  184M   0% /run/user/0

```

## 5.目录结构

```bash
// 1.带隐藏目录
$ ls -la

// 2.不带隐藏目录
$ ll

// 3.不带隐藏目录结果
total 64
lrwxrwxrwx.   1 root root     7 Mar  7  2019 bin -> usr/bin
dr-xr-xr-x.   5 root root  4096 Sep 10 15:24 boot
drwxr-xr-x    2 root root  4096 Nov  5  2019 data
drwxr-xr-x   19 root root  3000 Sep 10 15:23 dev
drwxr-xr-x.  90 root root  4096 Nov  5 15:34 etc
drwxr-xr-x.   2 root root  4096 Apr 11  2018 home
lrwxrwxrwx.   1 root root     7 Mar  7  2019 lib -> usr/lib
lrwxrwxrwx.   1 root root     9 Mar  7  2019 lib64 -> usr/lib64
drwx------.   2 root root 16384 Mar  7  2019 lost+found
drwxr-xr-x.   2 root root  4096 Apr 11  2018 media
drwxr-xr-x.   2 root root  4096 Apr 11  2018 mnt
drwxr-xr-x.   4 root root  4096 Sep 10 15:41 opt
dr-xr-xr-x  101 root root     0 Sep 10 15:23 proc
dr-xr-x---.   8 root root  4096 Sep 10 16:43 root
drwxr-xr-x   27 root root  1000 Nov  5 15:34 run
lrwxrwxrwx.   1 root root     8 Mar  7  2019 sbin -> usr/sbin
drwxr-xr-x.   2 root root  4096 Apr 11  2018 srv
dr-xr-xr-x   13 root root     0 Nov  5 16:02 sys
drwxrwxrwt.   9 root root  4096 Nov  5 15:34 tmp
drwxr-xr-x.  13 root root  4096 Mar  7  2019 usr
drwxr-xr-x.  19 root root  4096 Mar  7  2019 var
```

| 目录 | 备注 |
|  ----  | ----  |
| /boot  | 系统启动相关的文件，如内核、initrd，以及grub（BootLoader） |
| /dev | Device(设备) 的缩写, 该目录下存放的是 Linux 的外部设备 |
| /etc  | 所有的系统管理所需要的配置文件和子目录 |
| /home | 用户的家目录，每一个用户的家目录通常默认为/home/USERNAME |
| /lib |  Library(库) 的缩写这个目录里存放着系统最基本的动态连接共享库 |
| /lost+found | 这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件 |
| /media | linux 系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux 会把识别的设备挂载到这个目录下 |
| /mnt | 系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在 /mnt/ 上，然后进入该目录就可以查看光驱里的内容了 |
| /opt | optional(可选) 的缩写，这是给主机额外安装软件所摆放的目录 |
| /proc |  Processes(进程) 的缩写，/proc 是一种伪文件系统（也即虚拟文件系统），存储的是当前内核运行状态的一系列特殊文件，这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息 |
| /root | 该目录为系统管理员，也称作超级权限者的用户主目录 |
| /run | 是一个临时文件系统，存储系统启动以来的信息 |
| /sbin | s 就是 Super User 的意思，是 Superuser Binaries (超级用户的二进制文件) 的缩写，这里存放的是系统管理员使用的系统管理程序 |
| /srv | 该目录存放一些服务启动之后需要提取的数据 |
| /sys | 当一个内核对象被创建的时候，对应的文件和目录也在内核对象子系统中被创建 |
| /tmp | temporary(临时) 的缩写这个目录是用来存放一些临时文件的 |
| /usr | unix shared resources(共享资源) 的缩写，这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于 windows 下的 program files 目录 |
| /usr/bin | 系统用户使用的应用程序 |
| /usr/sbin | 超级用户使用的比较高级的管理程序和系统守护程序 |
| /usr/src | 内核源代码默认的放置目录 |
| /var | variable(变量) 的缩写，这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件 |

## 6.CPU/内存/进程

Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况，类似于Windows的任务管理器

```bash
$ top

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 8779 root      20   0  642148  12120  10536 S  0.7  0.6  72:23.76 YDEdr
17036 polkitd   20   0 1274480 350136    648 S  0.7 18.6 627:58.60 mysqld
13273 root      20   0  573444  25984   1428 S  0.3  1.4  33:23.64 containerd
21596 root      20   0       0      0      0 S  0.3  0.0   0:00.01 kworker/0:0
28259 root      20   0  160604   9152   1772 S  0.3  0.5  54:06.52 barad_agent
28260 root      20   0  611276  13504   1984 S  0.3  0.7 247:08.11 barad_agent
    1 root      20   0  125456   3676   2292 S  0.0  0.2  10:11.12 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.57 kthreadd
    4 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H
    6 root      20   0       0      0      0 S  0.0  0.0   4:03.46 ksoftirqd/0
    7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
    8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh
    9 root      20   0       0      0      0 S  0.0  0.0  11:41.35 rcu_sched
   10 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 lru-add-drain
   11 root      rt   0       0      0      0 S  0.0  0.0   0:23.18 watchdog/0
   13 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kdevtmpfs
   14 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 netns
   15 root      20   0       0      0      0 S  0.0  0.0   0:01.59 khungtaskd
   16 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 writeback
   17 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kintegrityd
   18 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 bioset
   19 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 bioset
   20 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 bioset
```

## 7.常见命令

| 命令 | 备注 |
| ---- | ---- |
| cd | change directory |
| ls | list files |
| rm | remove |
| mkdir | make directory |
| rmdir | remove directory |
| touch | 修改文件或者目录的时间属性 |
| grep  | 查找文件里符合条件的字符串 |
| cp | copy file |
| mv | move file |
| whereis | 查找文件 |
| tree | 以树状图列出目录的内容 |
| zip | 压缩 |
| unzip | 解压 |
| gzip | 压缩 |
| gunzip | 解压 |
| tar | tape archive: 备份文件 |
| tar -czvf tar.gz a.c | 备份a.c为tar.gz |
| tar -tzvf tar.gz | 列出压缩文件内容 |
| tar -xzvf tar.gz | 解压 |
| ps | process status:显示当前进程的状态 |
| ps -ef \| grep x | 查看x进程 |
| kill | 删除执行中的程序或工作 |
| kill -9 pid | 强制杀死pid进程 |
| sudo | 以系统管理者的身份执行指令 |
| vi/vim | vim编辑器 |
| echo | 字符串的输出 |
| echo string >> test.txt | 在test.txt中添加string |
| echo string > test.txt | 用string覆盖test.txt内容 |
