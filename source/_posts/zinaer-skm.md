---
title: Zinaer SKM - 一款轻量强大的 SSH 密钥管理工具
date: 2017-10-25 06:30:10
category: 工具
tags: 开发
---
今天为大家推荐的一款工具是新鲜出炉的 [Zinaer SKM](https://skm.zinaer.com)，一款轻量，实用，强大的 SSH 密钥管理工具。

## 什么是 SSH

维基百科的定义：

> Secure Shell（缩写为 SSH），由 IETF 的网络工作小組（Network Working Group）所制定；SSH 为一项建立在应用层和传输层基础上的安全协议，为计算机上的 Shell（壳层）提供安全的传输和使用环境。

通俗点讲，就是可以方便你安全的与远程服务进行连接的工具。

## SSH 的基本架构

SSH 协议框架中最主要的部分是三个协议：

* 传输层协议（The Transport Layer Protocol）：传输层协议提供服务器认证，数据机密性，信息完整性等的支持。
* 用户认证协议（The User Authentication Protocol）：用户认证协议为服务器提供客户端的身份鉴别。
* 连接协议（The Connection Protocol）：连接协议将加密的信息隧道复用成若干个逻辑通道，提供给更高层的应用协议使用。

## SSH 的安全验证

在客户端来看，SSH 提供两种级别的安全验证。

* 第一种级别（基于密码的安全验证），知道帐号和密码，就可以登录到远程主机，并且所有传输的数据都会被加密。但是，可能会有别的服务器在冒充真正的服务器，无法避免被「中间人」攻击。
* 第二种级别（基于密钥的安全验证），需要依靠密钥，也就是你必须为自己创建一对密钥，并把公有密钥放在需要访问的服务器上。客户端软件会向服务器发出请求，请求用你的密钥进行安全验证。服务器收到请求之后，先在你在该伺服器的用户根目录下寻找你的公有密钥，然后把它和你发送过来的公有密钥进行比较。如果两个密钥一致，服务器就用公有密钥加密「质询」（challenge）并把它发送给客户端软件。从而避免被「中间人」攻击。

## 为什么会有 Zinaer SKM

阿里云 Code 是个免费，可无限创建私有项目的代码托管平台（基于开源系统 Gitlab），然而有一些限制或者特性：

* 相同 SSH 密钥只允许在一个账号添加（意味着多账号就要多个密钥）
* 不同密钥必须配置 SSH 的 config，而阿里云 Code 对应的 Host 是固定的（意味着向不同账号推送代码，需要修改这个配置）
* 由于笔者公司项目和个人项目都会用到阿里云 Code，也就是说会有不同账号，多个 SSH 密钥，加上还会用到 Github 等代码托管平台，各种服务器管理，大量的密钥，手动的修改，切换配置，确实不方便。索性就开发了这款轻量，实用，强大的 SSH 密钥管理工具。

## Zinaer SKM 的特性

* 创建，查看和删除你的 SSH 密钥
* 通过别名管理你所有的 SSH 密钥
* 选择并设置默认的 SSH 密钥
* 备份和恢复你所有的 SSH 密钥
* 多平台支持（Mac OS X and 各种 Linux 平台）

## 安装

参考官网：https://skm.zinaer.com

## 使用

```
$ skm -h

usage: skm [-h] [-v] [-i [init]] [-c [create]] [-l [ls]] [-u [use]]
           [-d [delete]] [-b [backup]] [-r [restore]]

Zinaer SKM - Manage your multiple SSH keys easily.
--------------------------------------------------
    Author: zinaer.com
    Website: https://skm.zinaer.com
    Version: 1.0

optional arguments:
  -h, --help     show this help message and exit
  -v, --version  print the version
  -i [init]      Initialize SSH keys store for the first time use
  -c [create]    Create a new SSH key
  -l [ls]        List all the available SSH keys
  -u [use]       Set specific SSH key as default by its alias name
  -d [delete]    Delete specific SSH key by alias name
  -b [backup]    Backup all SSH keys to an archive file
  -r [restore]   Restore SSH keys from an existing archive file

Please enjoy it!
```

### 第一次使用

第一次使用先进行初始化 SSH 密钥的存储。

得益于规划统一，有规则的目录存储，SSH 的管理变得清晰明了，有条理。

```
$ skm -i
✔ SSH key store initialized!
Key store location is: /Users/zinaer/.skm/
```

Zinaer SKM 将创建目录 $HOME/.skm 存储所有的 SSH 密钥。

> 说明：如果你在 $HOME/.ssh 目录已经有 id_rsa 和 id_rsa.pub 密钥，Zinaer SKM 会移动它们到 $HOME/.skm/default 目录。

与现有使用平滑兼容，不中断用户当前的使用，这个是 Zinaer SKM 的态度。

### 创建一对新的 SSH 密钥

> 说明：当前只支持 RSA 密钥类型。

```
$ skm -c zinaer

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Generating public/private rsa key pair.
Your identification has been saved in /Users/zinaer/.skm/zinaer/id_rsa.
Your public key has been saved in /Users/zinaer/.skm/zinaer/id_rsa.pub.
The key fingerprint is:
...
✔ SSH key [zinaer] created!
```

采用别名的设计，完全不用头疼冗长的目录文件路径记忆，交给 Zinaer SKM 就好了。

### 查看所有的 SSH 密钥

```
$ skm -l

✔ Found 2 SSH key(s)!

default ✔
zinaer
```

便于随时掌握已有的密钥，得益于别名设计，清晰明了。

### 设置默认使用的 SSH 密钥

```
$ skm -u zinaer
Now using SSH key: zinaer
```

这个命令彻底解决了，笔者之前遇到的痛点，依托别名随时快速切换默认密钥，代替了 SSH 官方的固定死的 config 配置模式。

### 删除 SSH 密钥

```
$ skm -d zinaer

SSH key [zinaer] is currently in use, please confirm to delete it [y/n]:y
✔ SSH key [zinaer] deleted!
```

不用的密钥随时删除，判断是否是当前正在使用的默认密钥，良好的二次确认，可有效降低密钥管理的误操作性。要知道，服务器管理，为了提高安全性，禁用账号密码登录，采用 SSH 密钥连接是通用做法。一旦密钥误删，你将和你的服务器失联。

### 备份密钥

备份 $HOME 目录下所有的 SSH 密钥。

```
$ skm -b

a .
a ./default
a ./default/id_rsa
a ./default/id_rsa.pub

✔  All SSH keys backup to: /Users/zinaer/skm-20171024131446.tar.gz
```

备份是一个好习惯，在你误删密钥的情况下，它就是你的救命稻草。采用年月日时分秒的命名方式，方便随时恢复不同时期的密钥。在初始化后，在创建完密钥后，进行及时的备份，是个不错的选择。

### 恢复密钥

```
$ skm -r ~/skm-20171024131446.tar.gz

x ./
x ./default/
x ./default/id_rsa
x ./default/id_rsa.pub
✔  All SSH keys restored to /Users/zinaer/.skm
```

有备份，就应该有恢复，同样是一条命令，你不必再花时间去甄别拷贝，Zinaer SKM 帮你搞定。

### 许可证

使用 MIT 许可证，这意味着你可以随意拷贝使用它。

人生苦短，我用 Python! Python 在 Mac OS X 和 Linux 平台作为默认软件被安装一直是传统，这是它可以用来开发命令行工具的巨大优势之一。Zinaer SKM 就是采用 Python 2.7 开发，默认即可支持在 Mac OS X 和各种 Linux 平台的运行。同时为了更加方便，笔者也将它们制作成了这两个平台专属的可执行文件。这一切都可以从官网：https://skm.zinaer.com 获取。