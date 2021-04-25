---
title: Manjaro + Windows 双系统配置
tags: ["UEFI", "引导", "双系统", "Linux"]
cover: https://cdn.jsdelivr.net/gh/JerryZhongJ/Pictures/20210326221214.png
---

网上关于如何安装Windows+Linux的教程一查一大堆, 一般都是博客里面的. 但他们大都都只是记录了操作流程, 并没有对"为什么要这么做"有太多解释. 我不愿意就这样盲目的上手, 所以花了大量时间做前期的准备, 补充基础知识. 

所以这篇博客一反常态, 反而很少提及具体怎么做, 因为这种东西在网上找找就好了. 本篇博客重点在于解释, 以及把所有查到的资料汇总.
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

分区信息存在硬盘的分区表中, 分区可以看作是**物理层面**的行为!

分区的意义:  
1. Separating user data from system data can **prevent** the system partition **from becoming full and rendering the system unusable**. 
2. Partitioning can also make **backing up easier**.

分区表有: **MBR** partition table, GUID partion table(**GPT**), Apple partition map(APM).
[^diskpartition]

[^diskpartition]: <https://en.wikipedia.org/wiki/Disk_partitioning>

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



MBR(Master Boot Record) 主分区引导记录.   
每个扇区, 区块都被分配了一个逻辑块地址，即**LBA**. 以前是用**C**ylinder-**T**rack-**S**ector来表示.

主引导扇区是整个硬盘的第一扇区（主分区的第一个扇区), MBR就保存在主引导扇区中. 这个扇区里还包含了硬盘分区表DPT（Disk Partition Table），和结束标志字（Magic number）. 扇区总计512字节，MBR占446字节.

DPT只有4个分区项, 最多允许有4个主分区, 或者3个主分区+1个扩展分区. 最高只支持2TB.  
主分区只有一个文件系统. 一般引导文件如bootmgr, ntldr要求在第一主分区. 有些BIOS也对第一主分区有要求. 这个第一主分区一般就是C盘.  
一个硬盘只能由一个扩展分区, 但是Windows会再分为多个逻辑分区, 给每个逻辑分区分配一个盘符. GPT没有扩展分区和逻辑分区, 全都是主分区.

文件系统类型包含在分区表项的**分区类型**里. [^partition_type]

[^partition_type]: <https://en.wikipedia.org/wiki/Partition_type>; <https://www.win.tue.nl/~aeb/partitions/partition_types-1.html>
### GPT
GPT全称GUID分区表, 是UEFI规范的一部分.

所谓GUID是全局唯一标识符.

GPT的优点有:
1. 可管理空间达到18EB, 接近无限.
2. 分区数量没有限制.
3. 分区可以命名, 长72个字节.
   
### 挂载
> Mounting is a process by which the operating system **makes files and directories on a storage device** (such as hard drive, CD-ROM, or network share) **available** for users to access **via** the computer's **file system**.

挂载就是把存储设备的文件系统(文件目录结构)纳入到计算机的文件系统中, 使得用户能够通过路径的方式访问到里面的文件.
### Windows的挂载
Windows的挂载就是分配盘符. 我们插入U盘时,就是挂载一个存储设备, 此时就多了一个盘符. 我们通过这个盘符访问U盘里的文件. 卸载后, 盘符就回收了.

### Linux的挂载

物理硬盘有自己的文件系统, 即自己的目录结构. Linux的挂载就是把这个硬盘分区的文件系统(or part of it)映射到逻辑的文件目录上. 通过这个文件目录来访问物理文件系统中的文件. 如一个硬盘分区里有/file.txt, 我把硬盘分区挂载到/home上, 那么我就可以通过/home/file.txt来访问硬盘上的文件. 根目录必然挂载了一个硬盘分区.

挂载可以重叠, 比如我把disk1都挂载到根目录上, /home是有内容的, 在disk1中. 但是我又把新的硬盘disk2挂载到/home上, 此时原来/home的内容会被隐藏, 显示disk2的文件, 写入也写进disk2. 但是把disk2卸载后, 原来在disk1中的内容又会显现出来. 同理, 两个硬盘的文件系统可以挂载到同一个挂载点上. [^mounting]

