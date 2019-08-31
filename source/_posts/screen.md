---
title: GNU Screen 多会话管理工具
date: 2017-11-01 06:12:10
category: 工具
tags: Linux
---
**GNU Screen** 是一款由 GNU 计划开发的用于命令行终端切换的自由软件。用户可以通过该软件同时连接多个本地或远程的命令行会话，并在其间自由切换。

## 功能

* 会话恢复
* 多窗口
* 会话共享

## SIGHUP 信号

为什么关掉窗口/断开连接会使得正在运行的程序死掉？

在 Linux/Unix 中，有这样几个概念：

* 进程组（process group）：一个或多个进程的集合，每一个进程组有唯一一个进程组 ID，即进程组长进程的 ID。
* 会话期（session）：一个或多个进程组的集合，有唯一一个会话期首进程（session leader）。会话期 ID 为首进程的 ID。
* 会话期可以有一个单独的控制终端（controlling terminal）。与控制终端连接的会话期首进程叫做控制进程（controlling process）。当前与终端交互的进程称为前台进程组。其余进程组称为后台进程组。

根据 POSIX.1 定义：

* 挂断信号（SIGHUP）默认的动作是终止程序。
* 当终端接口检测到网络连接断开，将挂断信号发送给控制进程（会话期首进程）。
* 如果会话期首进程终止，则该信号发送到该会话期前台进程组。
* 一个进程退出导致一个孤儿进程组中产生时，如果任意一个孤儿进程组进程处于 STOP 状态，发送 SIGHUP 和 SIGCONT 信号到该进程组中所有进程。

因此当网络断开或终端窗口关闭后，控制进程收到 SIGHUP 信号退出，会导致该会话期内其他进程退出。

## 开始使用 Screen

### 创建一个新的窗口

```
$ screen
或
$ screen vim test.c
```

在一个已有 screen 会话中创建新的窗口并且换到该窗口，键入 C-a c，即 Ctrl 键 + a 键，之后再按下 c 键。

### 会话恢复

```
$ screen vim /tmp/abc
```

键入 C-a d 暂时中断会话。

找到要恢复的会话：

```
$ screen -ls
There are screens on:
	12036.ttys000.XIAOs-MacBook-Pro	(Detached)
1 Sockets in /var/folders/ww/1wrvb8qj1bqg7181thcc9y980000gn/T/.screen.
```

重新连接会话：

```
$ screen -r 12036
```

## 其它

默认情况下，screen 接收以 C-a 开始的命令。这种命令形式在 screen 中叫做键绑定（key binding），C-a 叫做命令字符（command character）。

可以通过 C-a ? 来查看所有的键绑定，常用的键绑定有：

```
C-a ?		显示所有键绑定信息
C-a w		显示所有窗口列表
C-a C-a		切换到之前显示的窗口
C-a c		创建一个新的运行 shell 的窗口并切换到该窗口
C-a n		切换到下一个窗口
C-a p		切换到前一个窗口(与 C-a n 相对)
C-a 0..9	切换到窗口 0..9
C-a a		发送 C-a 到当前窗口
C-a d		暂时断开 screen 会话
C-a k		杀掉当前窗口
C-a [		进入拷贝/回滚模式
```

如果一个会话死掉了，screen -ls 会显示该会话为 dead 状态，可以使用 screen -wipe 清除该会话。