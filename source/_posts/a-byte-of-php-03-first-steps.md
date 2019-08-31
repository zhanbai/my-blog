---
title: 《简明 PHP 教程》03 第一步
date: 2018-08-29 10:07:02
category: 《简明 PHP 教程》
tags: 《简明 PHP 教程》
---
接下来我们将看见如何在 PHP 中运行一个传统的“Hello World”程序。本章将会教你如何编写、保存与运行 PHP 程序。

通过 PHP 来运行的你的程序有两种方法——使用交互式解释器提示符或直接运行一个源代码文件。我们将了解如何使用他们二者的功能。

## 使用解释器提示符

在你的操作系统中打开终端（Terminal）程序（正如我们先前在 [安装](./04.installation.md) 章节所讨论过的那样）然后通过输入 `php -a` 并按下 `[enter]` 键来打开 PHP 交互式运行模式。

当你启动 PHP 后，你会看见在你能开始输入内容的地方出现了 `php >` 。这个被称作 PHP 解释器提示符。

在 PHP 解释器提示符，输入：

```
echo "Hello World";
```

在输入完成后按下 `[enter]` 键。你将会看到屏幕上打印出 `Hello World` 字样。

下面是一个在 Mac OS X 电脑上你能够看见的结果的示例。有关 PHP 软件的细节将会因为你使用的电脑而有所不同，但是从提示符（如 `php >` ）开始部分应该是相同的，而不会受到操作系统的影响。

```
~ php -a
Interactive shell

php > echo 'Hello World';
Hello World
```

你自然会注意到，PHP 会立即给你输出了一行结果！你刚才所输入的便是一句独立的 PHP 语句 。我们使用 `echo` （不必太过惊讶）命令来打印你所提供的信息。在这里，我们提供了文本 `Hello World` ，然后它便被迅速地打印到了屏幕上。

### 如何退出解释器提示符

如果你正在使用一款 GNU/Linux 或 OS X 上的 Shell 程序，你可以通过按下 `[ctrl + d]` 组合键或是输入 `exit` 并敲下 `[enter]` 来退出解释器提示符。

## 选择一款编辑器

当我们希望运行某些程序时，总不能每次都在解释器提示符中输入我们的程序。因此我们需要将它们保存为文件，从而我们便可以多次地运行这些程序。

要想创建我们的 PHP 源代码文件，我们需要一款能够让你输入并保存代码的编辑器软件。一款优秀的面向程序员的编辑器能够帮助你的编写源代码文件工作变得轻松得多。故而选择一款编辑器确实至关重要。你要像挑选你想要购买的汽车一样挑选你的编辑器。一款优秀的编辑器能够帮助你更轻松地编写 PHP 程序，使你的编程之旅更加舒适，并助你找到一条更加安全且快速的道路到达你的目的地（实现你的目标）。

对编辑器的一项最基本要求为语法高亮 ，这一功能能够通过标以不同颜色来帮助你区分 PHP 程序中的不同部分，从而能够让你更好看清你的程序，并使它的运行模式更加形象化。

