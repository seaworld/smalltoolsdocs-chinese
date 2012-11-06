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

Scrapy Ϊÿ��URL����`scrapy.http.Request`����ʹ��`start_urls`���ԣ�ʹ��'parse'��������ص�������

request��������ִ�з���'response'����Ȼ��ͨ��'parse'������

��ȡ Items
----------------

Selectorsѡ��������
^^^^^^^^^^^^^^^^^^^^^^^^^

�м�����������ȡ��ҳ���ݣ�scrapyʹ�û���'XPath'�ı��ʽ��ԭ�����ڸ����ѡ������Ϣ��������ԭ����<topics-selectors>

.. _XPath: http://www.w3.org/TR/xpath

�����м������ӣ�

* ``/html/head/title``: ѡȡhead�����`<title>`Ԫ�����������

* ``/html/head/title/text()``: ѡȡ`<title>`������ı���

* ``//td``: ѡȡ ``<td>`` Ԫ��

* ``//div[@class="mine"]``: ���� `class="mine"`��ѡȡ`div`Ԫ��

��ֻ��һ���ʹ��Xpath�����ӣ�����Xpath���ʽ�и�������ã��˽���࿴��this XPath tutorial <http://www.w3schools.com/XPath/default.asp>

����XPaths,scrapy�ṩ�ˡ�scrapy.selector.XPathSelector���࣬��������HtmlXPathSelector��XmlXPathSelector����֮ǰ������Response ����.

����Կ���ѡ�������ɽ�㹹�ɣ�����ʵ������ѡ�����Ǻ�root�������ϵ�ġ�

ѡ��������������(�����������API)

* :meth:`~scrapy.selector.XPathSelector.select`: �Ż�һ��ѡ�����б�

* :meth:`~scrapy.selector.XPathSelector.extract`: ����XPathѡ����

* :meth:`~scrapy.selector.XPathSelector.re`: ����һ��unicode�ַ���


��shell��ʹ��ѡ����
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

����ʹ�����õ�ѡ��������Ҫ��ϵͳ��װIPython��һ����չ��python�նˣ�

��ʼһ��shell������Ҫ��Ŀ�Ķ���Ŀ¼�����У�

   scrapy shell http://www.dmoz.org/Computers/Programming/Languages/Python/Books/

shell����ʾ:

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

shellִ�������õ�ץȡ����Ӧ��'respomse'��������������`response.body`�����õ���Ӧ�壬����`response.headers`�����õ���Ӧͷ��

shell��Ҫʵ��������ѡ������һ��HTML��һ��XML�����������ԣ�

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

��ȡ����
^^^^^^^^^^^^^^^^^^^

���ڣ�������ȡЩ���õ���Ϣ��

�����ն�����`response.body`�����Ӵ��룬�����Ҫ��XPaths��ȡ��Ȼ�������ԭʼ��html�������һ����ζ������Ϊ��������򵥣������ʹ��firefox����չ��filebug��

�����ҳ��Ĵ��룬����ҵ���ҳ��Ӧ����Ϣ��<ul>���ʵ���ǵڶ���<ul>Ԫ�ء�

�����´��룬���ǻ�ѡ��ÿһ��<li>Ԫ�ء�
code::

   hxs.select('//ul/li')

��վ����:

   hxs.select('//ul/li/text()').extract()

��վ����:

   hxs.select('//ul/li/a/text()').extract()

��վ����:

   hxs.select('//ul/li/a/@href').extract()

���Ѿ�˵�ˣ�ÿ��'select()'���÷���һ��ѡ�����б����ǿ��Ը���һ���ʹ��'select()'ȥ����ڵ㣬����ʹ�����ԣ�

   sites = hxs.select('//ul/li')
   for site in sites:
       title = site.select('a/text()').extract()
       link = site.select('a/@href').extract()
       desc = site.select('text()').extract()
       print title, link, desc

.. ע��::

   ����Ƕ��ʽ��������ѡ��������
   :ref:`topics-selectors-nesting-selectors` and
   :ref:`topics-selectors-relative-xpaths` in the :ref:`topics-selectors`
   �ĵ�

�Ѵ��������Ŀ::

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

����ץȡdomz.org��վ����ῴ����������У�

   scrapy crawl dmoz

ʹ��Item
--------------

:class:`~scrapy.item.Item` ������python���ֶΣ�����Է��������ֶΣ�������Զ���ļ�һ�㣩ʹ�ñ�׼���ֶ��﷨��

   >>> item = DmozItem()
   >>> item['title'] = 'Example title'
   >>> item['title']
   'Example title'

���淵��Ԥ�ڵ����ݣ���䵽Item�������ԣ���ȡ���յĴ�����������


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

.. ע��:: ����Կ�����������ȫò�� dirbot_��Ŀ����ַ��https://github.com/scrapy/dirbot

ִ��һ��������dmoz.org��վ��

   [dmoz] DEBUG: Scraped from <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
        {'desc': [u' - By David Mertz; Addison Wesley. Book in progress, full text, ASCII format. Asks for feedback. [author website, Gnosis Software, Inc.\n],
         'link': [u'http://gnosis.cx/TPiP/'],
         'title': [u'Text Processing in Python']}
   [dmoz] DEBUG: Scraped from <200 http://www.dmoz.org/Computers/Programming/Languages/Python/Books/>
        {'desc': [u' - By Sean McGrath; Prentice Hall PTR, 2000, ISBN 0130211192, has CD-ROM. Methods to build XML applications fast, Python tutorial, DOM and SAX, new Pyxie open source XML processing library. [Prentice Hall PTR]\n'],
         'link': [u'http://www.informit.com/store/product.aspx?isbn=0130211192'],
         'title': [u'XML Processing with Python']}

�洢��ȡ����
========================

��򵥵Ĵ洢��ʹ��<topics-feed-exports>���������

    scrapy crawl dmoz -o items.json -t json

����������л���json��'items.json'�ļ��������е���ȡitems��

��С��Ŀ�������ֲᣩ��֪ʶ�͹��ˣ�Ȼ�����������ִ�и����ӵ���ȡ����Ҫ��<topics-item-pipeline>������Items��һ��Pipelines��ռλ���ļ�����Ŀ������ʱ�����ɣ���`tutorial/pipelines.py`�������ֻ��Ҫץȡ�ʹ洢���ݣ�����Ҫʹ���κιܵ�����Ķ�����

��һ��
==========
           
����ֲḲ���������Scrapy�����Ǻܶ���������û�б��ᵽ��`topics-whatelse`��`intro-overview`�½�����Ҫ���ݵĿ��ټ�顣

���ڣ������Ƽ������ʹ��ʾ����Ŀ��`intro-examples`��������������`section-basics`

.. _JSON: http://en.wikipedia.org/wiki/JSON
.. _dirbot: https://github.com/scrapy/dirbot
