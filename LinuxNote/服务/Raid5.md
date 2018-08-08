# Raid5 卷制作

## 题目

创建RAID5

> 新建三个5G的云硬盘，挂载到主机
>
> 使用 mdadm 将两块云硬盘创建RAID5阵列，设备文件名为md0；
>
> 将新建的 RAID5 格式化为 XFS 文件系统，编辑 /etc/fstab 文件通过 UUID 的方式实现系统启动时能够自动挂载到 /data/web_data 目录。

挂载硬盘后查看现在磁盘情况

```bash
➜ root@localhost  ~  fdisk -l
Disk /dev/sda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000b6f60

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    41943039    19921920   8e  Linux LVM

Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```


```bash
Disk /dev/sdc: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```


```bash
Disk /dev/sdd: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```


可以看到 `sdb` `sdc` `sdd`三块硬盘已被添加

下载 mdadm 工具

```bash
➜ root@localhost  ~  yum install mdadm -y
```


首先进行分区 将新添加的三块磁盘都进行分区 格式调整为 `Linux raid autodetect`

```bash
(ssh) root@blog : ~
[0] # fdisk /dev/sdd
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x3d83b230.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p):
Using default response p
Partition number (1-4, default 1):
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
Partition 1 of type Linux and of size 5 GiB is set

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): fd
Changed type of partition 'Linux' to 'Linux raid autodetect'

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```


这个时候在查看三个磁盘的变化

```bash
(ssh) root@blog : ~
[0] #  mdadm -E /dev/sd[b-d]
/dev/sdb:
   MBR Magic : aa55
Partition[0] :     10483712 sectors at         2048 (type fd)
/dev/sdc:
   MBR Magic : aa55
Partition[0] :     10483712 sectors at         2048 (type fd)
/dev/sdd:
   MBR Magic : aa55
Partition[0] :     10483712 sectors at         2048 (type fd)
```


一行命令创建 Raid5 盘

```bash
(ssh) root@blog : ~
[0] # mdadm -C  /dev/md0 --level=5 --raid-devices=3 /dev/sdb1 /dev/sdc1 /dev/sdd1
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```


检查并确认

```bash
(ssh) root@blog : ~
[0] # cat /proc/mdstat                                                           
Personalities : [raid6] [raid5] [raid4]
md0 : active raid5 sdd1[3] sdc1[1] sdb1[0]
      10473472 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/3] [UUU]

unused devices: <none>
```


完成之后 使用下面命令进行检查

```bash
(ssh) root@blog : ~
[0] # mdadm -D /dev/md0
/dev/md0:
           Version : 1.2
     Creation Time : Tue May 22 02:18:35 2018
        Raid Level : raid5
        Array Size : 10473472 (9.99 GiB 10.72 GB)
     Used Dev Size : 5236736 (4.99 GiB 5.36 GB)
      Raid Devices : 3
     Total Devices : 3
       Persistence : Superblock is persistent

       Update Time : Tue May 22 02:19:02 2018
             State : clean
    Active Devices : 3
   Working Devices : 3
    Failed Devices : 0
     Spare Devices : 0

            Layout : left-symmetric
        Chunk Size : 512K

Consistency Policy : resync

              Name : blog:0  (local to host blog)
              UUID : cad17787:226b56a0:12679ff7:66f10831
            Events : 18

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       33        1      active sync   /dev/sdc1
       3       8       49        2      active sync   /dev/sdd1
```


格式化卷并输出UUID

```bash
(ssh) root@blog : ~
[0] # mkfs.xfs /dev/md0
meta-data=/dev/md0               isize=512    agcount=16, agsize=163712 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=2618368, imaxpct=25
         =                       sunit=128    swidth=256 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=8 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

(ssh) root@blog : ~
[0] # blkid /dev/md0
/dev/md0: UUID="b66c2f32-eb8e-4814-992b-27ad8de09363" TYPE="xfs"
```


更改fstab文件

```bash
(ssh) root@blog : ~
[0] # cat /etc/fstab

#
# /etc/fstab
# Created by anaconda on Tue May 22 01:50:46 2018
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=77e3ccce-c35c-4b30-ab80-b967af6f00c6 /boot                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
UUID=b66c2f32-eb8e-4814-992b-27ad8de09363  /data/web_data xfs  defaults        0 0
```
