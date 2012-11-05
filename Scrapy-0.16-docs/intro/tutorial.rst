.. _intro-tutorial:

===============
Scrapy �ֲ�
===============

������Ѳ࣬���Ǽٶ�Scrapy�Ѿ���װ���ˣ����û�У��Ȱ�װ

����Ҫʹ��һ��������ʽ��Ŀ��domz)����Ϊ���ǵ�ץȡ����վ������

����ֲ��֪����������������

1. ����һ��scrapy��Ŀ��
2. ��������ȡ��items��
3. дһ����������ȡ���ݡ�
4. дһ���ܵ����洢��ȡ��items��

Scrapyʹ��pythonд�ģ�����������֣��������õ�scrapy�����������ʲô���ģ������ǳ���Ϥ������ԣ������ѧϰpython���Ƽ���Dive Into Python������������µı���ߣ����python��ʼ����������Ķ�û�б�̾�����Ƽ���Դ��

.. _Python: http://www.python.org
.. _this list of Python resources for non-programmers: http://wiki.python.org/moin/BeginnersGuide/NonProgrammers
.. _Dive Into Python: http://www.diveintopython.org

����һ����Ŀ
==================

�ڿ�ʼץȡ֮ǰ������Ҫ����һ��Scrapy��Ŀ������һ��Ŀ¼��������������

   scrapy startproject tutorial

�ᴴ��һ����tutorial����Ŀ¼����������Ŀ¼���ݣ�

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

�����˼: 

* ``scrapy.cfg``: ��Ŀ�����ļ�
* ``tutorial/``: ��Ŀ��pythonģ�飬��Ҫ�����������Ĵ��롣
* ``tutorial/items.py``: ��Ŀ��items�ļ���
* ``tutorial/pipelines.py``: ��Ŀ�Ĺܵ��ļ�
* ``tutorial/settings.py``: ��Ŀsettings file.
* ``tutorial/spiders/``: ����Ŀ¼��

����item.
=================

Items ����Ҫ���ص���ȡ����,���Ǵ����Ǽ򵥵�python�ֵ�,���ǰ���һЩ���������ݵı���,��ֹ����.

���Ǵ���һ��class'scrapy.item.Item',������һ������'scrapy.item.Field',��ORM(������ϤORMSҲû��ϵ,��ῴ�����Ǻܼ򵥵�).

���ǿ�ʼ��item����mobdel,��dmoz.org��ȥ���ݣ���Ҫ����name,url�����������Ƕ�������������Ϊ�����ֶΡ������������Ǳ༭items.py������'tutorial'Ŀ¼�����ǵ�Items�������������

    from scrapy.item import Item, Field

    class DmozItem(Item):
        title = Field()
        link = Field()
        desc = Field()
        
�տ�ʼ������е㸴�ӣ����Ƕ�����item��������ʹ�������齨��������ֻ��֪��item�Ϳ����ˡ�

��һ������
================

��������д�࣬���ڴ���վ��ץ����Ϣ��

����һ������URLs��list�������ȡlink���ͷ���ҳ��Ҫ��ȡ�����ݣ�����topics-items��

����һ�����棬����Ҫ�̳�`scrapy.spider.BaseSpider`������������Ҫ�ģ�ǿ���ԣ����ԣ�

* :attr:`~scrapy.spider.BaseSpider.name`: ʶ�����棬����Ψһ���ڲ�ͬ���������治��������ͬ�����֡�

* :attr:`~scrapy.spider.BaseSpider.start_urls`: ����Ҫ��ȡ��urls�����ԣ�Ҫ���ص�ҳ����������棬�����urls����̵Ĳ������ݡ�

* :meth:`~scrapy.spider.BaseSpider.parse` ��һ������ķ��������ص�ʱ�����`~scrapy.http.Response` ��������ʼURL����Ӧ����Ϊһ���������ݸ����������
 
  ���������ְ���ǽ�����Ӧ�����ݣ���ȡҪץȡ�����ݣ����scrpaed item����

  The :meth:`~scrapy.spider.BaseSpider.parse` �����������ص���ȡ���ݡ�

This is the code for our first Spider; save it in a file named�������ǵĵ�һ������Ĵ��룻����Ϊһ���ļ�`dmoz_spider.py`����`dmoz/spiders`Ŀ¼�£�

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

��ȡ
--------

�����ǵ����湤��������Ŀͬ��Ŀ¼�����У�

   scrapy crawl dmoz

�����Ӫ��ץȡ'dmoz.org'��վ�����õ��������ƵĽ����

   2008-08-20 03:51:13-0300 [scrapy] INFO: Started project: dmoz
   2008-08-20 03:51:13-0300 [tutorial] INFO: Enabled extensions: ...
   2008-08-20 03:51:13-0300 [tutorial] INFO: Enabled downloader middlewares: ...
   2008-08-20 03:51:13-0300 [tutorial] INFO: Enabled spider middlewares: ...
   2008-08-20 03:51:13-0300 [tutorial] INFO: Enabled item pipelines: ...
   2008-08-20 03:51:14-0300 [dmoz] INFO: Spider opened
   2008-08-20 03:51:14-0300 [dmoz] DEBUG: Crawled <http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/> (referer: <None>)
   2008-08-20 03:51:14-0300 [dmoz] DEBUG: Crawled <http://www.dmoz.org/Computers/Programming/Languages/Python/Books/> (referer: <None>)
   2008-08-20 03:51:14-0300 [dmoz] INFO: Spider closed (finished)

ע�����[dmoz]���У�����������Ϣ������Կ���һ������'start_urls'.������ЩURLs��ʼ��һ��������û���������ӡ�

����˼���ǣ�'parse'�����������ļ���������*Books* and *Resources*,����urls�����ݡ�

�����·�����ʲô?
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
