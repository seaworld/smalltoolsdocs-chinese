.. _topics-index:

==============================
Scrapy |version| documentation
==============================

这篇文档包含所有你需要知道的Scrapy信息.

Getting help
============

碰到问题了？这里可以帮到你！

* 试试doc：`FAQ <faq>`--它会获得很多公共的问题。
* 一些特殊的信息？看:ref:`genindex` or :ref:`modindex`.
* 搜索‘scrapy用户的邮件列表’的信息，或者发一个问题。
* 在 #scrapy IRC channel`_ 问问题。
* 在`issue tracker`_汇报bug.

.. _archives of the scrapy-users mailing list: http://groups.google.com/group/scrapy-users/
.. _post a question: http://groups.google.com/group/scrapy-users/
.. _#scrapy IRC channel: irc://irc.freenode.net/scrapy
.. _issue tracker: https://github.com/scrapy/scrapy/issues


第一步
===========

.. toctree::
   :hidden:

   intro/overview
   intro/install
   intro/tutorial
   intro/examples

:doc:`intro/overview`
    知道Scarpy能做什么。

:doc:`intro/install`
	安装它。

:doc:`intro/tutorial`
	写下第一个Scrapy 项目

:doc:`intro/examples`
	学习定制一个Scrapy项目。

.. _section-basics:

基本概念
==============

.. toctree::
   :hidden:

   topics/commands
   topics/items
   topics/spiders
   topics/link-extractors
   topics/selectors
   topics/loaders
   topics/shell
   topics/item-pipeline
   topics/feed-exports
   topics/link-extractors

:doc:`topics/commands`
		知道如何用命令行来管理项目

:doc:`topics/items`
		定义要爬取的Item

:doc:`topics/spiders`
    Write the rules to crawl your websites.

:doc:`topics/selectors`
    Extract the data from web pages using XPath.

:doc:`topics/shell`
    Test your extraction code in an interactive environment.

:doc:`topics/loaders`
    Populate your items with the extracted data.

:doc:`topics/item-pipeline`
    Post-process and store your scraped data.

:doc:`topics/feed-exports`
    Output your scraped data using different formats and storages.

:doc:`topics/link-extractors`
    Convenient classes to extract links to follow from pages.

Built-in services
=================

.. toctree::
   :hidden:

   topics/logging
   topics/stats
   topics/email
   topics/telnetconsole
   topics/webservice

:doc:`topics/logging`
    Understand the simple logging facility provided by Scrapy.
   
:doc:`topics/stats`
    Collect statistics about your scraping crawler.

:doc:`topics/email`
    Send email notifications when certain events occur.

:doc:`topics/telnetconsole`
    Inspect a running crawler using a built-in Python console.

:doc:`topics/webservice`
    Monitor and control a crawler using a web service.


Solving specific problems
=========================

.. toctree::
   :hidden:

   faq
   topics/debug
   topics/contracts
   topics/firefox
   topics/firebug
   topics/leaks
   topics/images
   topics/ubuntu
   topics/scrapyd
   topics/autothrottle
   topics/jobs
   topics/djangoitem

:doc:`faq`
    Get answers to most frequently asked questions.

:doc:`topics/debug`
    Learn how to debug common problems of your scrapy spider.

:doc:`topics/contracts`
	Learn how to use contracts for testing your spiders.

:doc:`topics/firefox`
    Learn how to scrape with Firefox and some useful add-ons.

:doc:`topics/firebug`
    Learn how to scrape efficiently using Firebug.

:doc:`topics/leaks`
    Learn how to find and get rid of memory leaks in your crawler.

:doc:`topics/images`
    Download static images associated with your scraped items.

:doc:`topics/ubuntu`
    Install latest Scrapy packages easily on Ubuntu

:doc:`topics/scrapyd`
    Deploying your Scrapy project in production.

:doc:`topics/autothrottle`
    Adjust crawl rate dynamically based on load.

:doc:`topics/jobs`
    Learn how to pause and resume crawls for large spiders.

:doc:`topics/djangoitem`
    Write scraped items using Django models.

.. _extending-scrapy:

Extending Scrapy
================

.. toctree::
   :hidden:

   topics/architecture
   topics/downloader-middleware
   topics/spider-middleware
   topics/extensions
   topics/api

:doc:`topics/architecture`
    Understand the Scrapy architecture.

:doc:`topics/downloader-middleware`
    Customize how pages get requested and downloaded.

:doc:`topics/spider-middleware`
    Customize the input and output of your spiders.

:doc:`topics/extensions`
    Extend Scrapy with your custom functionality

:doc:`topics/api`
    Use it on extensions and middlewares to extend Scrapy functionality

Reference
=========

.. toctree::
   :hidden:

   topics/request-response
   topics/settings
   topics/signals
   topics/exceptions
   topics/exporters

:doc:`topics/commands`
    Learn about the command-line tool and see all :ref:`available commands <topics-commands-ref>`.

:doc:`topics/request-response`
    Understand the classes used to represent HTTP requests and responses.

:doc:`topics/settings`
    Learn how to configure Scrapy and see all :ref:`available settings <topics-settings-ref>`.

:doc:`topics/signals`
    See all available signals and how to work with them.

:doc:`topics/exceptions`
    See all available exceptions and their meaning.

:doc:`topics/exporters`
    Quickly export your scraped items to a file (XML, CSV, etc).


All the rest
============

.. toctree::
   :hidden:

   news
   contributing
   versioning
   experimental/index

:doc:`news`
    See what has changed in recent Scrapy versions.

:doc:`contributing`
    Learn how to contribute to the Scrapy project.

:doc:`versioning`
    Understand Scrapy versioning and API stability.

:doc:`experimental/index`
    Learn about bleeding-edge features.
