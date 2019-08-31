---
title: 《简明 PHP 教程》02 安装
date: 2018-08-27 10:51:58
category: 《简明 PHP 教程》
tags: 《简明 PHP 教程》
---
我们在本书中提及“PHP”，“PHP 7”时，我们指的是任何大于等于 PHP 7.2 的 PHP 发行版。

## 在 GNU/Linux 下安装

对于 GNU/Linux 用户，你可以使用发行版的包管理器来安装 PHP 7，例如在 Debian 与 Ubuntu 平台下，你可以输入命令：`sudo apt update && sudo apt install php7.2`。

要想验证安装是否成功，你可以通过打开 `Terminal` 应用或通过按下 `Alt + F2` 组合键并输入 `gnome-terminal` 来启动终端程序。如果这不起作用，请查阅你所使用的的 GNU/Linux 发行版的文档。现在，运行 `php` 命令来确保其没有任何错误。

你会看到在运行命令后 PHP 的版本信息显示在屏幕上：

```
$ php -v
PHP 7.2.9-1+ubuntu16.04.1+deb.sury.org+1 (cli) (built: Aug 19 2018 07:16:12) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.2.9-1+ubuntu16.04.1+deb.sury.org+1, Copyright (c) 1999-2018, by Zend Technologies
    with Xdebug v2.6.0, Copyright (c) 2002-2018, by Derick Rethans
```

附注：`$` 是 Shell 的提示符。根据你电脑所运行的操作系统的设置的不同，它也会有所不同，在之后的内容中我会使用 $ 符号来代表提示符。

注意：输出的内容会因你的电脑而有所不同，其取决于你在你的电脑上安装的 PHP 版本。

## 在 Mac OS 下安装

对于 Mac OS X 用户，你可以使用 `Homebrew` 并通过命令 `brew install php@7.2` 进行安装。

要想验证安装是否成功，你可以通过按键 `[Command + Space]` （以启动 Spotlight 搜索），输入 `Terminal` 并按下 `[enter]` 键来启动终端程序。现在，试着运行 `php` 命令来确保其没有任何错误。

## 在 Windows 下安装

访问 http://php.net/downloads.php 并下载最新版本的 PHP。在本书撰写的时点，最新版本为 PHP 7.2。其安装过程与其它 Windows 平台的软件的安装过程无异。

然后，为你的 PHP 所在的根目录（php.exe 所在的文件夹）设置 PATH，这样就可以从命令行中直接执行 PHP。

在 Vista，Windows 7 和 Windows 8 中，我们可以使用 `setx` 命令从命令行设置路径。

```
setx path "%path%;c:\directoryPath"
```

## 总结

从现在起，我们将假定你已经在你的系统中安装了 PHP。

接下来，我们将要撰写我们的第一个 PHP 程序。