*假如在挂载点被隐藏会怎样?*

[^mounting]: <https://www.zhihu.com/question/266907637>

与"一切设备皆文件"区分. 硬盘设备会在/dev下有一个文件/dev/sdb, 但是挂载不是挂载一个硬盘!是挂载它的文件系统, 让它的内容可以访问!

## 启动引导

### Legacy BIOS
Legacy BIOS 只支持MBR分区表.

BIOS的引导过程为:
1. 加电自检.
2. 读取主引导记录MBR, 转移控制权. MBR就在第一主分区的的第一个扇区, 也是整个硬盘的第一个扇区. MBR中有引导代码(第一阶段引导), 
3. MBR的引导代码会启动引导程序(第二阶段引导). 引导程序有GRUB, bootmgr. GRUB可以引导Linux和Windows, 但bootmgr只能引导Windows.

### UEFI

UEFI兼容MBR分区表, 支持GPT表.
![](/assets/posts_ref/Manjaro%20+%20Windows/UEFI_boot_process.png)


UEFI用一个东西叫做UEFI boot manager, 可以看作是一个启动菜单一样的东西. 在Linux, 我们可以用efibootmgr这个工具对它修改,添加,删除. 在Windows用EasyUEFI.
uefi boot entry存储在NVRAM中, 形如:
```shell
[root@system directory]# efibootmgr -v 
BootCurrent: 0002 
Timeout: 3 seconds 
BootOrder: 0003,0002,0000,0004 
Boot0000* CD/DVD Drive BIOS(3,0,00) 
Boot0001* Hard Drive HD(2,0,00) 
Boot0002* Fedora HD(1,800,61800,6d98f360-cb3e-4727-8fed-5ce0c040365d)File(\EFI\fedora\grubx64.efi) 
Boot0003* opensuse HD(1,800,61800,6d98f360-cb3e-4727-8fed-5ce0c040365d)File(\EFI\opensuse\grubx64.efi) 
Boot0004* Hard Drive BIOS(2,0,00)P0: ST1500DM003-9YN16G . 
[root@system directory]#
```
第二行说的是这个boot menu的停留时间.

UEFI会挨个尝试这些efi项. 其中Boot0000和Boot0004表示以BIOS兼容模式启动.  

Boot0001没有指示引导目标, 只说了引导哪个硬盘. 在这个情况下, UEFI会挨个查找EFI System Partition(ESP), 在ESP查找某个位置的某个文件. 位置和文件名根据机器架构而定. 命名规范为\EFI\BOOT\BOOT{machine type short-name}.EFI . 对于x86-64的机器, 就找\EFI\BOOT\BOOTx64.EFI. 这主要是用来从可移动设备引导,这样就不需要提前配置引导文件的具体位置了.

Boot0002和Boot0003表示用来引导永久OS的, 它指示出引导文件的具体位置, 即\EFI\opensuse\grubx64.efi. [^uefi]

[^uefi]: <https://www.happyassassin.net/posts/2014/01/25/uefi-boot-how-does-that-actually-work-then/>; <https://wiki.archlinux.org/index.php/Arch_boot_process#Under_UEFI>



### 双系统

我的情况是先安装了Windows, 再安装Linux, 此时已经有efi分区. 不需要重新创建分区，只需要挂载分区. 挂载到/boot/efi. [^efi_mount]

引导的话, 可以在UEFI引导双系统, 每次在UEFI处选择; 也可以都交给GRUB来引导, UEFI每次都启动GRUB. 但是前者要看硬件的UEFI怎么实现的, 有些不允许我们选择, 而且Windows可能存在一个问题, 就是每次Windows启动会自动将自己的EFI entry移到最上方[^windows_efi]. 因此我先尝试着用uefi, 若不行, 在研究如何让GRUB引导Windows.

Manjaro使用Calamares作为安装程序, 使用GRUB作为bootloader.
注意安全引导的问题.
[^dualsys]: <https://wiki.archlinux.org/index.php/EFI_system_partition_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E6%8C%82%E8%BD%BD%E5%88%86%E5%8C%BA>; <https://wiki.archlinux.org/index.php/Dual_boot_with_Windows#UEFI_systems>
[^windows_efi]: <https://askubuntu.com/questions/838780/windows-10-changes-uefi-boot-order-every-time>
[^efi_mount]: <https://wiki.manjaro.org/index.php/UEFI_-_Install_Guide#Setting_filesystem_mount_points>


### 安全启动

在开启安全启动后，在信任的组件运行结束后（如烧录在Flash上的UEFI固件PEI和DXE部分），固件接下来会要求所有运行应用和驱动都要有被认可的数字签名，否则不予执行。这些应用和驱动包括：Shell、扩展驱动和OS Loader。最后OS Loader也就是操作系统引导程序，它的本质是一个UEFI应用程序，它由操作系统厂商提供并签名。其后的操作系统的映像签名则由OS Loader来验证，由此构成一个信任链条，保证OS映像甚至内核驱动都是被签名验证过的。

Linux系统面临着没有数字签名的问题. Ubuntu支持Secure Boot, 但Manjaro不支持, 因此**需要先disable**安全启动.[^needtodisable]

[^secureboot]

[^secureboot]: <https://zhuanlan.zhihu.com/p/30136593>
[^needtodisable]: <https://forum.manjaro.org/t/is-it-possible-to-enable-secure-boot/16156/2>; <https://forum.manjaro.org/t/do-i-need-to-disable-secure-boot/20254/4>

## 双系统下的休眠问题

首先, 如果两个OS共享同一个硬盘分区, 不能在一个OS休眠后启动另一个OS, 这会破坏数据一致性, 导致数据丢失.尤其是两个OS共享EFI分区, 可能让EFI partition损坏. 而windows的快速启动功能会让"Windows的关闭计算机"实际为休眠.

因此, 首先要关闭Windows的快速启动功能, 其次要保证每次切换系统时, **不要休眠**. 最好就把休眠功能关掉.
[^hibernation]: <https://wiki.archlinux.org/index.php/Dual_boot_with_Windows#Fast_Startup_and_hibernation>

## 开始安装
[^install]: <https://forum.manjaro.org/t/root-tip-dual-boot-manjaro-and-windows/1164>
### 烧录镜像进USB
在Windows使用rufus烧录, 当问你"ISO模式还是dd模式"时, 一定要选择dd模式.[^iso_dd]

[^iso_dd]: <https://wiki.manjaro.org/index.php/Burn_an_ISO_File#Writing_to_a_USB_Stick_in_Windows>

### Disable Intel RST
安装Manjaro前需要关闭Intel Optane Memory 和 Rapid Storage Technology.

若启用RST, 硬盘处于RAID模式, 则安装程序无法识别硬盘.[^rst] 在BIOS里面也要把SATA模式由RAID改为AHCI, **这一般会导致Windows启动不了**, 因此要先去查一下如何修改SATA模式且不需要重装Windows的方法. 我进BIOS看过, SATA已经是AHCI模式, 我就没有了解了.

[^rst]: <https://forum.manjaro.org/t/install-manjaro-with-sata-mode-raid/5525>; <https://forum.manjaro.org/t/ssd-not-detected-when-booting-in-live-environment/16679>
### Driver选择
先进入Live Environment时会要求选择语言, 键盘和Driver. Driver的选择方法如下:

> Free :
drivers are open-source, like Manjaro itself, written and updated by a large community. For AMD graphics cards and > hardware with Intel-based integrated graphics, this is the best choice.
> 
> Non-Free :
drivers are closed-source, written and updated only by the hardware manufacturers. This is generally the best choice for newer Nvidia dedicated graphics. For older Nvidia hardware the Free drivers work very well.
>
> If in doubt, choose Free drivers. If you want to play games with an Nvidia graphics card, choose non-Free drivers.

