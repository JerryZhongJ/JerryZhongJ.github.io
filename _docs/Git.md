---
title: Git

---

## 基本操作

`git init`: 新建仓库

`git add`: 添加到暂存区

`git commit -m`: 提交到仓库; 
- `-a`: 跳过git add, 自动提交**已经跟踪**的文件. [^basic]

`git rm <file>`: 从版本库, 工作目录中删除文件; 
- `-cached`: 保留workspace的文件.
- 关于文件的删除有一点很狗. 当删除了文件后, 使用*git add*是不能把这个改动添加进去的, 因为此时文件夹中不包含已删除的文件, 即*不包括已删除的文件. 因此, 需要显示的写出已删除的文件名, 并把它add进去. 否则, 删除文件始终是not staged的状态.  
或者, 简单点, 用*commit -am*可以自动提交已跟踪的文件, 也可以成功删除.  
要不然, 一开始就是用*git rm*来删除.


`git status`: 查看状态;  
- `-s --short`: 简略信息

`git log`: 查看提交日志 

## gitignore

用于指导跟踪什么文件, 对于已跟踪的文件无效.

`\#xxx`: 添加注释

`!xxxx`: 重新包含匹配的文件, 但若是该文件的目录被排除了, 那就没法重新包含

`/xx, xx/xx/`: 相对路径, 只在.gitignore所在目录那一层找. (默认是递归查找匹配的)

`xx/`: 限定目录文件, 否则将查找普通文件和目录文件, 递归查找匹配

`\*, ?, []`: 通配符, 但只用于表示文件名, 即不能替代'/'符号, 以表示文件路径.

`\*\*`: 通配符, 表示文件路径





## 基本概念 [^start]

### 直接记录快照，而非差异比较

>其它大部分系统以文件变更列表的方式存储信息，这类系统（CVS、Subversion、Perforce、Bazaar 等等） 将它们存储的信息看作是一组基本文件和每个文件随时间逐步累积的差异 （它们通常称作 **基于差异（delta-based）** 的版本控制）
>
> 
>
>Git 更像是把数据看作是对小型文件系统的一系列快照。 在 Git 中，每当你提交更新或保存项目状态时，它基本上就会对当时的全部文件创建一个快照并保存这个快照的索引。 为了效率，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git 对待数据更像是一个 **快照流**

### Git 一般只添加数据

>  你执行的 Git 操作，几乎只往 Git 数据库中 **添加** 数据。 你很难让 Git 执行任何不可逆操作，或者让它以任何方式清除数据。

### Git 保证完整性

> Git 中所有的数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情时更改任何文件内容或目录内容。 这个功能建构在 Git 底层，是构成 Git 哲学不可或缺的部分。 若你在传送过程中丢失信息或损坏文件，Git 就能发现。
>
> Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）

### 关于 workspace， index，和repository

index: 暂存区，但**不是存放临时的修改**的！

需要这么理解，一份文件具有三个镜像，分别在workspace，index，repository。

因此，当你`commit`之后，但没有`add`，那么此时暂存区空空如也？不是！暂存区可以理解为**有一个完整的文件镜像**，且这个镜像和repository中的HEAD一毛一样。因此使用`checkout -- file`之后可以将暂存区的镜像cover workspace中的镜像。

同理，在`commit`之后没有`add`，使用`reset [commit] --mixed`时，暂存区会被`commit` 复原。假如将暂存区理解为记录了临时的修改，那么暂存区理应是空的，就很难理解被commit 覆盖是怎么一回事。[^tree]

## 版本回退

### reset 和 checkout（用于撤销）

`reset [commit] [file]`: `commit`是版本号, 缺省为`HEAD`. `file`是重置某个文件, 缺省代表对重置整个workspace或index. 有三个模式 `--soft`, `--mixed`(缺省), `--hard`，都是将`commit`所指的版本覆盖其他地方，且`HEAD`指针都会移动到`commit`。不同之处在于覆盖谁，`--soft`是只改变HEAD，`--mixed`会覆盖index，`--hard`会覆盖index和workspace。

`reset`可以针对某个file使用，不需要加 `--`.[^reset]

`checkout [commit] -- file`: 与*reset --hard*类似. 但是commit可以省略，此时表示用index 覆盖 workspace（考虑已`commit`但未`add`，然后`reset --mixed`的情况，需要正确理解index的含义: 不是临时存储区!）。若指定commit 则覆盖index 和 workspace[^checkout]'

在回退时, `HEAD`表示当前的仓库版本, `HEAD^`指上一个版本, `HEAD^^`上上个 ..., *HEAD~n*往上n个

最好使用commit id. 

## 分支

主分支master, 和其他分支一样, 并没有优越性.

HEAD只有**一个**, 指向当前分支的最新版本.

### 基本操作 [^branch]

`git branch <branch>`: 分支创建

​	注意: 并**不会切换**到新分支

`git checkout <branch>`: 分支切换

- 注意:  执行前需要保持workspace和index干净, 清空或commit; 否则切换失败. 使用`chekcout -f`强制切换.
- 切换后, workspace会被重置成目标分支的最后提交. 具体是指: 将tracked的文件重置成版本库中的文件. 对于untracked的文件, git不会动.


`git checkout -b <branch>`: 分支创建同时切换

`git log --oneline --decorate --graph --all`: 查看分支

`git branch -d <branch>`: 分支删除

#### 分支合并 [^merge]

`git merge <branch>`: 将branch合并入当前所在的分支, 新产生的快照属于当前分支.

若两个分支只是简单的祖先/子孙关系, 那么只会将其中一个分支指针前移, 此之谓fast-forward.

若两个分支是源于同一个祖先所做的不同修改, 则合并操作会产生一个新的快照, 它有两个父指针, 指向两个分支的快照.

当文件存在冲突, 则合并失败.

> 此时 Git 做了合并，但是没有自动地创建一个新的合并提交。 Git 会暂停下来，等待你去解决合并产生的冲突。 你可以在合并冲突后的任意时刻使用 `git status` 命令来查看那些因包含合并冲突而处于未合并（unmerged）状态的文件

> 你可以运行 `git mergetool`，该命令会为你启动一个合适的可视化合并工具，并带领你一步一步解决这些冲突

> 等你退出合并工具之后，Git 会询问刚才的合并是否成功。 如果你回答是，Git 会**暂存**那些文件以表明冲突已解决： 你可以再次运行 `git status` 来确认所有的合并冲突都已被解决

注意: 这里还没有完成合并. 修改也还未提交.

> 如果你对结果感到满意，并且确定之前有冲突的的文件都已经暂存了，这时你可以输入 `git commit` 来完成合并提交

此次commit将作为合并提交, 即如上所述, 有两个父指针.

## 标签 [^tag]

### 基本操作

`git tag`: 查看;
- `-l | --list <expression>`: 使用正则表达式

`git tag <tag> [commit]`: 轻量标签, commit缺省, 默认HEAD

`git tag -d <tag>`: 删除

### 推送到远程

默认的git push不会将标签推送到远程服务器, 需要显示地推送, 像推送branch一样

`git push <name> <tag>`: 使用 *--tags* 将远程服务器没有的标签全部推送过去



## 远程

一个仓库可以有多个远程仓库, 尤其是和其他人协作时.

而远程仓库的地址会取一个名字, 默认叫origin.

因此不同的名字**不是**指不同的project. 虽然不同远程仓库不尽相同, 但至少是同一个project的. 

可以通过不同的远程地址, 拉取不同人的的提交.

### 基本操作

`git remote [-v]`: 查看远程仓库, -v 显示地址
`git remote show <remote>`: 查看远程仓库情况, 包括`HEAD`所在分支, 跟踪分支, 需要更新的分支.

#### URL

HTTP: http://server.com/usrename/repository.git

HTTPS: https://server.com/usrename/repository.git

SSH: ssh://[user@]server/project.git 或 [user@]server:project.git

注意ssh://协议的两种写法有冒号和反斜杠之分.

#### 创建 本地 -> 远程

若已经有了本地仓库, 想把本地仓库放到服务器.

需要先在服务器上新建一个仓库, 然后在本地关联它.

`git remote add <name> url`

远程地址也有https:// 但每次推送都需要输入口令, 而git://使用ssh

#### 创建 远程 -> 本地

