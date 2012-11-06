.. _intro-tutorial:

===============
Scrapy 手册
===============

在这个搜侧，我们假定Scrapy已经安装好了，如果没有，先安装

我们要使用一个‘开放式项目（domz)’作为我们的抓取的网站样例。

这个手册会知道你完成下面的任务：

1. 创建一个scrapy项目。
2. 定义你提取的items。
3. 写一个爬虫来提取内容。
4. 写一个管道来存储提取的items。

Scrapy使用python写的，如果你是新手，你可能想得到scrapy在这个语言是什么样的，如果你非常熟悉别的语言，想快速学习python，推荐‘Dive Into Python’，如果你是新的编程者，想从python开始，看看下面的对没有编程经验的推荐资源。

.. _Python: http://www.python.org
.. _this list of Python resources for non-programmers: http://wiki.python.org/moin/BeginnersGuide/NonProgrammers
.. _Dive Into Python: http://www.diveintopython.org

创建一个项目
==================

在开始抓取之前，你需要创建一个Scrapy项目，进入一个目录，运行下面的命令：

   scrapy startproject tutorial

会创建一个‘tutorial’的目录，包含下列目录内容：

   tutorial/
       scrapy.cfg
       tutorial/
           __init__.py
           items.py
           pipelines.py
           settings.py
           spiders/
               __init__.py 
               ... 

大概意思: 

* ``scrapy.cfg``: 项目配置文件
* ``tutorial/``: 项目的python模块，你要在这里包含你的代码。
* ``tutorial/items.py``: 项目的items文件。
* ``tutorial/pipelines.py``: 项目的管道文件
* ``tutorial/settings.py``: 项目settings file.
* ``tutorial/spiders/``: 爬虫目录。

定义item.
=================

Items 包含要加载的爬取数据,他们大致是简单的python字典,但是包含一些对填入数据的保护,防止错误.

他们创建一个class'scrapy.item.Item',定义了一个属性'scrapy.item.Field',像ORM(并不熟悉ORMS也没关系,你会看到这是很简单的).

我们开始对item进行mobdel,从dmoz.org拿去数据，需要捕获name,url和描述，我们定义这三个属性为三个字段。这样做，我们编辑items.py，房子'tutorial'目录，我们的Items对象就是这样：

    from scrapy.item import Item, Field

    class DmozItem(Item):
        title = Field()
        link = Field()
        desc = Field()
        
刚开始这可能有点复杂，但是定义了item允许你在使用其他组建处理数据只需知道item就可以了。

第一个爬虫
================

爬虫用于写类，用于从网站上抓如信息。

定义一个下载URLs的list，如何提取link，和分析页面要提取的内容，看‘topics-items’

创建一个爬虫，你需要继承`scrapy.spider.BaseSpider`，定义三个主要的，强制性，属性：

* :attr:`~scrapy.spider.BaseSpider.name`: 识别爬虫，必须唯一，在不同的爬虫里面不能设置相同的名字。

* :attr:`~scrapy.spider.BaseSpider.start_urls`: 爬虫要爬取的urls，所以，要下载的页面会在这里面，后面的urls会相继的产生数据。

* :meth:`~scrapy.spider.BaseSpider.parse` ：一个爬虫的方法，下载的时候调用`~scrapy.http.Response` 对象来开始URL，响应数据为一个参数传递给这个方法。
 
  这个方法的职责是解析响应的数据，提取要抓取的数据（编程scrpaed item）。

  The :meth:`~scrapy.spider.BaseSpider.parse` 方法负责处理返回的爬取数据。

This is the code for our first Spider; save it in a file named这是我们的第一个爬虫的代码；保存为一个文件`dmoz_spider.py`，在`dmoz/spiders`目录下：

   from scrapy.spider import BaseSpider

   class DmozSpider(BaseSpider):
       name = "dmoz"
       allowed_domains = ["dmoz.org"]
       start_urls = [
           "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
           "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
       ]
        
       def parse(self, response):
           filename = response.url.split("/")[-2]
           open(filename, 'wb').write(response.body)

爬取
--------

让我们的爬虫工作，到项目同级目录，运行：

   scrapy crawl dmoz

