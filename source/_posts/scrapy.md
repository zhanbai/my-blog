---
title: Scrapy 一个简单、快速、可扩展的爬虫框架
date: 2017-11-13 06:12:10
categories: 技术
tags: 爬虫
---
最近需要帮其他部门同事爬些数据，所以更加深入了解和使用了之前接触的一个爬虫框架，Scrapy。

Scrapy 是一个由 Python 开发的简单、快速、可扩展，并且开源的爬虫框架。可以应用在包括数据挖掘，信息处理或存储历史数据等一系列的程序中。

今天简单介绍下这个框架，也许哪一天你可能需要用到它。

## 通过一个例子来了解

为了展示 Scrapy 怎么获取你想要的数据，我们将通过一个简单的爬虫例子来说明。

下面是一个爬虫的代码，获取博客 [http://blog.zinaer.com](http://blog.zinaer.com/) 文章的标题和发表时间：

```
import scrapy

class ZinaerBlogSpider(scrapy.Spider):
    name = 'blog'
    start_urls = [
        'http://blog.zinaer.com'
    ]

    def parse(self, response):
        for data in response.css('ul.post-list li'):
            yield {
                'title': data.css('span.post-meta::text').extract_first(),
                'date': data.css('a.post-link::text').extract_first()
            }
```

将上述代码放到一个 python 文件中，保存为 blog_spider.py 并使用 `runspider` 命令执行它：

```
$ scrapy runspider blog_spider.py -o blog.json
```

这样，博客里文章的标题和发表时间就会以 JSON 的格式保存下来。

```
[
	{
        "title":"Nov 12, 2017",
        "date":"Go 语言八周年"
    },
    {
        "title":"Nov 11, 2017",
        "date":"人工智能领域最好的 5 种编程语言"
    },
    {
        "title":"Nov 10, 2017",
        "date":"Github 欢迎所有 CI 工具"
    },
    {
        "title":"Nov 9, 2017",
        "date":"几个掌握 CSS 网格布局的便捷资源"
    },
    {
    	...
    },
    {
        "title":"Oct 18, 2017",
        "date":"为什么你需要另一款 Markdown 编辑器？"
    },
    {
        "title":"Oct 17, 2017",
        "date":"WiFi WPA2 安全加密协议被破解，路由器等一大波设备可能沦陷？"
    }
]
```

## 这其中发生了什么

当你运行上述命令时，Scrapy 在程序中找到相关定义，并通过爬虫引擎运行它。

抓取通过向 `start_urls` 属性中定义的 URL 发出请求，并调用默认的回调方法 parse, 将响应对象作为参数传递。在 `parse` 回调中，我们使用 CSS 选择器循环引用元素。

你可以注意到 Scrapy 的一个主要优势，请求是异步调用和处理的。也就是说，不需要等待一个请求和处理完成，它可以发送另一个请求或者做其它事情。即使某些请求或处理失败，其它请求仍然会继续。

虽然并发请求可以快速爬取相关内容，但是 Scrapy 还可以通过其它一些设置控制爬虫的行为，比如，在每个请求直接设置延迟，限制请求域或每个 IP 的并发数，甚至可以自动调节这些行为。

上面的例子，导出了 JSON 文件，你也可以轻松导出其它格式，比如：XML 或 CSV 或者存储到后端以及数据库中。

## 还有什么

你已经看到如何使用 Scrapy 从网站中提取和存储项目，这只是基础。Scrapy 提供了许多强大的功能，使得爬取数据更加简单和高效，比如：

* 内置支持使用扩展 CSS 选择器和 XPath 表达式从 HTML/XML 选择和提取数据，使用正则表达式提取数据。
* 交互式 shell 控制台，这个在尝试 CSS 和 XPath 来抓取数据，编写和调试的时候非常有用。
* 内置支持多种格式输出，JSON, CSV, XML。并可以存储到多个后端，FTP，S3, 本地文件系统。
* 强大的编码支持和自动检测，用于处理外部的，非标准的编码声明。
* 强大的可扩展支持，允许你使用信号和定义良好的 API（中间件，扩展和管道）来增强自己的功能。
* 广泛的内置扩展和中间件处理：cookies 和 session 处理；HTTP 功能，如压缩，身份验证，缓存；user-agent 模拟；robots.txt；爬虫深度限制等等。
* Telnet 控制台，用于连接运行着 Scrapy 进程的 Python 控制台，从而编译和调试你的爬虫。