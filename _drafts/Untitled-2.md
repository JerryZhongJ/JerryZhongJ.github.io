---
title: Manjaro + Windows 双系统配置
tags: ["Disk", "MBR", "GPT", "Arch Linux"]
---


<!--more-->
## 分区

### 簇和扇区
**簇**:  
> 簇（Data cluster）或者分配单元（allocation unit）是操作系统中磁盘文件存储管理的单位，可为一个或多个物理扇区组成，由格式化时选定文件系统而定。**簇是操作系统所使用的逻辑概念，而非磁盘的物理特性**。[^cluster]
[^cluster]: <https://zh.wikipedia.org/wiki/%E7%B0%87>
**扇区**: 
> 扇区（英文：sector）是磁盘或光盘上一个磁道的分区[^sector]

[^sector]: <https://zh.wikipedia.org/wiki/%E7%A3%81%E7%9B%98%E6%89%87%E5%8C%BA>

![](/assets/posts_ref/Manjaro%20+%20Windows/disk.svg)
其中C是一个扇区, B是一个簇.

### 卷和分区
**分区**:  
> Disk partitioning or disk slicing[1] is the creation of one or more regions on secondary storage, so that each region can be managed separately.[2] These regions are called partitions. **It is typically the first step of preparing a newly installed disk, before any file system is created.**

硬盘分区先于文件系统!

> The disk stores the information about the partitions' locations and sizes in an area known as the **partition table** that **the operating system reads before any other part of the disk**.

分区可以看作是物理层面的行为!

分区的意义:  
1. Separating user data from system data can **prevent** the system partition **from becoming full and rendering the system unusable**. 
2. Partitioning can also make **backing up easier**.

分区表有: MBR partition table, GUID partion table(GPT), Apple partition map(APM).
[^partition]

[^partition]: <https://en.wikipedia.org/wiki/Disk_partitioning>

**卷**:
> A Volume is a logical abstraction from physical storage.[^volume]
> A Disk may contain one or multiple partitions.
> A Volume may span multiple disks.

卷是逻辑概念, 是基于分区之上的抽象.

硬盘有好多种工作模式，其中在基本磁盘模式下，卷与分区没有什么根本区别，可以认为一个分区就是一个卷。Windows7以后，将分区和简单卷都标记为了简单卷。

动态磁盘的特征是：磁盘容量可以调整，不会丢失数据，一个卷可以包容几个物理磁盘。有5种卷类型: 简单卷, 跨区卷, 带区卷, 镜像卷, RAID-5卷.[^volume2]


[^volume]: <https://superuser.com/questions/1340300/distinction-between-volume-and-partition-terminology>
[^volume]: <http://www.techwander.cn/Wordpress/windows%E5%88%86%E5%8C%BA%E5%92%8C%E5%8D%B7%E7%9A%84%E5%8C%BA%E5%88%AB/>

### MBR
![](/assets/posts_ref/Manjaro%20+%20Windows/mbr.png)

MBR(Master Boot Record) 主分区引导记录. 主引导扇区是整个硬盘的第一扇区（主分区的第一个扇区), MBR就保存在主引导扇区中. 这个扇区里还包含了硬盘分区表DPT（Disk Partition Table），和结束标志字（Magic number）. 扇区总计512字节，MBR占446字节.

DPT只有4个分区项, 最多允许有4个主分区, 或者3个主分区+1个扩展分区. 最高只支持2TB.

主分区只有一个文件系统. 一般引导文件如bootmgr, ntldr要求在第一主分区. 有些BIOS也对第一主分区有要求. 这个第一主分区一般就是C盘.

一个硬盘只能由一个扩展分区, 但是Windows会再分为多个逻辑分区, 给每个逻辑分区分配一个盘符. GPT没有扩展分区和逻辑分区, 全都是主分区.

### GPT

### 挂载
> Mounting is a process by which the operating system **makes files and directories on a storage device** (such as hard drive, CD-ROM, or network share) **available** for users to access **via** the computer's **file system**.

挂载就是把存储设备的文件系统(文件目录结构)纳入到计算机的文件系统中, 使得用户能够通过路径的方式访问到里面的文件.
### Windows的挂载
Windows的挂载就是分配盘符. 我们插入U盘时,就是挂载一个存储设备, 此时就多了一个盘符. 我们通过这个盘符访问U盘里的文件. 卸载后, 盘符就回收了.

### Linux的挂载
用Windows的方式理解Linux的挂载可能会有点困难. 在Windows里面, 一个盘符就相当于根目录, 盘符后面的路径表示在文件在这个分区里面的位置. 

物理硬盘有自己的文件系统, 尤其自己的目录结构. Linux的挂载就是把这个硬盘分区的文件系统(or part of it)映射到逻辑的文件目录上. 通过这个文件目录来访问物理文件系统中的文件. 根目录必然挂载了一个硬盘分区.

挂载可以重叠, 比如我把disk1都挂载到根目录上, /home是有内容的, 在disk1中. 但是我又把新的硬盘disk2挂载到/home上, 此时原来/home的内容会被隐藏, 显示disk2的文件, 写入也写进disk2. 但是把disk2卸载后, 原来在disk1中的内容又会显现出来. 同理, 两个硬盘的文件系统可以挂载到同一个挂载点上.
*假如在挂载点被隐藏会怎样?*
[^mounting]: <https://www.zhihu.com/question/266907637>

与"一切设备皆文件"区分. 硬盘设备会在/dev下有一个文件/dev/sdb, 但是挂载不是挂载一个硬盘!是挂载它的文件系统, 让它的内容可以访问!

## UEFI 和 BIOS

### 
## 前期准备
关闭混合休眠




## linux分区
### 文件系统选择

## grub

## Linux 访问 Windows文件