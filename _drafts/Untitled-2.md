---
title: Manjaro + Windows 双系统配置
tags: ["Disk", "MBR", "GPT", "Arch Linux"]
---



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

## MBR

### GPT


### 挂载

### 
## 前期准备
关闭混合休眠




## linux分区
### 文件系统选择

## grub

## Linux 访问 Windows文件