这个民营是抓取'dmoz.org'网站，你会得到下面相似的结果：

   2008-08-20 03:51:13-0300 [scrapy] INFO: Started project: dmoz
   2008-08-20 03:51:13-0300 [tutorial] INFO: Enabled extensions: ...
   2008-08-20 03:51:13-0300 [tutorial] INFO: Enabled downloader middlewares: ...
   2008-08-20 03:51:13-0300 [tutorial] INFO: Enabled spider middlewares: ...
   2008-08-20 03:51:13-0300 [tutorial] INFO: Enabled item pipelines: ...
   2008-08-20 03:51:14-0300 [dmoz] INFO: Spider opened
   2008-08-20 03:51:14-0300 [dmoz] DEBUG: Crawled <http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/> (referer: <None>)
   2008-08-20 03:51:14-0300 [dmoz] DEBUG: Crawled <http://www.dmoz.org/Computers/Programming/Languages/Python/Books/> (referer: <None>)
   2008-08-20 03:51:14-0300 [dmoz] INFO: Spider closed (finished)

注意包含[dmoz]的行，这个爬虫的信息。你可以看到一样定义'start_urls'.由于这些URLs开始于一个，他们没有引用连接。

有意思的是，'parse'方法，两个文件被创建，*Books* and *Resources*,包含urls的内容。

引擎下发生了什么?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Scrapy 为每个URL创建`scrapy.http.Request`对象，使用`start_urls`属性，使用'parse'方法处理回调方法。

request都是任务，执行返回'response'对象，然后通过'parse'方法。

提取 Items
----------------

Selectors选择器介绍
^^^^^^^^^^^^^^^^^^^^^^^^^

有几个方法来提取网页数据，scrapy使用基于'XPath'的表达式的原理，对于更多的选择器信息和其他的原理，看<topics-selectors>

.. _XPath: http://www.w3.org/TR/xpath

这里有几个例子：

* ``/html/head/title``: 选取head里面的`<title>`元素里面的内容

* ``/html/head/title/text()``: 选取`<title>`里面的文本。

* ``//td``: 选取 ``<td>`` 元素

* ``//div[@class="mine"]``: 根据 `class="mine"`来选取`div`元素

这只是一组简单使用Xpath的例子，但是Xpath表达式有更多的作用，了解更多看：this XPath tutorial <http://www.w3schools.com/XPath/default.asp>

关于XPaths,scrapy提供了‘scrapy.selector.XPathSelector‘类，包含两个HtmlXPathSelector，XmlXPathSelector，用之前必须有Response 对象.

你可以看到选择器是由结点构成，所以实例化的选择器是和root结点想联系的。

选择器有三个方法(点击看完整的API)

* :meth:`~scrapy.selector.XPathSelector.select`: 放回一个选择器列表

* :meth:`~scrapy.selector.XPathSelector.extract`: 返回XPath选择器

* :meth:`~scrapy.selector.XPathSelector.re`: 返回一个unicode字符串


在shell里使用选择器
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

举例使用内置的选择器，需要在系统安装IPython（一个扩展的python终端）

开始一个shell，你需要项目的顶级目录，运行：

   scrapy shell http://www.dmoz.org/Computers/Programming/Languages/Python/Books/

