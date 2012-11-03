.. _intro-overview:

==================
Scrapy at a glance
==================

Scrapy ��һ����վ����Ӧ�ÿ�ܣ��ṹ���������ڹ����������������ھ���Ϣ������ʷ���ݡ�

Scrapy������Ϊ��Ļץȡ�����ṩAPIs��ȡ��������ݣ�������ͨ���������档


���ĵ�����Scrapy����ĸ�������֪��������ι���Ȼ��ʹ������Ҫ�Ĺ��ܡ�

׼����ʼ�� :

ѡ��һ����ַ
==============

����Ҫ��ȡ��վ�ĺܶ���Ϣ��������վ���ṩ�κ�API���ߺͱ��ʽ�ķ��ʣ�Scarpy�����ṩ��ȡ��Щ��Ϣ��

�����ǿ�һ����ȡ��URL����ô�������������ļ��Ĵ�С��

���ҳ����Կ���������ӵ����µ�Ӱ���ӣ�

    http://www.mininova.org/today

������Ҫץ������
==================================

��һ�����鶨��Ҫץȡ�����ݡ�

This would be our Item::

    from scrapy.item import Item, Field

    class Torrent(Item):
        url = Field()
        name = Field()
        description = Field()
        size = Field()

дһ����������ȡ����
==================================

��������������дһ�����涨�忪ʼ��URL��(http://www.mininova.org/today��������links�͹����ҳ������ȡ���ݡ�

����㿴��һ��ҳ�����ݣ����ǻῴ�����еĵ�Ӱ����URLs����http://www.mininova.org/tor/NUMBER������Number��һ�����֣�����ʹ���������������ӣ�/tor/\d+

����Ҫʹ��XPath��ѡ��HTMLҳ����ҩ��ȡ�����ݣ�����������һ��һ������ҳ�棺

    http://www.mininova.org/tor/13206120

�����ҳ���Դ���룬 ����XPath��ѡ�����ݣ��������������ʹ�С��

��html�ļ�Դ���룬�����ܿ���һ��<h1>��ǩ�����棺

   <h1>Home[2009][Eng]XviD-ovd</h1>

һ���ڵ���ʽ��ȡ���ֿ��ԣ�

    //h1/text()

���������������ְ���<div>��ǩ����"id=description":

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

����ڵ�ѡ�����������

    //div[@id='description']

����ļ���С������<div>���<p>��ǩ"id=specifications":

   <div id="specifications">

   <p>
   <strong>Category:</strong>
   <a href="/cat/4">Movies</a> &gt; <a href="/sub/35">Documentary</a>
   </p>

   <p>
   <strong>Total size:</strong>
   699.79&nbsp;megabyte</p>


.. highlight:: none

ѡ��������������:

   //div[@id='specifications']/p[2]/text()[2]

����Ǵ�������

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

Ϊ�˼�࣬�������������Ҫ�Ķ�����������Ŀ������intro-overview-item�

������������ȡ����
==================================

�������������������ȡ��վ����Ϊһ��json��ʽ������ļ���scraped_data.json��
���������У���Ҫ��װ w3lib,Twisted�����û��crawl���ѡ����ƣ�������� scrapy fetch��

    scrapy crawl mininova.org -o scraped_data.json -t json

���Ϊһ����ͨ��json�ļ�������Ժ��ݵĵ�����ʽ��XML��CSV�����������洢��̨����Amazon s3��

��Ҳ����ʹ�ùܵ������浽���ݿ�

�ع�
===================

������顰scraped_data.json���ļ�����ῴ��ץȡ����Ŀ��

    [{"url": "http://www.mininova.org/tor/2657665", "name": ["Home[2009][Eng]XviD-ovd"], "description": ["HOME - a documentary film by ..."], "size": ["699.69 megabyte"]},
    # ... other items ...
    ]

���ע�⵽���е�ֵ��Ҫ��ȡ��url�����ڷ���Ŀ¼����һ��list������Ϊ���ص�lists����������һ���򵥵�ֵ����ִ�����ɾ����ճ��ֵ��

