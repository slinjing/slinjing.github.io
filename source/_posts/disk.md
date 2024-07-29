---
title: 磁盘分区 # 必选-文章标题
date: 2020-05-07 00:00:00    # 必选-文章创建日期
updated:           # 可选-文章更新日期
tags:              # 可选-文章标          
  - Linux
categories: Linux  # 可选-文章分类
keywords:       # 可选-文章关键字
description:    # 可选-文章描述
top_img:        # 可选-文章顶部图片
comments:       # 可选-显示文章评论模块(默认 true)
cover:  '/img/linux.jpg' # 可选-文章缩略图
---

## 概述
在Linux系统中，磁盘是通过分区来使用的。分区是将一个硬盘划分成几个逻辑部分来使用，在每个分区中可以存储不同的文件系统。因此，在挂载磁盘之前，需要先对磁盘进行分区。

## 分区格式
MBR和GPT是两种常见的磁盘分区表格式。GPT格式较新，具有较多优势，包括：
>支持更大的磁盘容量。MBR最大支持2.2TB，而GPT支持高达9.44ZB。
支持更多分区。MBR最多支持4个主分区，而GPT支持128个主分区。
更高的安全性。GPT用CRC校验机制和备份分区表保护分区表数据的完整性，而 MBR不使用。

MBR是较旧的格式，但仍被广泛使用。它具有以下优势：
>与旧系统兼容。MBR与所有版本的 Windows和大多数版本的Linux兼容。
简单易用。MBR的结构相对简单，易于理解和使用。

## 磁盘分区
下面分别对MBR和GPT进行演示，需要用到fdisk和gdisk两种工具。
- fdisk：支持MBR分区表
- gdisk：支持GPT分区表

在分区前，先使用`lsblk`命令查看磁盘情况：
```shell
$ lsblk
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda               8:0    0 447.1G  0 disk /data
sdb               8:16   0 931.5G  0 disk
├─sdb1            8:17   0     1G  0 part /boot
└─sdb2            8:18   0 930.5G  0 part
  ├─centos-root 253:0    0    50G  0 lvm  /
  ├─centos-swap 253:1    0  11.8G  0 lvm
  └─centos-home 253:2    0 868.8G  0 lvm  /home
sdc               8:32   0 931.5G  0 disk
```

### fdisk
这里就使用sdc来演示，使用`fdisk /dev/sdc`命令开始分区：
```shell
$ fdisk /dev/sdc

The device presents a logical sector size that is smaller than
the physical sector size. Aligning to a physical sector (or optimal
I/O) size boundary is recommended, or performance may be impacted.
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help):
```
执行命令后会看到上面的提示信息，此时可以选择的输入有以下：
| 选项 | 说明 |
|:-------:|:-------:|
| d | 删除分区 |
| l | 列出已知的分区类型 |
| n | 创建分区 |
| p | 打印分区表 |
| d | 删除分区 |
| q | 不保存退出 |
| w | 保存并退出 |
| m | 帮助信息 |

输入n后也有两个选项，其中p代表主分区，e代表扩展分区，这里选择p。
```shell
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-1953525167, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-1953525167, default 1953525167):
Using default value 1953525167
Partition 1 of type Linux and of size 931.5 GiB is set
```
输入p回车后会提示输入分区编号、开始扇区和结束扇区等信息，如果分配整块硬盘的华直接回车即可。分区编号默认是1、选择开始扇区默认是2048、结束扇区，不输入的话就是分配整改扇区。需要调整的话输入自己要分配的容量，如：+500G。完成后输入w保存并退出，看到下面提示就说明成功了。
```shell
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

### gdisk
gdisk工具需要先安装，使用命令`yum -y install gdisk`，它与fdisk使用方式类似，gdisk默认创建的GPT分区。
```shell
$ gdisk  /dev/sdc
GPT fdisk (gdisk) version 0.8.10

Partition table scan:
  MBR: protective
  BSD: not present
  APM: not present
  GPT: present

Found valid GPT with protective MBR; using GPT.

Command (? for help): n
Partition number (1-128, default 1): p
First sector (34-1953525134, default = 2048) or {+-}size{KMGTP}:
Last sector (2048-1953525134, default = 1953525134) or {+-}size{KMGTP}:
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300):
Changed type of partition to 'Linux filesystem'

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): y
OK; writing new GUID partition table (GPT) to /dev/sdc.
The operation has completed successfully.
```
查看分区表
```shell
$ gdisk -l /dev/sdc
GPT fdisk (gdisk) version 0.8.10

Partition table scan:
  MBR: protective
  BSD: not present
  APM: not present
  GPT: present

Found valid GPT with protective MBR; using GPT.
Disk /dev/sdc: 1953525168 sectors, 931.5 GiB
Logical sector size: 512 bytes
Disk identifier (GUID): 7C0FF1A3-AECC-4421-A733-CA1CD6534316
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 1953525134
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048      1953525134   931.5 GiB   8300  Linux filesystem
```

## 格式化磁盘
完成分区后下一步格式化磁盘，格式化时支持多种文件系统格式，每种格式都有其特定的用途和优势。常见的Linux文件系统格式有ext4、xfs、Btrfs等，这里分别对ext4、xfs进行演示：

### ext4
```shell
$ mkfs -t ext4 /dev/sdc
mke2fs 1.42.9 (28-Dec-2013)
/dev/sdc is entire device, not just one partition!
Proceed anyway? (y,n) y
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
61054976 inodes, 244190646 blocks
12209532 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2392850432
7453 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
        102400000, 214990848

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

### xfs

```shell
$ mkfs.xfs -f /dev/sdc
meta-data=/dev/sdc               isize=512    agcount=4, agsize=61047662 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=244190646, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=119233, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

## 挂载
挂载磁盘使用`mount`命令，挂载磁盘之前，‌需要保证文件系统已经被创建，‌同时需要知道要挂载的设备和挂载点。‌

### 临时挂载
>xfs
```shell
$ mkdir /hdd
$ mount -t xfs /dev/sdc /hdd
$ df -hT
Filesystem              Type      Size  Used Avail Use% Mounted on
/dev/sdc                xfs       932G   33M  932G   1% /hdd
```

>ext4
```shell
$ mount -t ext4 /dev/sdc /hdd
$ df -hT
Filesystem              Type      Size  Used Avail Use% Mounted on
/dev/sdc                ext4      917G   77M  871G   1% /hdd
```

### 永久挂载
首先使用`blkid`命令拿到UUID：
```shell
$ blkid /dev/sdc
/dev/sdc: UUID="bf499f8d-bfce-4ea0-b71a-4a9328197292" TYPE="ext4"
```
然后将挂载信息写入到/etc/fstab文件中，最后使用`mount -a`命令刷新，这样关机之后下次重启也会自动挂载。
```shell
$ echo "UUID=bf499f8d-bfce-4ea0-b71a-4a9328197292 /hdd ext4 defaults 0 0" >> /etc/fstab
$ mount -a
```
也可以不使用UUID，直接用下面的形式进行挂载：
```shell
$ echo "/dev/sdc /hdd  ext4 defaults 0 0" >> /etc/fstab
```
取消挂载使用`umount`命令：
```shell
$ umount /hdd
```

更改挂载权限的命令：
```shell
$ mount -o ro /dev/sdc /hdd   # 将挂载设备改为只读
$ mount -o rw /dev/sdc /hdd   # 将挂载设备改为可读写
```