---
title: apt 和 apt-get 的区别
date: 2016-06-11 14:59:35
category: 技术
tags: Linux
---

从 Ubuntu 16.04 开始，一个值得注意的新功能是 apt 命令的引入。事实上，apt 的第一个稳定版本是 2014 年发布的，但是随着 Ubuntu 16.04 的发布，人们才开始注意到它。

越来越多的人使用 `apt install package` 代替 `apt-get install package`，慢慢的，许多其它发行版本也开始遵循 Ubuntu 的脚步，鼓励用户使用 apt 而不是 apt-get。

你可能知道 apt 和 apt-get 的区别，但是如果有类似的命令，那么新命令 apt 对应使用哪一个？你可能还在思考 apt 是否比 apt-get 好？你应该使用新命令 apt 还是坚持使用 apt-get？

我将在本文解释这些问题，希望通过阅读这篇文章，你能有个清晰的认知。

## 为什么要引入 apt ？

基于 Debian 的 Linux 发行版系统，如：Ubuntu、Linux Mint 和 elementary OS，都内置了包管理工具。Debian 使用了一组叫 Advanced Packaging Tool（APT）的包管理工具。注意，这里不要与 apt 命令混淆。

有各种可以与 APT 交互的工具来实现基于 Debian 的 Linux 发行版安装包的安装，删除和管理。apt-get 是一个广泛使用的命令行工具，另一个是同时具有 GUI 和命令行的 Aptitude。

与 apt-get 类似的命令有很多，比如 apt-cache。这就是问题的所在，这些命令太分散了，对于没有使用过 Linux 的普通用户，很难理解与使用。apt 命令的引入就是为了解决这个问题，apt 包含 apt-get 和 apt-cache 中最广泛使用的功能，而且可以管理 apt.conf 文件。

## apt 与 apt-get 的区别

使用 apt 可以获得几乎所有的功能，它的主要目的就是让用户使用最简单、高效的方式使用包管理工具。

apt 默认启用一些对用户实际使用有益的操作，比如：可以在 apt 安装或删除操作过程中显示进度条。在更新软件包列表的时候还可以提示你可以升级的包的数量。虽然使用 apt-get 也可以实现这些功能，但是 apt 默认开启这些特性。

## apt 与 apt-get 命令的区别

虽然 apt 有些命令和 apt-get 类似，但是 apt 并没有向后兼容 apt-get。这意味着不可能使用 apt 完全替代 apt-get 命令。下面我列出了哪些 apt 命令替换了 apt-get 和 apt-cache 命令。

| apt 命令 | 被取代的命令 | 说明 |
|------|------|------|
| apt install | apt-get install | 安装新包 |
| apt remove | apt-get remove | 卸载已安装的包（保留配置文件） |
| apt purge | apt-get purge | 卸载已安装的包（删除配置文件） |
| apt update | apt-get update | 更新软件包列表 |
| apt upgrade | apt-get upgrade | 更新所有已安装的包 |
| apt autoremove | apt-get autoremove | 卸载已不需要的包依赖 |
| apt full-upgrade | apt-get dist-upgrade | 自动处理依赖包升级 |
| apt search | apt-cache search | 查找软件包 |
| apt show | apt-cache show | 显示指定软件包的详情 |

apt 也有一些自己的命令。

| 新的 apt 命令 | 说明 |
|------|------|
| apt list | 列出包含条件的包（已安装，可升级等） |
| apt edit-sources | 编辑源列表 |

apt 正在不断发展，因此，后续可能看到更多新的命令行。

## apt-get 已被弃用

没有任何信息表明 apt-get 已被弃用，实际上也不应该，因为它还有比 apt 更多的功能。对于一些使用场景，如脚本操作，可能还要用 apt-get 命令。

## 应该使用 apt 还是 apt-get

作为普通的 Linux 用户，优先使用 apt，它是 Linux 发行版推荐的命令。它提供了包管理必要的选项，更重要的是便于记忆。

## 结语

我希望可以讲清楚 apt 和 apt-get 的区别，最后总结下 apt 和 apt-get 的结论：

* apt 是 apt-get 和 apt-cache 的子集，为包管理提供必要的命令。
* 虽然 apt-get 没有被弃用，但是作为普通 Linux 用户，推荐开始频繁的使用 apt。
