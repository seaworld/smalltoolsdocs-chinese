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

Scrapy creates :class:`scrapy.http.Request` objects for each URL in the
``start_urls`` attribute of the Spider, and assigns them the ``parse`` method of
the spider as their callback function.

These Requests are scheduled, then executed, and
:class:`scrapy.http.Response` objects are returned and then fed back to the
spider, through the :meth:`~scrapy.spider.BaseSpider.parse` method.

Extracting Items
----------------

Introduction to Selectors
^^^^^^^^^^^^^^^^^^^^^^^^^

There are several ways to extract data from web pages. Scrapy uses a mechanism
based on `XPath`_ expressions called :ref:`XPath selectors <topics-selectors>`.
For more information about selectors and other extraction mechanisms see the
:ref:`XPath selectors documentation <topics-selectors>`.

.. _XPath: http://www.w3.org/TR/xpath

Here are some examples of XPath expressions and their meanings:

* ``/html/head/title``: selects the ``<title>`` element, inside the ``<head>``
  element of a HTML document

* ``/html/head/title/text()``: selects the text inside the aforementioned
  ``<title>`` element.

* ``//td``: selects all the ``<td>`` elements

* ``//div[@class="mine"]``: selects all ``div`` elements which contain an
  attribute ``class="mine"``

These are just a couple of simple examples of what you can do with XPath, but
XPath expressions are indeed much more powerful. To learn more about XPath we
recommend `this XPath tutorial <http://www.w3schools.com/XPath/default.asp>`_.

For working with XPaths, Scrapy provides a :class:`~scrapy.selector.XPathSelector`
class, which comes in two flavours, :class:`~scrapy.selector.HtmlXPathSelector`
(for HTML data) and :class:`~scrapy.selector.XmlXPathSelector` (for XML data). In
order to use them you must instantiate the desired class with a
:class:`~scrapy.http.Response` object.

You can see selectors as objects that represent nodes in the document
structure. So, the first instantiated selectors are associated to the root
node, or the entire document.

Selectors have three methods (click on the method to see the complete API
documentation).

* :meth:`~scrapy.selector.XPathSelector.select`: returns a list of selectors, each of
  them representing the nodes selected by the xpath expression given as
  argument. 

* :meth:`~scrapy.selector.XPathSelector.extract`: returns a unicode string with
   the data selected by the XPath selector.

* :meth:`~scrapy.selector.XPathSelector.re`: returns a list of unicode strings
  extracted by applying the regular expression given as argument.


Trying Selectors in the Shell
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To illustrate the use of Selectors we're going to use the built-in :ref:`Scrapy
shell <topics-shell>`, which also requires IPython (an extended Python console)
installed on your system.

To start a shell, you must go to the project's top level directory and run::

   scrapy shell http://www.dmoz.org/Computers/Programming/Languages/Python/Books/

This is what the shell looks like::

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

After the shell loads, you will have the response fetched in a local
``response`` variable, so if you type ``response.body`` you will see the body
of the response, or you can type ``response.headers`` to see its headers.

The shell also instantiates two selectors, one for HTML (in the ``hxs``
variable) and one for XML (in the ``xxs`` variable) with this response. So let's
try them::

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

Extracting the data
^^^^^^^^^^^^^^^^^^^

Now, let's try to extract some real information from those pages. 

You could type ``response.body`` in the console, and inspect the source code to
figure out the XPaths you need to use. However, inspecting the raw HTML code
there could become a very tedious task. To make this an easier task, you can
use some Firefox extensions like Firebug. For more information see
:ref:`topics-firebug` and :ref:`topics-firefox`.

After inspecting the page source, you'll find that the web sites information
is inside a ``<ul>`` element, in fact the *second* ``<ul>`` element.

So we can select each ``<li>`` element belonging to the sites list with this
code::

   hxs.select('//ul/li')

And from them, the sites descriptions::

   hxs.select('//ul/li/text()').extract()

The sites titles::

   hxs.select('//ul/li/a/text()').extract()

And the sites links::

   hxs.select('//ul/li/a/@href').extract()

As we said before, each ``select()`` call returns a list of selectors, so we can
concatenate further ``select()`` calls to dig deeper into a node. We are going to use
that property here, so::

   sites = hxs.select('//ul/li')
   for site in sites:
       title = site.select('a/text()').extract()
       link = site.select('a/@href').extract()
       desc = site.select('text()').extract()
       print title, link, desc

.. note::

   For a more detailed description of using nested selectors, see
   :ref:`topics-selectors-nesting-selectors` and
   :ref:`topics-selectors-relative-xpaths` in the :ref:`topics-selectors`
   documentation

Let's add this code to our spider::

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

Now try crawling the dmoz.org domain again and you'll see sites being printed
in your output, run::

   scrapy crawl dmoz

Using our item
--------------

:class:`~scrapy.item.Item` objects are custom python dicts; you can access the
values of their fields (attributes of the class we defined earlier) using the
standard dict syntax like::

   >>> item = DmozItem()
   >>> item['title'] = 'Example title'
   >>> item['title']
   'Example title'

Spiders are expected to return their scraped data inside
:class:`~scrapy.item.Item` objects. So, in order to return the data we've
scraped so far, the final code for our Spider would be like this::

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

.. note:: You can find a fully-functional variant of this spider in the dirbot_
   project available at https://github.com/scrapy/dirbot

Now doing a crawl on the dmoz.org domain yields ``DmozItem``'s::

   [dmoz] DEBUG: Scraped from <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
        {'desc': [u' - By David Mertz; Addison Wesley. Book in progress, full text, ASCII format. Asks for feedback. [author website, Gnosis Software, Inc.\n],
         'link': [u'http://gnosis.cx/TPiP/'],
         'title': [u'Text Processing in Python']}
   [dmoz] DEBUG: Scraped from <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
        {'desc': [u' - By Sean McGrath; Prentice Hall PTR, 2000, ISBN 0130211192, has CD-ROM. Methods to build XML applications fast, Python tutorial, DOM and SAX, new Pyxie open source XML processing library. [Prentice Hall PTR]\n'],
         'link': [u'http://www.informit.com/store/product.aspx?isbn=0130211192'],
         'title': [u'XML Processing with Python']}

Storing the scraped data
========================

The simplest way to store the scraped data is by using the :ref:`Feed exports
<topics-feed-exports>`, with the following command::

    scrapy crawl dmoz -o items.json -t json

That will generate a ``items.json`` file containing all scraped items,
serialized in `JSON`_.

In small projects (like the one in this tutorial), that should be enough.
However, if you want to perform more complex things with the scraped items, you
can write an :ref:`Item Pipeline <topics-item-pipeline>`. As with Items, a
placeholder file for Item Pipelines has been set up for you when the project is
created, in ``tutorial/pipelines.py``. Though you don't need to implement any item
pipeline if you just want to store the scraped items.

Next steps
==========
           
This tutorial covers only the basics of Scrapy, but there's a lot of other
features not mentioned here. Check the :ref:`topics-whatelse` section in
:ref:`intro-overview` chapter for a quick overview of the most important ones.

Then, we recommend you continue by playing with an example project (see
:ref:`intro-examples`), and then continue with the section
:ref:`section-basics`.

.. _JSON: http://en.wikipedia.org/wiki/JSON
.. _dirbot: https://github.com/scrapy/dirbot
