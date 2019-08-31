---
title: Vagrant 的那些事儿
date: 2017-10-30 06:12:10
category: 工具
tags: 开发
---
Vagrant 是一款用于构建及配置虚拟开发环境的软件，基于 Ruby，主要以命令行的方式运行。

## 为什么选择虚拟开发环境?

操作系统默认提供的软件栈，系统内置的软件往往都是较旧的版本，系统的升级容易导致相关软件被覆盖，我们应该使用虚拟机搭建本地开发环境，安全地与本地操作系统隔离开。虚拟机是使用软件模拟的操作系统，表现与单独的计算机完全一样。虚拟机使用的操作系统与生产服务器一样，能避免由操作系统所用软件不同而导致意料之外的开发和运行时错误。

创建和管理虚拟机的软件程序有很多，有些是商用的，例如 VMWare Fusion 和 Parallels，有些则是开源的，例如 VirtualBox。VirtualBox 免费，而且足够强大，提供了适用于各种操作系统的安装程序。我们选择它。

VirtualBox 虽然能创建虚拟机，但没有提供用户友好的界面，无法轻易地启动，配置、停止和销毁虚拟机。这里我们可以使用 Vagrant，它使用用户友好的命令行接口补足（并抽象）了 VirtualBox，我们只需执行一个命令就能创建、启动、停止和销毁 VirtualBox 虚拟机。Vagrant 提供了适用于各种操作系统的 GUI 安装程序。

## 怎么使用

VirtualBox 和 Vagrant 的安装都很简单，像安装普通软件一样安装就好了。安装好之后，我们可以在终端应用中执行 vagrant 命令创建、配置、启动、停止和销毁 VirtualBox 虚拟机。下面是最常使用的 Vagrant 命令：

```
vagrant init // 在当前工作目录中新建 Vagrantfile 配置脚本。我们使用这个脚本配置虚拟机的属性，保存所有配置细节。
vagrant up // 创建或启动虚拟机。
vagrant provision // 使用指定的配置脚本配置虚拟机。
vagrant ssh // 通过 SSH 登录虚拟机。
vagrant halt // 停止虚拟机。
vagrant destroy // 销毁虚拟机。
vagrant global-status // 查看宿主机所有的虚拟机信息。
```

Vagrant 容器（box）是预先配置好的虚拟机，可以在此基础上进一步配置，来满足我们的需求。有些容器是空盒，有些容器则为特定类型的应用提供了完整的软件栈。我们可以访问 Hashicorp Atlas 浏览可用的容器。

### 初步创建一个可用的虚拟机

找到所需的 Vagrant 容器后，在终端应用中进入相应的工作目录，然后执行下述命令初始化，新建 Vagrantfile 文件：

```
$ vagrant init
```

用你喜欢的文本编辑器中打开这个文件，这个文件中的内容是用 Ruby 编写的，不过易于理解，找到 `config.vm.box` 设置，将其值改为你使用的 Vagrant 容器名。例如，我选择 Ubuntu 容器，会把这个值改为 ubuntu/xenial64。更新后，Vagrantfile 文件中应该有下面这行：

```
config.vm.box = "ubuntu/xenial64"
```

然后，去掉下面这行的注释，以便通过本地网络中 192.168.33.10 这个 IP 地址在 Web 浏览器中访问虚拟机：

```
config.vm.network "private_network", ip: "192.168.33.10"
```

最后，执行下述命令，创建并启动这个虚拟机：

```
$ vagrant up
```

这个命令会下载远程 Vagrant 容器（如果需要下载的话），然后基于这个容器新建 VirtualBox 虚拟机。这样你就初步有了一台可用的虚拟机。

## 其它你需要知道的

### 控制 CPU 和内存

```
config.vm.provider "virtualbox" do |vb| 
  vb.memory = "1024
  vb.cpus = "2"
end
```

上述配置，设置内存为 1024MB，CPU 是两核的。

### 图形界面

如果你是一个新手，开始，使用一个拥有图像界面的虚拟机，也许是个不错的选择。

```
config.vm.provider "virtualbox" do |vb| 
  # Display the VirtualBox GUI when booting the machine 
  vb.gui = true  
end
```

### 创建快照

Vagrant 支持安装插件，进行自身功能的扩展。快照是一个不错的插件，在做重要操作前，创建快照，可以方便失误后快速恢复。

### 同步文件夹

不管使用什么配置工具，通常都会把本地设备中的项目目录与虚拟机中的一个目录对应起来。本地的任何变动都会立即同步到虚拟机。我们可以去掉 Vagrantfile 文件中下面这行的注释，在本地和虚拟机之间同步目录。

```
config.vm.synced_folder ".", "/vagrant_data"
```

第一个参数（.）是相对 Vagrantfile 配置文件的本地路径；第二个参数（／vagrant_data）是虚拟机中的绝对路径，这个目录会和本地目录对应起来。OS X 用户应该使用 NFS 方式同步文件夹，方法是把 config.vm.synced_folder 那一行设置为：

```
config.vm.synced_folder ".", "/vagrant_data", type: "nfs"
```

虚拟机创建和配置完成后，就可以像操作任何相同 Linux 发行版本一样，进行相关环境的部署，开展高效的开发工作。