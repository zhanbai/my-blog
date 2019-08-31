---
title: LNMP 构建经典 Web 服务架构
date: 2012-06-15 16:03:54
category: 技术
tags: 开发
---
更新于 2017-03-16 14:59

## 什么是 LNMP 架构？

LNMP 是用于开发和部署 Web 应用程序的普遍存在的 LAMP 架构的变体。传统上，LAMP 由 Linux，Apache，MySQL 和 PHP 组成。由于其模块化特性，组件可以轻松地换出。使用 LNMP，Apache 被替换为轻量级但功能强大的 Nginx。

### Linux

Linux 是一种自由和开放源代码的类 UNIX 操作系统。在 LNMP 架构中最常用的两个 Linux 发行版是 Debian 和 Ubuntu。

### Nginx

Nginx 是一个免费的，开源的，高性能的 HTTP 服务器和反向代理，以及一个 IMAP / POP3 代理服务器。NGINX 以其高性能，稳定性，丰富的功能集，简单的配置和低资源消耗而闻名。

### MySQL

MySQL 是一个性能高、成本低、可靠性好、开放源代码的关系数据库管理系统。

### PHP

PHP 是一种被广泛应用的开放源代码的多用途脚本语言，它可嵌入到 HTML 中，尤其适合 web 开发。

## 先决条件

你需要在你的服务器上拥有一个具有 sudo 权限的常规非 root 账户。

在本教程，我们将使用 Ubuntu 16.04 LTS 作为演示服务器，此过程的所有软件将直接来自 Ubuntu 的默认包存储库。这意味着我们可以使用 apt 软件包管理套件来完成安装。

## 安装 Nginx Web 服务器

由于这是我们第一次使用 apt 这个命令，我们应该更新我们的本地包索引，然后我们可以安装服务器：

```
$ sudo apt update
$ sudo apt install nginx
```

在 Ubuntu 16.04 LTS 上，Nginx 的配置在安装后就开始运行。

如果你正在用 ufw 运行防火墙，你需要允许连接到 Nginx。

建议你启用只允许所需流量限制的最严格的配置文件。由于这里尚未为我的服务器配置 SSL，在本指南中，我只允许端口 80 上的流量。你可以通过以下命令启用此功能：

```
$ sudo ufw allow 'Nginx HTTP'
```

可以通过以下命令来验证更改：

```
$ sudo ufw status
```

你应该在现实的输出中看到允许的 HTTP 流量：

```
Status: active

To                         Action      From
--                         ------      ----
Nginx HTTP                 ALLOW       Anywhere                  
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

添加新的防火墙规则后，你可以通过在 Web 浏览器中访问服务器的域名或公共 IP 地址来测试服务器是否已经启动并运行。

如果你没有指向你服务器的域名，并且不知道你服务器的公共 IP 地址，可以通过在终端中键入以下命令找到它：

```
$ ip address show | grep inet\ | awk '{ print $2; }' | sed 's/\/.*$//'
```

这将打印出几个 IP 地址，你可以在浏览器中依次尝试。

同时，也可以通过互联网来获取：

```
$ curl -4 icanhazip.com
```

键入浏览器你获取到的地址，会访问到 Nginx 的默认页面：

```
http://server_domain_or_IP
```

如果你看到上面的页面，你已经成功安装了 Nginx。

## 安装 MySQL 来管理站点数据

你可以通过键入以下命令轻松安装：

```
$ sudo apt install mysql-server
```

你将被要求设置 root 密码以在 MySQL 系统中使用。

MySQL 数据库软件现已安装，但其配置尚未完全完成。

为了确保安装，我们可以运行一个简单的安全脚本，询问是否要修改一些不安全的默认值。通过键入以下内容开始脚本：

```
$ sudo mysql_secure_installation
```

系统将要求你输入 MySQL root 帐户设置的密码。接下来，将询问你是否要配置 VALIDATE PASSWORD PLUGIN。

> 警告：启用此功能是一个判断机制。如果启用，那么不符合指定条件的密码将被 MySQL 拒绝并报错。如果你使用弱密码与自动配置 MySQL 用户凭据的软件（如 phpMyAdmin 的 Ubuntu 软件包）结合使用，将会导致问题。可以安全地禁用验证，但是你应该始终对数据库凭据使用强的，唯一的密码。

回答 y 为是，或任何其它继续表示不启用。

```
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No:
```

如果你已启用验证，系统会要求你选择一个级别的密码验证。请记住，如果输入 2，是最强级别，在尝试设置不包含数字，大写和小写字母，特殊字符或基于常用字典单词的任何密码时，你将收到错误。

```
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG:1
```

如果你启用了密码验证，则会显示现有 root 密码的密码强度，并询问你是否要更改该密码。如果你对当前密码感到满意，请在提示符下输入 n 表示“否”：

```
Using existing password for root.

