.. _topics-spiders:

=======
爬虫
=======

爬虫是一个类，定义一个明确网站（或一组网站）会被爬取，包含怎么样进行检索（例如：跟随链接）和
怎样从页面提取数据结构（例如：爬取items）。换句话说，spider就是定义自定义行为来抓取和解析页面对特定网站（或，在有些情况是一组网站）。

对伊spiders，循环爬取通过下面实现：

1. 你开始产生初始化请求来抓取第一个urls，指定一个response函数为回调函数来下载这些请求。

   第一个requests执行来获得调用   :meth:`~scrapy.spider.BaseSpider.start_requests` 
   方法产生 :class:`~scrapy.http.Request` 于指定的URLs 在  :attr:`~scrapy.spider.BaseSpider.start_urls` 
   和  :attr:`~scrapy.spider.BaseSpider.parse` 作为request的回调方法。

2. 在回调方法，你解析一个响应（或页面）然后返回一个  :class:`~scrapy.item.Item` 对象,
   :class:`~scrapy.http.Request` 对象,  or 两个的迭代器. 这些请求会包含一个回调（或相同的）会通过Scrapy的特定回调下载它们的响应handled。

3. 在回调方法，你解析页面内容，通常使用:ref:`topics-selectors` （你可以使用BeautifuSoup,lxml或者你喜欢的其它方法）
   来产生items包含解析数据。

4. 最后，items返回爬虫会通常存储一个数据库（在某些情况下：:ref:`Item Pipeline <topics-item-pipeline>`）或写到一个文件里使用 :ref:`topics-feed-exports`.

尽管循环应用任何种类的spider，不同的默认spiders捆绑入Scrapy有不同的目的。我们在这讲解一些这些类型.

.. _spiderargs:

Spider 参数
================

Spiders可以接受参数来修改它们的行为。很多常见的spider参数定义起始的URLs来或者限制爬取指定的网站，但是他们可以配置任何spider。

Spider参数传递给 :command:`crawl` 命令行，使用``-a`` 选项，例如::

    scrapy crawl myspider -a category=electronics

Spiders在它的结构体里接受参数::

    class MySpider(BaseSpider):
        name = 'myspider'

        def __init__(self, category=None):
            self.start_urls = ['http://www.example.com/categories/%s' % category]
            # ...

Spider参数可以通过 :ref:`scrapyd-schedule` API传递.

.. _topics-spiders-ref:

内置 spiders 文档
==========================

Scrapy带有一些有用的spiders可以使用，都是spiders的子类。他们为了提供方便的方法来实现一些常见的爬取类子，
像跟随链接爬取规则，或解析一个XML/CSV 源

例如，使用下面的spiders，我们假定你有意个叫 ``TestItem`` 定义一个 ``myproject.items`` 模块::

    from scrapy.item import Item

    class TestItem(Item):
        id = Field()
        name = Field()
        description = Field()


.. module:: scrapy.spider
   :synopsis: Spiders基本类，spider manager and spider middleware

BaseSpider
----------

