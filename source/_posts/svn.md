---
title: 简明 SVN 教程
date: 2018-09-25 18:33:58
categories: 技术
tags: SVN
---
最近新入职的公司团队，有用到 SVN 进行版本控制，所以对 SVN 进行了详细的学习与了解。

## 关于版本控制

版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。

## 本地版本控制系统

许多人习惯用复制整个项目目录的方式来保存不同的版本，或许还会改名加上备份时间以示区别。 这么做唯一的好处就是简单，但是特别容易犯错。 有时候会混淆所在的工作目录，一不小心会写错文件或者覆盖意想外的文件。

为了解决这个问题，人们很久以前就开发了许多种本地版本控制系统，大多都是采用某种简单的数据库来记录文件的历次更新差异。

![](https://git-scm.com/book/en/v2/images/local.png)

## 集中化的版本控制系统

接下来人们又遇到一个问题，如何让在不同系统上的开发者协同工作？ 于是，集中化的版本控制系统（Centralized Version Control Systems，简称 CVCS）应运而生。 这类系统，诸如 CVS、Subversion 以及 Perforce 等，都有一个单一的集中管理的服务器，保存所有文件的修订版本，而协同工作的人们都通过客户端连到这台服务器，取出最新的文件或者提交更新。 多年以来，这已成为版本控制系统的标准做法。

![](https://git-scm.com/book/en/v2/images/centralized.png)

这种做法带来了许多好处，特别是相较于老式的本地 VCS 来说。 现在，每个人都可以在一定程度上看到项目中的其他人正在做些什么。 而管理员也可以轻松掌控每个开发者的权限，并且管理一个 CVCS 要远比在各个客户端上维护本地数据库来得轻松容易。

事分两面，有好有坏。 这么做最显而易见的缺点是中央服务器的单点故障。 如果宕机一小时，那么在这一小时内，谁都无法提交更新，也就无法协同工作。 如果中心数据库所在的磁盘发生损坏，又没有做恰当备份，毫无疑问你将丢失所有数据——包括项目的整个变更历史，只剩下人们在各自机器上保留的单独快照。 本地版本控制系统也存在类似问题，只要整个项目的历史记录被保存在单一位置，就有丢失所有历史更新记录的风险。

## 分布式版本控制系统

基于上面的问题，出现了分布式版本控制系统（Distributed Version Control System，简称 DVCS）。 在这类系统中，像 Git、Mercurial、Bazaar 以及 Darcs 等，客户端并不只提取最新版本的文件快照，而是把代码仓库完整地镜像下来。 这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。 因为每一次的克隆操作，实际上都是一次对代码仓库的完整备份。

![](https://git-scm.com/book/en/v2/images/distributed.png)

更进一步，许多这类系统都可以指定和若干不同的远端代码仓库进行交互。籍此，你就可以在同一个项目中，分别和不同工作小组的人相互协作。 你可以根据需要设定不同的协作流程，比如层次模型式的工作流，而这在以前的集中式系统中是无法实现的。

## 针对现状优化

基于简单、使用场景有限、良好的权限管理或者其它原因采用了 SVN 进行版本控制，那么我们就需要想办法弥补它的不足，针对单点故障的问题，就要对版本库进行定时全量和增量的备份，以确保源码的安全。

## 关于 SVN

Subversion(SVN) 是一个开源的集中化的版本控制系統，会记住每一次文件的变动，这样你可以将文件恢复到旧的版本或者浏览文件的历史变动。

## SVN 的一些概念

* repository（源代码库）：源代码统一存放的地方
* checkout（提取）：当你手上没有源代码的时候，你需要从 repository checkout 一份
* commit（提交）：当你已经修改了代码，你就需要 commit 到 repository
* update（更新）：当你已经 checkout 了一份源代码，update 一下你就可以和 repository 上的源代码同步，你手上的代码就会有最新的变更

## SVN 生命周期

* 创建版本库：create 操作创建一个新的版本库，版本库用于存放文件，包括了每次修改的历史。
* 检出：checkout 操作从版本库创建一个工作副本，作为开发者私人的工作空间，可以进行内容的修改，然后提交到版本库中。
* 更新：update 操作更新版本库，将工作副本与版本库进行同步。因为版本库是整个团队共用的，当其他人提交了改动，你的工作副本就会过期。
* 执行变更：检出之后，可以进行添加、编辑、删除、重命名、移动文件/目录等变更操作。当最终执行了 commit 操作后，就对版本库进行了相应变更。
* 复查变化：当你对工作副本进行了一些修改后，你的工作副本就会比版本库新，在 commit 操作之前使用 status/diff 操作复查下你的修改是一个好的习惯。
* 修复错误：如果你对工作副本做了许多修改，当时不想要这些修改了，revert 操作可以重置工作副本的修改，恢复到原始状态。
* 解决冲突：合并的时候可能发生冲突，使用 merge 操作进行合并。因为 SVN 合并是以行为单位的，只要不是修改的同一行，SVN 都会自动合并，如果是同一行，SVN 会提示冲突，需要手动进行确认修改，合并代码。其中 resolve 操作可以帮助找出冲突。
* 提交更改：将文件/目录添加到待变更列表，使用 commit 操作将更改从工作副本更新到版本库，提交是添加注释说明，是个好的习惯。

## SVN 的一些优点

* 原子提交，一次提交不管是单个还是多个文件，都是作为一个整体提交的。在这当中发生的意外例如传输中断，不会引起数据库的不完整和数据损坏。
* 目录与文件都能进行版本控制。
* 重命名、复制、删除文件等动作都保存在版本历史记录当中。

## SVN 安装

### Windows

正常的 Windows 软件安装方法，下载地址：[Subversion](http://subversion.apache.org/packages.html#windows)。

### CentOS

大多数 GNU/Linux 发行版系统自带了Subversion ，所以它很有可能已经安装在你的系统上了。可以使用下面命令检查是否安装了。

```
$ svn --version
```

如果没有安装可以使用命令安装

```
$ sudo yum install subversion
```

### Ubuntu

安装命令

```
$ sudo apt install subversion
```

## SVN 服务器配置

新建版本库目录

```
$ mkdir /opt/svn
```

创建版本库

```
$ svnadmin create /opt/svn/zinaer
```

在目录 `/opt/svn/zinaer/conf` 中有 `svnserve.conf`、`passwd`、`authz` 文件配置相关用户和权限。

服务配置文件 `svnserve.conf`

```
[general]
anon-access = none
auth-access = write
password-db = passwd
authz-db = authz
realm = zinaer
```

说明：

* anon-access：控制非鉴权用户访问版本库的权限。取值：write、read 和 none。缺省值：read。
* auth-access：控制鉴权用户访问版本库的权限。取值：write、read 和 none。缺省值：write。
* authz-db：指定权限配置文件名。用于实现以路径为基础的访问控制。如果不是绝对路径，就是相对 conf 目录的相对路径。缺省值：authz。
* realm：指定版本库的认证域，即在登录时提示的认证域名称。如果两个版本库的认证域相同，建议使用相同的用户名口令数据文件。缺省值：一个 UUID（全局唯一标识）。

用户名口令文件 `passwd`

由 `svnserve.conf` 的配置项 `password-db` 指定。格式：`<用户名> = <口令>`

```
[users]
admin = admin
zinaer = 123456
```

权限配置文件 `authz`

由 `svnserve.conf` 的配置项 `authz-db` 指定。格式：`<用户组> = <用户列表>`、`[<版本库名>:<路径>]`

```
[groups]
g_admin = admin,zinaer

[zinaer:/]
@g_admin = rw
* = r
```

说明：`@` 表示一个组名，而不是用户名，`*` 表示其余所有人。

启动服务

```
$ svnserve -d -r 目录 --listen-port 端口号
```

`-r` 指定版本库根目录 `--listen-port` 指定 SVN 监听端口，默认监听 3690

`-r` 配置决定了不同的访问方式。

方式一：`-r` 直接指定到版本库（单库模式）

```
$ svn serve -d -r /opt/svn/zinaer
```

authz 配置文件：

```
[groups]
admin = admin,zinaer
dev = zinaer001
[/]
@admin = rw
zinaer = r
```

客户端使用 URL：svn://192.168.10.10/ 即可访问 zinaer 版本库。

方法二：指定到版本库的上级目录（多库模式）

```
$ svn serve -d -r /opt/svn
```

authz 配置文件：

```
[groups]
admin = admin,zinaer
dev = zinaer001
[zinaer:/]
@admin = rw
zinaer001 = r
[zinaer001:/]
@admin = rw
zinaer001 = r
```

客户端使用 URL：svn://192.168.10.10/zinaer 即可访问 zinaer 版本库。

## SVN 检出操作

我们已经创建了版本库 zinaer，并且启动了服务，URL 为：svn://192.168.10.10/zinaer 用户 zinaer 由读写权限。

```
$ svn checkout svn://192.168.10.10/zinaer --username zinaer --password 123456
```

查看版本库详细信息

```
$ svn info
```

## SVN 解决冲突

假设账号 admin 和 zinaer 都创建了版本库副本，在版本号是 1 的时候，都更新了 hello.txt 文件，admin 在修改完后提交到了服务器，这时候文件版本变成了 2。同时 zinaer 在版本 1 的时候也进行了修改，这时候提交到服务器由于不是在版本 2 上进行的修改，导致提交失败。

现在我们来处理冲突，使用命令查看更改：

```
$ svn diff
Index: hello.txt
===================================================================
--- hello.txt   (revision 3)
+++ hello.txt   (working copy)
@@ -1 +1 @@
-hello world https://skm.zinaer.com
+hello world https://skm.zinaer.com 123
```

如果我们直接提交会提示什么

```
$ svn commit -m 'change first'
Sending        hello.txt
Transmitting file data .done
Committing transaction...
svn: E160028: Commit failed (details follow):
svn: E160028: File '/hello.txt' is out of date
```

可以看到，不能成功提交。为了避免代码被相互覆盖，SVN 不允许我们这样操作，需要先 update

```
$ svn update
Updating '.':
C    hello.txt
Updated to revision 4.
Summary of conflicts:
  Text conflicts: 1
```

此时，和版本库已经同步了，可以进行 commit 了。

## SVN 提交操作

我们在版本库中添加一个 README.md 文件

```
$ cat README.md
# 简明 SVN 教程
```

查看状态

```
$ svn status
?       README.md
```

`?` 代表 `README.md` 还没有加入版本控制中。

将文件加入版本控制

```
$ svn add README.md
A         README.md
```

`A` 代表加入了版本控制，现在需要将其更新到版本库中

```
$ svn commit -m 'add README.md'
Adding         README.md
Transmitting file data .done
Committing transaction...
Committed revision 6.
```

`-m` 选项用于添加注释信息。

## SVN 版本回退

如果我们想放弃对文件的修改，可以使用 `revert` 命令。

我们对 README.md 文件进行修改并查看状态

```
$ svn status
M       README.md
```

现在我们进行撤销操作，更改到未修改时的状态

```
$ svn revert README.md
Reverted 'README.md'
```

`revert` 不仅仅可以使单个文件恢复原状，而且可以对整个目录恢复。使用命令 `-R`

```
$ svn revert -R zinaer
```

如果想恢复一个已经提交的版本，相当于我们需要撤销旧版本所有的更改然后提交一个新版本。比如：从版本 8 恢复到版本 7

```
$ svn merge -r 8:7 README.md
```

## SVN 查看历史消息

* svn log：用来展示 svn 的版本作者、日期、路径等。
* svn diff：用来显示特定修改的行级详细信息。
* svn cat：取得在特定版本的某文件显示在当前屏幕。
* svn list：显示一个目录或某一个版本存在的文件。

## SVN 分支

如果希望开发进程分开成两条不同的线路时，创建不同的分支进行开发是个很好的方式。

分支其实就是主线的一个 copy 版，不过分支同样具有版本控制功能，并且和主分支相互独立，最后可以将分支合并到主分支，从而成为一个项目。

我们在本地创建一个 `zhanbai` 分支

```
$ ls
branches/  tags/  trunk/

$ svn copy trunk/ branches/zhanbai
A         branches\zhanbai
```

查看状态

```
$ svn status
A  +    branches\zhanbai
```

提交新的分支到版本库

```
$ svn commit -m 'add zhanbai'
Adding         branches\zhanbai
Committing transaction...
Committed revision 9.
```

现在我们开始在 `zhanbai` 分支进行开发，创建 `index.html` 文件

```
$ cd branches/zhanbai/

$ ls
hello.txt  index.html  README.md
```

将 `index.html` 加入版本控制，并提交到版本库中

```
$ svn status
?       index.html

$ svn add index.html
A         index.html

$ svn commit -m 'add index.html'
Adding         index.html
Transmitting file data .done
Committing transaction...
Committed revision 10.
```

切换到 `trunk` 分支，并更新，然后将 `zhanbai` 分支合并到 `trunk` 中

```
$ svn merge ../branches/zhanbai/
--- Merging r10 into '.':
A    index.html
--- Recording mergeinfo for merge of r10 into '.':
 G   .
```

此时，`trunk` 中就多了 `index.html` 文件。然后，将合并号的 `trunk` 提交到版本库中

```
$ svn commit -m 'add index.html'
Adding         index.html
Committing transaction...
Committed revision 12.
```

## SVN 标签

SVN 支持打标签，也就是在项目开发中，开发到一定阶段可以单独一个版本作为发布时，往往可以打包一个固定的版本。

```
$ svn copy trunk/ tags/v1.0
A         tags\v1.0
```

将在 `tags` 目录下创建 `v1.0` 目录。

然后查看状态，提交 `tag` 到版本库中。

```
$ svn status
A  +    tags\v1.0

$ svn commit -m 'add v1.0'
Adding         tags\v1.0
Committing transaction...
Committed revision 13.
```