shell会显示:

    [ ... Scrapy log here ... ]

    [s] Available Scrapy objects:
    [s] 2010-08-19 21:45:59-0300 [default] INFO: Spider closed (finished)
    [s]   hxs        <HtmlXPathSelector (http://www.dmoz.org/Computers/Programming/Languages/Python/Books/) xpath=None>
    [s]   item       Item()
    [s]   request    <GET http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
    [s]   response   <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
    [s]   spider     <BaseSpider 'default' at 0x1b6c2d0>
    [s]   xxs        <XmlXPathSelector (http://www.dmoz.org/Computers/Programming/Languages/Python/Books/) xpath=None>
    [s] Useful shortcuts:
    [s]   shelp()           Print this help
    [s]   fetch(req_or_url) Fetch a new request or URL and update shell objects
    [s]   view(response)    View response in a browser

    In [1]: 

shell执行完后，你得到抓取的响应在'respomse'参数，这是输入`response.body`，你会得到响应体，输入`response.headers`，你会得到响应头。

shell需要实例化两个选择器，一个HTML，一个XML，我们来试试：

   In [1]: hxs.select('//title')
   Out[1]: [<HtmlXPathSelector (title) xpath=//title>]

   In [2]: hxs.select('//title').extract()
   Out[2]: [u'<title>Open Directory - Computers: Programming: Languages: Python: Books</title>']

   In [3]: hxs.select('//title/text()')
   Out[3]: [<HtmlXPathSelector (text) xpath=//title/text()>]

   In [4]: hxs.select('//title/text()').extract()
   Out[4]: [u'Open Directory - Computers: Programming: Languages: Python: Books']

   In [5]: hxs.select('//title/text()').re('(\w+):')
   Out[5]: [u'Computers', u'Programming', u'Languages', u'Python']

提取数据
^^^^^^^^^^^^^^^^^^^

现在，我们提取些有用的信息。

你在终端输入`response.body`，监视代码，算出需要的XPaths获取，然而，检查原始的html代码会是一个乏味的任务，为了让任务简单，你可以使用firefox的扩展想filebug。

检查完页面的代码，你会找到网页对应的信息在<ul>里，事实上是第二个<ul>元素。

用以下代码，我们会选择每一个<li>元素。
code::

   hxs.select('//ul/li')

网站描述:

   hxs.select('//ul/li/text()').extract()

网站名字:

   hxs.select('//ul/li/a/text()').extract()

网站链接:

   hxs.select('//ul/li/a/@href').extract()

在已经说了，每个'select()'调用返回一个选择器列表，我们可以更深一层的使用'select()'去除深节点，我们使用属性：

   sites = hxs.select('//ul/li')
   for site in sites:
       title = site.select('a/text()').extract()
       link = site.select('a/@href').extract()
       desc = site.select('text()').extract()
       print title, link, desc

.. 注意::

   更多嵌入式及诶到哪选择器，看
   :ref:`topics-selectors-nesting-selectors` and
   :ref:`topics-selectors-relative-xpaths` in the :ref:`topics-selectors`
   文档

把代码加入项目::

   from scrapy.spider import BaseSpider
   from scrapy.selector import HtmlXPathSelector

   class DmozSpider(BaseSpider):
       name = "dmoz"
       allowed_domains = ["dmoz.org"]
       start_urls = [
           "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
           "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
       ]
       
       def parse(self, response):
           hxs = HtmlXPathSelector(response)
           sites = hxs.select('//ul/li')
           for site in sites:
               title = site.select('a/text()').extract()
               link = site.select('a/@href').extract()
               desc = site.select('text()').extract()
               print title, link, desc

现在抓取domz.org网站，你会看到输出，运行：

   scrapy crawl dmoz

使用Item
--------------

:class:`~scrapy.item.Item` 对象是python的字段，你可以访问它的字段（类的属性定义的简单一点）使用标准的字段语法：

   >>> item = DmozItem()
   >>> item['title'] = 'Example title'
   >>> item['title']
   'Example title'

爬虫返回预期的数据，填充到Item对象，所以，爬取最终的代码是这样：


   from scrapy.spider import BaseSpider
   from scrapy.selector import HtmlXPathSelector

   from tutorial.items import DmozItem

   class DmozSpider(BaseSpider):
      name = "dmoz"
      allowed_domains = ["dmoz.org"]
      start_urls = [
          "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
          "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
      ]
       
      def parse(self, response):
          hxs = HtmlXPathSelector(response)
          sites = hxs.select('//ul/li')
          items = []
          for site in sites:
              item = DmozItem()
              item['title'] = site.select('a/text()').extract()
              item['link'] = site.select('a/@href').extract()
              item['desc'] = site.select('text()').extract()
              items.append(item)
          return items

.. 注意:: 你可以看到这个爬虫的全貌在 dirbot_项目，地址：https://github.com/scrapy/dirbot

执行一个爬虫爬dmoz.org网站：

   [dmoz] DEBUG: Scraped from <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
        {'desc': [u' - By David Mertz; Addison Wesley. Book in progress, full text, ASCII format. Asks for feedback. [author website, Gnosis Software, Inc.\n],
         'link': [u'http://gnosis.cx/TPiP/'],
         'title': [u'Text Processing in Python']}
   [dmoz] DEBUG: Scraped from <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
        {'desc': [u' - By Sean McGrath; Prentice Hall PTR, 2000, ISBN 0130211192, has CD-ROM. Methods to build XML applications fast, Python tutorial, DOM and SAX, new Pyxie open source XML processing library. [Prentice Hall PTR]\n'],
         'link': [u'http://www.informit.com/store/product.aspx?isbn=0130211192'],
         'title': [u'XML Processing with Python']}

存储爬取数据
========================

最简单的存储是使用<topics-feed-exports>，下面命令：

    scrapy crawl dmoz -o items.json -t json

这个生成序列化成json的'items.json'文件包含所有的爬取items。

在小项目里（像这个手册），知识就够了，然而，如果你想执行更复杂的爬取，你要看<topics-item-pipeline>，对于Items，一个Pipelines的占位符文件在项目创建的时候生成，在`tutorial/pipelines.py`，如果你只需要抓取和存储数据，则不需要使用任何管道方面的东西。

下一步
==========
           
这个手册覆盖最基本的Scrapy，但是很多其他特性没有被提到，`topics-whatelse`和`intro-overview`章节有重要内容的快速简介。

现在，我们推荐你继续使用示例项目（`intro-examples`），接下来继续`section-basics`

.. _JSON: http://en.wikipedia.org/wiki/JSON
.. _dirbot: https://github.com/scrapy/dirbot