`git clone url` (默认叫origin

#### SSH Key

生成: `ssh-keygen -t rsa -C "email@example.com"` (一路回车

公钥, 私钥存储在: ~/.ssh/

公钥有.pub后缀

### 推送 [^push]

`git push <远程主机名> <本地分支名>:<远程分支名>`

**注意**: 因此本地分支名不一定与远程分支名相同.

`git push origin master`: 如果远程分支被省略，如上则表示将本地分支推送到与之存在追踪关系的远程分支（通常两者同名），如果该远程分支不存在，则会被新建

`git push origin ：master`: 如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支，等同 `git push origin --delete master`

`git push origin`: 如果当前分支与远程分支存在追踪关系，则本地分支和远程分支都可以省略，将当前分支推送到origin主机的对应分支

`git push`: 若当前分支设置了上流分支或者只有一个远程分支, 则省略远程仓库和远程分支.

### 抓取
`git fetch <reomte>`: 从`remote`上抓取所有分支, 并不会自动合并.

### 远程跟踪分支 [^ramotetb]
[^remotetb]: <https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches>

远程引用(reomte references)本地仓库对远程仓库信息的引用, 包括分支和标签等.

分支的远程引用称作远程跟踪分支(remote tracking branch)它代表的是最近与远程仓库通信时, 如`push` 或 `pull` `fetch`后, 远程仓库的分支在本地仓库的位置, 名字为 `<remote>/<branch>`. 

比如说, 我在`pull` 后, 此时远程跟踪分支`origin/master`与本地分支`master`指向同一个地方, 比方说是id叫1234. 经过两次`commit`后, `master`前进了两步, id为2341和3412, 但`origin/master`保持不动, 依旧指向1234. 若远程仓库有人提交了别的修改, 比方说id = 5678, 但远程跟踪分支`origin/master`不变, 因为还没有和远程仓库通信, 还是指向1234. 若本地也做了修改, 那么`fetch`后远程分支`origin/master`将与本地分支岔开.

<img src="/assets/posts_ref/remote-branches-3.png" alt="`git fetch` 更新你的远程仓库引用。" style="zoom:67%;" />

> 要特别注意的一点是当抓取到新的远程跟踪分支时，本地不会自动生成一份可编辑的副本（拷贝）。 换一句话说，这种情况下，不会有一个新的 `serverfix` 分支——只有一个不可以修改的 `origin/serverfix` 指针。

即远程跟踪分支只是一个引用, 并不是真的副本. 要想访问修改, 就将远程跟踪分支与本地分支合并, `git merge <remote>/<branch>`. 也可以一开始用pull拉取, git pull = git fetch + git merge.

若本地没有相应的分支, 则用远程跟踪分支创建一个新的 ,`checkout -b <branch>  <remote>/<branch>`, 或者`checkout <remote>/branch`.

### 跟踪分支 [^remotetb]
跟踪分支是与远程跟踪分支直接相关的本地分支, 而与之相关的那个远程跟踪分支叫做**上流分支(upstream branch)**

*拓展一下, 上流(upstream)和下流(downstream)是指数据的流动. 这并不是绝对的概念, 每个仓库都有它自己理解的upstream和downstream. 假如想要的话, 两个仓库可以互相设对方为upstream. 另外可以这么理解: upstream只有一个, 而且你知道是谁; downstream可以有多个, 而且你并不知道有谁.[^upstream]*

[^upstream]: <https://stackoverflow.com/questions/2739376/definition-of-downstream-and-upstream>

每个分支都可以设置一个上流分支, 通过在`pull`, `push`, `branch`后加`-u` 或 `--set-upstream-to`. 在`clone`和`checkout`后, 都会自动设置上流分支. 设置上流分支后, `push`和`pull`可以省略远程仓库和远程分支.

一个本地仓库可以与多个远程仓库相关

[^basic]:<https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository>
[^tree]:<https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E7%BD%AE%E6%8F%AD%E5%AF%86>
[^reset]:<https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E7%BD%AE%E6%8F%AD%E5%AF%86>
[^checkout]:<https://git-scm.com/docs/git-checkout>
[^tag]:<https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE>
[^start]:<https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-Git-%E6%98%AF%E4%BB%80%E4%B9%88%EF%BC%9F>
[^branch]:<https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%AE%80%E4%BB%8B>
[^push]:<https://www.cnblogs.com/qianqiannian/p/6008140.html;>
[^merge]:<https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6>