Estimated strength of the password: 100 
Change the password for root ? ((Press y|Y for Yes, any other key for No) : n
```

对于其余的问题，你应该按 Y，并在每个提示符下按 Enter 键。这将删除一些匿名用户和测试数据库，禁用远程根登录，并加载这些新规则，以便 MySQL 立即遵循我们所做的更改。

此时，你的数据库系统现在已设置，我们可以继续。

## 安装 PHP 进行动态内容生成

由于 Nginx 不包含本地 PHP 处理，像其它一些 Web 服务器一样，我们需要安装 php-fpm，即 “fastCGI 进程管理器”。我们将告诉 Nginx 将 PHP 请求传递给这个软件进行处理。

我们可以安装这个模块，并且还将获得一个额外的帮助包，它将允许 PHP 与我们的数据库后端进行通信。安装将拉入必要的 PHP 核心文件。通过键入以下内容执行此操作：

```
$ sudo apt install php php-mysql
```

### 配置 PHP 处理器

我们现在已经安装了我们的 PHP 组件，但是我们需要进行一些小的配置更改，以使我们的设置更安全。

使用 root 权限打开主配置文件：

```
$ sudo vim /etc/php/7.0/fpm/php.ini
```

我们在这个文件中找到参数 cgi.fix_pathinfo。如果将前面的（;）注释掉，默认设置将为 “1”。

这是一个非常不安全的设置，因为它告诉 PHP 尝试执行最近的文件，如果找不到请求的 PHP 文件。这基本上将允许用户执行不应该被允许执行的 PHP 请求。

我们将通过取消注释行并将其设置为 “0” 来更改这两个条件，如下所示：

```
cgi.fix_pathinfo=0
```

保存并关闭这个文件。

现在，我们只需要重新启动我们的 PHP 处理器就可以生效了，输入：

```
$ sudo /etc/init.d/php7.0-fpm restart
```

## 配置 Nginx 使用 PHP 处理器

现在，我们安装了所有必需的组件。唯一的配置更改我们仍然需要是告诉 Nginx 使用我们的 PHP 处理器的动态内容。

我们在服务器块级别上执行此操作（服务器块类似于 Apache 的虚拟主机）。键入以下命令打开默认的 Nginx 服务器块配置文件：

```
$ sudo vim /etc/nginx/sites-available/default
```

现在，不包含注释，Nginx 默认服务器块配置文件看起来如下：

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

我们需要对我们网站的此文件进行一些更改。

首先，我们需要添加 index.php 作为 index 指令的第一个值，以便 index.php 在请求目录时提供命名的文件（如果可用）。

我们可以修改 server_name 指令以指向我们服务器的域名或公共 IP 地址。

对于实际的 PHP 处理，我们只需要取消注释处理 PHP 请求的文件的一段。这将是 location ~\.php$ 位置块，包含的 fastcgi-php.conf 代码段和与之关联的套接字 php-fpm。

我们还将取消注释处理 .htaccess 文件的位置块。Nginx 不处理这些文件。如果任何这些文件碰巧进入文档根目录，则不应将其提供给访问者。

进行更改后如下：

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name server_domain_or_IP;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

进行上述更改后，你可以保存并关闭文件。

通过以下命令来测试配置文件是否有语法错误

```
$ sudo nginx -t
```

如果报告任何错误，请返回并重新检查你的文件，然后继续。

当你修改好，重新加载 Nginx 生效：

```
$ sudo /etc/init.d/nginx reload
```

## 创建一个 PHP 文件来测试配置

你的 LNMP 架构现在应该设置完成。我们可以测试它来验证 Nginx 可以正确处理 .php 文件。

我们可以通过在我们的文档根中创建一个测试 PHP 文件来实现。在文本编辑器中打开文档根目录中调用的新文件：

```
$ sudo vim /var/www/html/info.php
```

在新文件中键入或粘贴以下行。这是有效的 PHP 代码，将返回有关我们的服务器的信息：

```
<?php
phpinfo();
```

完成后，保存并关闭文件。

现在，你可以在网络浏览器中访问此页面，方法是访问你的服务器的域名或公共 IP 地址，然后访问 /info.php：

```
http://server_domain_or_IP/info.php
```

你应该看到一个由 PHP 生成的网页，其中包含有关你的服务器的信息，如果你看到一个类似这样的页面，你已经成功地设置了 PHP 处理 Nginx。

验证 Nginx 正确呈现页面后，最好删除你创建的文件，因为它实际上可能会向未经授权的用户提供有关你的配置的一些提示，这可能有助于他们尝试插入。如果以后需要，可以随时重新生成此文件。

现在，请通过键入以下命令删除该文件：

```
$ sudo rm /var/www/html/info.php
```

## 结论

你现在应该已经在你的 Ubuntu 16.04 LTS 服务器上配置了一个 LNMP 架构。这为你向访问者提供 Web 内容提供了非常灵活的基础。