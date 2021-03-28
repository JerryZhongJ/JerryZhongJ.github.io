---
title: Linux 基本权限
tags: ["Linux"]
cover: https://cdn.jsdelivr.net/gh/JerryZhongJ/Pictures/20210326221159.JPG
date: 2021-3-18
---
<!--more-->
## 文件权限

三类用户: u(所有者) g(所属组用户) o(其他用户)

每类用户权限: r w x. 其中目录文件的可写表示可以添加删除文件, 可执行表示显示文件的详细信息.

其他权限:

**set user/group id**: 允许可执行文件以所有者以及所属组的身份运行. 显然, 只对可执行文件有效, 且超越了x的含义. 显示文件权限时, set user id在所有者的可执行('x')位置显示's', set group id在所属组的可执行('x')的位置显示's'

**sticky bit**: 只对目录有效, 保护目录下的文件不被所有者以外的人删除. 显示时在其他用户的可执行('x')的位置显示't'



### 数字表示

即为rwx的二进制表示, (按rwx的顺序)

| 数字           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| 第一个可选数字 | **Set-User-ID** (=4), **Set-Group-ID** (=2) 和 **粘滞位** (=1) 之和 |
| 第二个数字     | **用户**的**可读** (=4), **可写** (=2)和 **可执行** (=1) 权限之和 |
| 第三个数字     | **组**权限同上                                               |
| 第四个数字位   | **其他用户**权限同上                                         |

 [^file_permit]



### 相关指令

**显示目录详细信息**: ls -l

**修改所有者**: chown, 需要root权限

**修改所属组**: chgrp, 需要是所有者或root

**修改访问权限**: chmod, 需要是所有者或root

## 进程权限
[^task_permisson1]
有三种id(只说user, group同理): real user id, effective user id, saved set user id.

**real user id**: 进程执行者的user id, 就是当前登录用户的id. 子进程的real user id 继承自父进程的real user id,一般无法改变(???).

在登陆时, login进程验证成功后, 会exec shell进程, 并设置shell进程的real user id 为该用户. 因此此后的由shell fork出来的子进程以及子进程的子进程....的real user id都是此用户.

**effective user id**: 进程进行文件操作时检查的权限.(真正意义上的进程的权限) 一般就是real user id, 但当可执行文件的set user id位被设置, 则effective user id = executable file's owner id. 注意: owner id是文件的所有者id, 不一定是当前登录的用户.

**saved set user id**: 在进程创建时, 父进程的effective user id的一份拷贝. 日后, 通过setuid()可以将effecitve user id设置为real user id, 或者 saved set user id. 即有机会获得执行者的权限, 或者父进程的权限.

[^task_permisson1]:<https://www.cnblogs.com/sparkdev/p/9694103.html>
[^file_permit]:<https://www.debian.org/doc/manuals/debian-reference/ch01.zh-cn.html#_unix_file_basics>