|Free |Non-Free|
|---|---|
|开源驱动|闭源驱动|
|AMD显卡|新Nvidia显卡|
|基于Intel的集显|想用Nvidia显卡打游戏|
|老的Nvidia显卡||
|实在不知道怎么选(条件都不满足)||

### 分区 大小设置
分区可以保护数据文件和系统文件分开, 不至于因为硬盘满了而全部挂掉. 另外, 对于重装系统也很方便.
[^partitioning]
关于各文件目录的含义和作用, 可以查阅[Filesystem Hierarchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)

**/**: 根目录. /usr和/etc一定要和根目录在同一个分区. 其中/usr保存的处共享的,只读的用户数据包含大量的用户应用文件
**/boot**: 用来存放系统内核,ramdisk,boot loader等. 一般而言不需要单独分区, 除非boot loader并不支持在根目录下访问/boot(比如说文件系统不支持). 假如不是吧ESP挂载到/boot, 那就分配200MiB足以. 我这里选择不单独分区.
**/home**: 保存的是个人数据, 个人的配置文件等.
**swap**: 一般用于当内存不够时, 用来和硬盘交换的; 或者需要休眠时, 存放在硬盘上. 关于swap的大小, 一般推荐RAM size为2GB-8GB的swap设置为同样的大小. 对于需要休眠的情况, Ubuntu推荐要RAM size + sqrt(RAM size), RedHat推荐要2X[^redhat_swap], 而Arch官网说即便swap size小于RAM也是可以成功休眠的[^arch_hibernation]. 众说纷纭, 我选择= RAM size, 根据Arch Linux的推荐.

[^swap]: <https://opensource.com/article/19/2/swap-space-poll>
[^redhat_swap]: <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/sect-disk-partitioning-setup-x86#sect-recommended-partitioning-scheme-x86>
[^arch_hibernation]: <https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate>
[^arch_swap]: <https://wiki.archlinux.org/index.php/Partitioning#Swap>
[^partitioning]: <https://wiki.archlinux.org/index.php/Partitioning>

具体操作为:  
1. 找到原来的ESP, 点击保留, 挂载到/boot/efi标记为boot和esp.
2. 选择空闲区, 点击创建, 文件系统为linuxswap, 大小为8GB, 标记为swap.
3. 选择空闲区,点击创建, 文件系统为ext4, 大小为64G, 挂载到根目录, 标记为root.
4. 剩余空间挂载到/home, 没有标记.

### update GRUB

有时候GRUB2不会检测Windows, 此时需要手动执行:
```shell
sudo update-grub
```

## 善后
[^leftbehind]

[^leftbehind]: <https://www.cnblogs.com/Jaywhen-xiang/p/11561661.html>; <https://www.luogu.com.cn/blog/finder-iot/windows-10-manjaro-shuang-ying-pan-shuang-ji-tong-an-zhuang-jiao-chen>
### 更改源
### 时间


## 感想

这次准备花了大概3天, 个人感觉效率很低. 频繁地在各个页面间切换, 却没有耐下性子好好看看里面的内容, 导致遗漏的东西很多, 要对一个页面反复地阅读.

另外, 我还发现各种各样的贴吧,论坛,博客的内容可信度参差不齐.我不愿意听信一家之言, 所以在对比和验证中花了很多时间. 但这个花费是可以避免的, 我应该优先找官方教程(User guide), 里面的内容直接与我要做的事情相关, 集中而且权威; 其次再找wiki, 里面权威, 但是无关的东西很多; 再次才找各种Forum, 这可能是针对某个非常具体的问题, 而上面两个地方都没有提及才去. 

另外, 我还意识到时效性的问题. 以前我从来不看帖子,博客什么时候写的, 但是我渐渐地发现, 有些信息是会过时的. 因此在查询之前, 我应该先想好我要的信息最远可以多久以前写的, 在浏览帖子的时候先找时间.