如果你对应从哪开始还没有概念，我推荐你使用 [PhpStorm](https://www.jetbrains.com/phpstorm/) 软件，它在 Windows、Mac OS X、GNU/Linux 上都可以运行。在下一节你能够了解到更多信息。

如果你正在使用 Windows 系统，不要用记事本——这是一个很糟糕的选择，因为它没有语法加亮功能，同样重要的另一个原因是，它不支持文本缩进功能，这一功能我们之后将会了解它究竟有多重要。而一款好的编辑器能够自动帮你完成这一工作。

如果你已是一名经验丰富的程序员，那你一定在用 [Vim](http://www.vim.org/) 或 [Emacs](http://www.gnu.org/software/emacs/) 了。无需多言，它们都是最强大的编辑器之一，用它们来编写你的 PHP 程序自是受益颇多。

或许你有意去花费时间来学习 Vim 或 Emacs，那么我也建议你学习它们二者中的一款，它们将在长远意义上对你裨益颇深。当然，正如我先前所推荐的，初学者可以以 PhpStorm 开始，从而在此刻专注于学习 PHP 而不是编辑器。

再次重申，请选择一款合适的编辑器——它能够让编写 PHP 程序变得更加有趣且容易。

### PhpStorm

[PhpStorm](https://www.jetbrains.com/phpstorm/) 是一款能够对你编写 PHP 程序的工作有所帮助的编辑器。

当你打开 PhpStorm 时，你会看见如下界面，点击 `Create New Project` ：

![image](http://upload-images.jianshu.io/upload_images/1595196-568f62bfafca178a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择 `PHP Empty Project` ：

![image](http://upload-images.jianshu.io/upload_images/1595196-8b91a6d764873484.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将你的项目路径位置中的 `untitled` 更改为 `helloworld` ，你所看到的界面细节应该类似于下方这番：

![image](http://upload-images.jianshu.io/upload_images/1595196-f7b6c849d25ad2c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击 `Create` 按钮。

对侧边栏中的 `helloworld` 右击选中，并选择 `New` -> `PHP File` ：

![image](http://upload-images.jianshu.io/upload_images/1595196-6670c399d77e6649.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你会被要求输入名字，现在输入 `hello` ：

![image](http://upload-images.jianshu.io/upload_images/1595196-a03153b6a646c923.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在你便可以看见一个新的文件已为你开启：

![image](http://upload-images.jianshu.io/upload_images/1595196-d019f89128188840.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

删除那些已存在的内容，现在由你自己输入以下代码：

```
<?php
echo "hello world";
```

现在右击你所输入的内容（无需选中文本），然后点击 `Run 'hello'` 。

![image](http://upload-images.jianshu.io/upload_images/1595196-e8e449181410f877.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此刻你将会看到你的程序所输出的内容（它所打印出来的内容）：

![image](http://upload-images.jianshu.io/upload_images/1595196-72cf6f9f852bd5f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

虽然只是刚开始的几个步骤，但从今以后，每当我们要求你创建一个新的文件时，记住只需在 `helloworld` 上右击并选择 -> `New` -> `PHP File` 并继续如上所述步骤一般输入内容并运行即可。

你可以在 [PhpStorm Quickstart](https://www.jetbrains.com/help/phpstorm/quick-start-guide-phpstorm.html) 页面找到有关 PhpStorm 的更多信息。

### Vim

安装 [Vim](http://www.vim.org/)。

* Mac OS X 应该通过 [HomeBrew](http://brew.sh/) 来安装 `macvim` 包。
* Windows 用户应该通过 [Vim 官方网站](http://www.vim.org/download.php) 下载“自安装可执行文件”。
* GNU/Linux 用户应该通过他们使用的发行版的软件仓库获取 Vim。例如 Debian 与 Ubuntu 用户可以安装 `vim` 包。

安装 [YouCompleteMe](https://github.com/Valloric/YouCompleteMe) 插件为 Vim 增添自动补全功能。

### Emacs

安装 [Emacs](https://www.gnu.org/software/emacs/)。

* Mac OS X 用户应该从 http://emacsformacosx.com 获取 Emacs。
* Windows 用户应该从 http://ftp.gnu.org/gnu/emacs/windows/ 获取 Emacs。
* GNU/Linux 用户应该从他们使用的发行版的软件仓库获取 Emacs。如 Debian 和 Ubuntu 用户可以安装 `emacs26` 包。

## 使用一份源代码文件

现在让我们回到编程中来。在你学习一门新的编程语言时有一项传统，你所编写并运行的第一个程序应该是 “Hello World” 程序——它所做的全部工作便是宣言你所运行的“Hello World”这句话。正如西蒙·科泽斯所说，这是“向编程之神所称颂的传统咒语，愿他帮助并保佑你更好的学习这门语言”。

启动你所选择的编辑器，输入如下程序并将它保存为 `hello.php` 。

如果你正在使用 PhpStorm，我们已经讨论过[如何从源文件中运行](#phpstorm)它了。

对于其它编辑器，打开一个新文件名将其命名为 `hello.php` ，然后输入如下内容：

```
<?php
echo "hello world";
```

你应当将文件保存到哪里？保存到任何你知道其位置与路径的文件夹。如果你不了解这句话是什么意思，那就创建一个新文件夹并用这一路径来保存并运行你所有的 PHP 程序：

* Mac OS X 上的 `/Code/py` 。
* GNU/Linux 上的 `/Code/py` 。
* Windows 上的 `C:\\py` 。

要想创建上述文件夹（在你正在使用的操作系统上），你可以在终端上使用 `mkdir` 命令，如 `mkdir /Code/py` 。

重要提示：你需要经常确认并确保你为文件赋予了 `.php` 扩展名，例如 `foo.php` 。

要想运行你的 PHP 程序：

1. 打开终端窗口（你可查阅先前的[安装](./04.installation.md)章节来了解应该怎么做）。
2. 使用 `cd` 命令来改变目录到你保存文件的地方，例如 `cd /Code/py` 。
3. 通过输入命令 `php hello.php` 来运行程序。程序的输出结果应如下方所示：

```
$ php hello.php
hello world
```

如果你得到了与上图类似的输出结果，那么恭喜你！——你已经成功运行了你的第一个 PHP 程序。你也已经成功突破了学习编程最困难的部分，也就是，开始编写你的第一个程序！

如果你遭遇了什么错误，请确认是否已经正确地输入了上面所列出的内容，并尝试重新运行程序。

### 它是如何工作的

一款 PHP 程序是由**语句**所构成的。在我们的第一个程序中，我们只有一条语句。在这条语句中，我们调用 `echo` 语句来输出我们提供的文本“hello world”。

## 总结

现在，你应该可以轻松地编写、保存并运行 PHP 程序了。

从此你便成为一名 PHP 用户了，现在让我们来学习更多有关 PHP 的概念。
