.. _intro-overview:

==================
Scrapy at a glance
==================

Scrapy 是一个网站爬虫应用框架，结构化数据用于广阔的领域，像数据挖掘，信息处理，历史数据。

Scrapy最初设计为屏幕抓取，它提供APIs提取额外的数据，或者普通的网络爬虫。


本文档介绍Scrapy后面的概念，你可以知道他是如何工作然后使用你需要的功能。

准备开始吧 :

选择一个网址
==============

你需要提取网站的很多信息，但是网站不提供任何API或者和编程式的访问，Scarpy可以提供提取这些信息。

让我们看一下提取的URL，那么，描述和种子文件的大小。

这个页面可以看到今天添加的最新电影种子：

    http://www.mininova.org/today

定义你要抓的内容
==================================

第一件事情定义要抓取的数据。

This would be our Item::

    from scrapy.item import Item, Field

    class Torrent(Item):
        url = Field()
        name = Field()
        description = Field()
        size = Field()

写一个爬虫来提取数据
==================================

接下来的事情是写一个爬虫定义开始的URL（(http://www.mininova.org/today），按着links和规则从页面里提取数据。

如果你看了一下页面内容，我们会看到所有的电影种子URLs都像http://www.mininova.org/tor/NUMBER，其中Number是一个数字，我们使用正则来构建链接：/tor/\d+

我们要使用XPath来选择HTML页面中药提取的数据，让我们来看一下一个种子页面：

    http://www.mininova.org/tor/13206120

看这个页面的源代码， 构建XPath来选择数据：种子名，描述和大小。

看html文件源代码，我们能看到一个<h1>标签在里面：

   <h1>Home[2009][Eng]XviD-ovd</h1>

一个节点表达式提取名字可以：

    //h1/text()

接下来，描述部分包含<div>标签包含"id=description":

   <h2>Description:</h2>

   <div id="description">
   "HOME" - a documentary film by Yann Arthus-Bertrand
   <br/>
   <br/>
   ***
   <br/>
   <br/>
   "We are living in exceptional times. Scientists tell us that we have 10 years to change the way we live, avert the depletion of natural resources and the catastrophic evolution of the Earth's climate.

   ...

.. highlight:: none

这个节点选择可以这样：

    //div[@id='description']

最后，文件大小包含在<div>里的<p>标签"id=specifications":

   <div id="specifications">

   <p>
   <strong>Category:</strong>
   <a href="/cat/4">Movies</a> &gt; <a href="/sub/35">Documentary</a>
   </p>

   <p>
   <strong>Total size:</strong>
   699.79&nbsp;megabyte</p>


.. highlight:: none

选择描述可以这样:

   //div[@id='specifications']/p[2]/text()[2]

最后是代码内容

    class MininovaSpider(CrawlSpider):

        name = 'mininova.org'
        allowed_domains = ['mininova.org']
        start_urls = ['http://www.mininova.org/today']
        rules = [Rule(SgmlLinkExtractor(allow=['/tor/\d+']), 'parse_torrent')]
        
        def parse_torrent(self, response):
            x = HtmlXPathSelector(response)

            torrent = TorrentItem()
            torrent['url'] = response.url
            torrent['name'] = x.select("//h1/text()").extract()
            torrent['description'] = x.select("//div[@id='description']").extract()
            torrent['size'] = x.select("//div[@id='info-left']/p[2]/text()[2]").extract()
            return torrent

为了简洁，我们特意忽略重要的东西，种子项目定义在intro-overview-item里。

运行爬虫来提取数据
==================================

最后，我们运行爬虫来爬取网站，作为一个json格式的输出文件“scraped_data.json”
（不能运行，需要安装 w3lib,Twisted，最后还没有crawl这个选项，郁闷，最后用了 scrapy fetch）

    scrapy crawl mininova.org -o scraped_data.json -t json

这成为一个普通的json文件，你可以很容的导出格式（XML，CSV）或者其它存储后台（像Amazon s3）

你也可以使用管道了来存到数据库

回顾
===================

如果你检查“scraped_data.json”文件，你会看到抓取的项目：

    [{"url": "http://www.mininova.org/tor/2657665", "name": ["Home[2009][Eng]XviD-ovd"], "description": ["HOME - a documentary film by ..."], "size": ["699.69 megabyte"]},
    # ... other items ...
    ]

你会注意到所有的值（要提取的url都会在分配目录）是一个list，是因为返回的lists，你可能想存一个简单的值或者执行添加删除和粘贴值。