.. class:: BaseSpider()

   这时最简单的spider，每一个spider都要继承它（都要绑定Scrapy，或者写自己的）他不提供特定的方法，只有请求
    ``start_urls``/``start_requests``,调用spider的 ``parse`` 来响应请求。

   .. attribute:: name
      
	   定义spider的名字，名字代表Scrapy的spider如何实现的。所以需要是唯一的。然而，实例化相同的spider。
	   这是非常重要的属性。

	   如果spider抓单个网站，一个常用实践是域名为爬虫的名字，或不带 `TLD`_. 所以，例如，spider爬取
	   ``mywebsite.com`` 会被叫做 ``mywebsite``.

   .. attribute:: 要爬取的域名

	   字符串list允许爬取的域名。不在的URLs中指定的将不被抓取，在
       :class:`~scrapy.contrib.spidermiddleware.offsite.OffsiteMiddleware` 开启的时候。

   .. attribute:: start_urls

	   一个启动的URLs的list，当没有指定的URLs。所有，下载的第一列会出现在这里。随后的产生的URLs会从开始的URLs中产生。

   .. method:: start_requests()

       返回第一个爬取的请求的迭代其。
      
	   这个方法叫做Scrapy，在spider开始爬取，如果urls指定了。 :meth:`make_requests_from_url` 
	   用来代替请求。这个方法调用Scrapy，所以它安全。

       The default implementation uses :meth:`make_requests_from_url` to
       generate Requests for each url in :attr:`start_urls`.

       If you want to change the Requests used to start scraping a domain, this is
       the method to override. For example, if you need to start by logging in using
       a POST request, you could do::

           def start_requests(self):
               return [FormRequest("http://www.example.com/login", 
                                   formdata={'user': 'john', 'pass': 'secret'},
                                   callback=self.logged_in)]

           def logged_in(self, response):
               # here you would extract links to follow and return Requests for
               # each of them, with another callback
               pass

   .. method:: make_requests_from_url(url)

       A method that receives a URL and returns a :class:`~scrapy.http.Request`
       object (or a list of :class:`~scrapy.http.Request` objects) to scrape. This
       method is used to construct the initial requests in the
       :meth:`start_requests` method, and is typically used to convert urls to
       requests.

       Unless overridden, this method returns Requests with the :meth:`parse`
       method as their callback function, and with dont_filter parameter enabled
       (see :class:`~scrapy.http.Request` class for more info).

   .. method:: parse(response)

       This is the default callback used by Scrapy to process downloaded
       responses, when their requests don't specify a callback.

       The ``parse`` method is in charge of processing the response and returning
       scraped data and/or more URLs to follow. Other Requests callbacks have
       the same requirements as the :class:`BaseSpider` class.

       This method, as well as any other Request callback, must return an
       iterable of :class:`~scrapy.http.Request` and/or
       :class:`~scrapy.item.Item` objects.

       :param response: the response to parse
       :type reponse: :class:~scrapy.http.Response`

   .. method:: log(message, [level, component])

       Log a message using the :func:`scrapy.log.msg` function, automatically
       populating the spider argument with the :attr:`name` of this
       spider. For more information see :ref:`topics-logging`.


BaseSpider example
~~~~~~~~~~~~~~~~~~

Let's see an example::

    from scrapy import log # This module is useful for printing out debug information
    from scrapy.spider import BaseSpider

    class MySpider(BaseSpider):
        name = 'example.com'
        allowed_domains = ['example.com']
        start_urls = [
            'http://www.example.com/1.html',
            'http://www.example.com/2.html',
            'http://www.example.com/3.html',
        ]

        def parse(self, response):
            self.log('A response from %s just arrived!' % response.url)

Another example returning multiples Requests and Items from a single callback::

    from scrapy.selector import HtmlXPathSelector
    from scrapy.spider import BaseSpider
    from scrapy.http import Request
    from myproject.items import MyItem

    class MySpider(BaseSpider):
        name = 'example.com'
        allowed_domains = ['example.com']
        start_urls = [
            'http://www.example.com/1.html',
            'http://www.example.com/2.html',
            'http://www.example.com/3.html',
        ]

        def parse(self, response):
            hxs = HtmlXPathSelector(response)
            for h3 in hxs.select('//h3').extract():
                yield MyItem(title=h3)

            for url in hxs.select('//a/@href').extract():
                yield Request(url, callback=self.parse)

.. module:: scrapy.contrib.spiders
   :synopsis: Collection of generic spiders

CrawlSpider
-----------

.. class:: CrawlSpider

   这个是抓取有规律的网站最常用的办法，它提供方便的结构来匹配一组规则。对于你的项目，
   它可能不是最好的，但是它在大多数的情况下足够用了，所以你可以使用它自定义的方法来
   覆写，或者直接重写你自己的spider来使用。

   一部分属性继承与BaseSpider，这个类提供了一个新的属性：

   .. 属性:: rules
   
	   是一个或者多个Rule对象，每个Rule对象定义一个确定的行为来抓取网站，Ruls对象在下面有描述，
	   如果多个rules匹配了同一个link，就使用属性中第一个匹配的。

       
Crawling 规则
~~~~~~~~~~~~~~

.. class:: Rule(link_extractor, callback=None, cb_kwargs=None, follow=None, process_links=None, process_request=None)

   ``link_extractor`` 是一个链接抽取器，定义页面中哪种link会被抽取。
      
   ``callback`` 回调方法或者一个string（需要是一个spider的方法名字）来被每个链接抽取器来调用。
   这个回调接受一个response作为它的第一个参数，返回包含Item的list或者一个Request的对象（或它们的子类）。

   .. warning:: 当写spider的规则是，避免使用 parse 作为回调，所以如果覆写crawl爬虫的 parse 方法，那么它就不会工作了。

   ``cb_kwargs`` 一个dict ， 包含要传递给回调方法的关键字 。

   ``follow`` 是布尔型，链接需要郡守每个相应规则，如果callback是空，follow默认的True，否则为False。

   ``process_links`` 可调用的方法或者字符串，主要目的用来过滤链接。

   ``process_request`` 可调用，在request提取规则的时候调用，需要返回一个request或者None（没匹配上）

CrawlSpider example
~~~~~~~~~~~~~~~~~~~

Let's now take a look at an example CrawlSpider with rules::

    from scrapy.contrib.spiders import CrawlSpider, Rule
    from scrapy.contrib.linkextractors.sgml import SgmlLinkExtractor
    from scrapy.selector import HtmlXPathSelector
    from scrapy.item import Item

    class MySpider(CrawlSpider):
        name = 'example.com'
        allowed_domains = ['example.com']
        start_urls = ['http://www.example.com']
        
        rules = (
            # Extract links matching 'category.php' (but not matching 'subsection.php') 
            # and follow links from them (since no callback means follow=True by default).
            Rule(SgmlLinkExtractor(allow=('category\.php', ), deny=('subsection\.php', ))),

            # Extract links matching 'item.php' and parse them with the spider's method parse_item
            Rule(SgmlLinkExtractor(allow=('item\.php', )), callback='parse_item'),
        )

        def parse_item(self, response):
            self.log('Hi, this is an item page! %s' % response.url)

            hxs = HtmlXPathSelector(response)
            item = Item()
            item['id'] = hxs.select('//td[@id="item_id"]/text()').re(r'ID: (\d+)')
            item['name'] = hxs.select('//td[@id="item_name"]/text()').extract()
            item['description'] = hxs.select('//td[@id="item_description"]/text()').extract()
            return item


这个爬虫会开始example的主页，手机类型链接和item链接，使用 parse_item 方法解析，对每个response,很多数据会通过XPath抽取
然后填入Item。

XMLFeedSpider
-------------

.. class:: XMLFeedSpider

    XMLFeedSpider is designed for parsing XML feeds by iterating through them by a
    certain node name.  The iterator can be chosen from: ``iternodes``, ``xml``,
    and ``html``.  It's recommended to use the ``iternodes`` iterator for
    performance reasons, since the ``xml`` and ``html`` iterators generate the
    whole DOM at once in order to parse it.  However, using ``html`` as the
    iterator may be useful when parsing XML with bad markup.

    To set the iterator and the tag name, you must define the following class
    attributes:  

    .. attribute:: iterator

        A string which defines the iterator to use. It can be either:

           - ``'iternodes'`` - a fast iterator based on regular expressions 

           - ``'html'`` - an iterator which uses HtmlXPathSelector. Keep in mind
             this uses DOM parsing and must load all DOM in memory which could be a
             problem for big feeds

           - ``'xml'`` - an iterator which uses XmlXPathSelector. Keep in mind
             this uses DOM parsing and must load all DOM in memory which could be a
             problem for big feeds

        It defaults to: ``'iternodes'``.

    .. attribute:: itertag

        A string with the name of the node (or element) to iterate in. Example::

            itertag = 'product'

    .. attribute:: namespaces

        A list of ``(prefix, uri)`` tuples which define the namespaces
        available in that document that will be processed with this spider. The
        ``prefix`` and ``uri`` will be used to automatically register
        namespaces using the
        :meth:`~scrapy.selector.XPathSelector.register_namespace` method.

        You can then specify nodes with namespaces in the :attr:`itertag`
        attribute.

        Example::
            
            class YourSpider(XMLFeedSpider):

                namespaces = [('n', 'http://www.sitemaps.org/schemas/sitemap/0.9')]
                itertag = 'n:url'
                # ...

    Apart from these new attributes, this spider has the following overrideable
    methods too:

    .. method:: adapt_response(response)

        A method that receives the response as soon as it arrives from the spider
        middleware, before the spider starts parsing it. It can be used to modify
        the response body before parsing it. This method receives a response and
        also returns a response (it could be the same or another one).

    .. method:: parse_node(response, selector)
       
        This method is called for the nodes matching the provided tag name
        (``itertag``).  Receives the response and an XPathSelector for each node.
        Overriding this method is mandatory. Otherwise, you spider won't work.
        This method must return either a :class:`~scrapy.item.Item` object, a
        :class:`~scrapy.http.Request` object, or an iterable containing any of
        them.

    .. method:: process_results(response, results)
       
        This method is called for each result (item or request) returned by the
        spider, and it's intended to perform any last time processing required
        before returning the results to the framework core, for example setting the
        item IDs. It receives a list of results and the response which originated
        those results. It must return a list of results (Items or Requests).


XMLFeedSpider example
~~~~~~~~~~~~~~~~~~~~~

These spiders are pretty easy to use, let's have a look at one example::

    from scrapy import log
    from scrapy.contrib.spiders import XMLFeedSpider
    from myproject.items import TestItem

    class MySpider(XMLFeedSpider):
        name = 'example.com'
        allowed_domains = ['example.com']
        start_urls = ['http://www.example.com/feed.xml']
        iterator = 'iternodes' # This is actually unnecesary, since it's the default value
        itertag = 'item'

        def parse_node(self, response, node):
            log.msg('Hi, this is a <%s> node!: %s' % (self.itertag, ''.join(node.extract())))

            item = Item()
            item['id'] = node.select('@id').extract()
            item['name'] = node.select('name').extract()
            item['description'] = node.select('description').extract()
            return item

Basically what we did up there was to create a spider that downloads a feed from
the given ``start_urls``, and then iterates through each of its ``item`` tags,
prints them out, and stores some random data in an :class:`~scrapy.item.Item`.

CSVFeedSpider
-------------

.. class:: CSVFeedSpider

   This spider is very similar to the XMLFeedSpider, except that it iterates
   over rows, instead of nodes. The method that gets called in each iteration
   is :meth:`parse_row`.

   .. attribute:: delimiter

       A string with the separator character for each field in the CSV file
       Defaults to ``','`` (comma).

   .. attribute:: headers
      
       A list of the rows contained in the file CSV feed which will be used to
       extract fields from it.

   .. method:: parse_row(response, row)
      
       Receives a response and a dict (representing each row) with a key for each
       provided (or detected) header of the CSV file.  This spider also gives the
       opportunity to override ``adapt_response`` and ``process_results`` methods
       for pre- and post-processing purposes.

CSVFeedSpider example
~~~~~~~~~~~~~~~~~~~~~

Let's see an example similar to the previous one, but using a
:class:`CSVFeedSpider`::

    from scrapy import log
    from scrapy.contrib.spiders import CSVFeedSpider
    from myproject.items import TestItem

    class MySpider(CSVFeedSpider):
        name = 'example.com'
        allowed_domains = ['example.com']
        start_urls = ['http://www.example.com/feed.csv']
        delimiter = ';'
        headers = ['id', 'name', 'description']

        def parse_row(self, response, row):
            log.msg('Hi, this is a row!: %r' % row)

            item = TestItem()
            item['id'] = row['id']
            item['name'] = row['name']
            item['description'] = row['description']
            return item


SitemapSpider
-------------

.. class:: SitemapSpider

    SitemapSpider allows you to crawl a site by discovering the URLs using
    `Sitemaps`_.

    It supports nested sitemaps and discovering sitemap urls from
    `robots.txt`_.

    .. attribute:: sitemap_urls

        A list of urls pointing to the sitemaps whose urls you want to crawl.

        You can also point to a `robots.txt`_ and it will be parsed to extract
        sitemap urls from it.

    .. attribute:: sitemap_rules

        A list of tuples ``(regex, callback)`` where:

        * ``regex`` is a regular expression to match urls extracted from sitemaps.
          ``regex`` can be either a str or a compiled regex object.

        * callback is the callback to use for processing the urls that match
          the regular expression. ``callback`` can be a string (indicating the
          name of a spider method) or a callable.

        For example::

            sitemap_rules = [('/product/', 'parse_product')]

        Rules are applied in order, and only the first one that matches will be
        used.

        If you omit this attribute, all urls found in sitemaps will be
        processed with the ``parse`` callback.

    .. attribute:: sitemap_follow

        A list of regexes of sitemap that should be followed. This is is only
        for sites that use `Sitemap index files`_ that point to other sitemap
        files.

        By default, all sitemaps are followed.


SitemapSpider examples
~~~~~~~~~~~~~~~~~~~~~~

Simplest example: process all urls discovered through sitemaps using the
``parse`` callback::

    from scrapy.contrib.spiders import SitemapSpider

    class MySpider(SitemapSpider):
        sitemap_urls = ['http://www.example.com/sitemap.xml']

        def parse(self, response):
            pass # ... scrape item here ...

Process some urls with certain callback and other urls with a different
callback::

    from scrapy.contrib.spiders import SitemapSpider

    class MySpider(SitemapSpider):
        sitemap_urls = ['http://www.example.com/sitemap.xml']
        sitemap_rules = [
            ('/product/', 'parse_product'),
            ('/category/', 'parse_category'),
        ]

        def parse_product(self, response):
            pass # ... scrape product ...

        def parse_category(self, response):
            pass # ... scrape category ...

Follow sitemaps defined in the `robots.txt`_ file and only follow sitemaps
whose url contains ``/sitemap_shop``::

    from scrapy.contrib.spiders import SitemapSpider

    class MySpider(SitemapSpider):
        sitemap_urls = ['http://www.example.com/robots.txt']
        sitemap_rules = [
            ('/shop/', 'parse_shop'),
        ]
        sitemap_follow = ['/sitemap_shops']

        def parse_shop(self, response):
            pass # ... scrape shop here ...

Combine SitemapSpider with other sources of urls::

    from scrapy.contrib.spiders import SitemapSpider

    class MySpider(SitemapSpider):
        sitemap_urls = ['http://www.example.com/robots.txt']
        sitemap_rules = [
            ('/shop/', 'parse_shop'),
        ]

        other_urls = ['http://www.example.com/about']

        def start_requests(self):
            requests = list(super(MySpider, self).start_requests())
            requests += [Request(x, callback=self.parse_other) for x in self.other_urls]
            return requests

        def parse_shop(self, response):
            pass # ... scrape shop here ...

        def parse_other(self, response):
            pass # ... scrape other here ...

.. _Sitemaps: http://www.sitemaps.org
.. _Sitemap index files: http://www.sitemaps.org/protocol.php#index
.. _robots.txt: http://www.robotstxt.org/
.. _TLD: http://en.wikipedia.org/wiki/Top-level_domain
