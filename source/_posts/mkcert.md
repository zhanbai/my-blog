---
title: mkcert 本地使用的 HTTPS 证书
date: 2019-01-09 09:26:09
categories: 工具
tags: HTTPS
---

![image](http://upload-images.jianshu.io/upload_images/1595196-3373f5f225a67bf3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

HTTPS 是 Web 发展的趋势，用于提高网站的安全性。使用 HTTPS 需要配置 TLS 证书，得益于 ACME 协议和 Let's Encrypt 证书，远程环境可以很容易部署。但是对于本地环境，还没有普遍有效的证书。

这是一个问题，因为越来越多的浏览器特性要求必须以 HTTPS 的方式访问，混合 HTTP 和 HTTPS 内容可能导致网站不可访问。本地环境使用 HTTPS 开发应该要像远程环境部署 HTTPS 一样简单。这就是今天推荐的工具 mkcert 的用途。

![image](http://upload-images.jianshu.io/upload_images/1595196-aba4cdf543d38975.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

mkcert 被设计的足够简单，隐藏了几乎所有生成 TLS 证书所必须的知识。它适用于任何主机名或者 IP，包括 localhost ，因为它只在你的本地环境使用。

证书是由你的私有 CA 签发，当你运行 `mkcert-install` 会自动配置这些信任，因此，当浏览器访问时，就会显示安全标识。目前支持 MacOS、Linux 和 Windows，以及 Firefox、Chrome 和 Java。甚至支持一些手机设备。

与 OpenSSL 不同的是，不需要为每个证书配置很多选项。mkcert 最主要的功能是作为开发者工具，聚焦于让本地环境配置 TLS 证书变得简单高效。

项目地址：https://github.com/FiloSottile/mkcert

