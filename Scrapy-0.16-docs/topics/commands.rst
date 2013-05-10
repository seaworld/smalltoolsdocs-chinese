.. _topics-commands:

=================
Command line tool
=================

.. versionadded:: 0.10

Scrapy 通过 ``scrapy``的命令行工具来控制，这里简称“Scrapy tool”来区别其他的子命令行，我们叫"命令"或“Scrapy 命令”。


Scrapy tool提供几个工具，多种支持，每个接受不同的参数。

.. _topics项目结构：

默认的Scrapy结构
====================================

在深入命令行工具之前，我们先了解Scrapy项目的目录结构。

尽管它可以被修改，所有的项目都有默认相同文件结构，相似的::

   scrapy.cfg
   myproject/
       __init__.py
       items.py
       pipelines.py
       settings.py
       spiders/
           __init__.py
           spider1.py
           spider2.py
           ...

这个目录 ``scrapy.cfg`` 文件相当与*项目的root目录*，文件包含python模块定义项目设置。这里一个例子::

    [settings]
    default = myproject.settings

使用 ``scrapy`` 工具
=========================

你可以使用Scrapy工具，不带任何参数，它答应很多的帮助信息和可用的命令::

    Scrapy X.Y - no active project

    Usage:
      scrapy <command> [options] [args]

    Available commands:
      crawl         Start crawling a spider or URL
      fetch         Fetch a URL using the Scrapy downloader
    [...]

第一行会输出当前有效的项目，如果你在项目里。这里是运行在项目外。如果运行在一个项目里，它会输出更多，像::

    Scrapy X.Y - project: myproject

    Usage:
      scrapy <command> [options] [args]

    [...]

创建项目
-----------------

第一件事就是输入 ``scrapy`` tool来创建你的Scrapy项目::

    scrapy startproject myproject

这会创建一个 Scrapy 项目在 ``myproject`` 目录里。

接下里，进入这个项目目录::

    cd myproject

这时，你可一用你 ``scrapy`` 命令来管你的项目。

控制项目
--------------------

你在项目里使用 ``scrapy`` 工具来控制和管理他们。

举个例子，创建一个爬虫::

    scrapy genspider mydomain mydomain.com

很多Scrapy的命令(like :command:`crawl`) 都必须在项目里。看 :ref:`commands reference <topics-commands-ref>` 下面有跟多信息关于命令行运行在项目里。

同样你注意这些命令有一些不同哦功能，当运行在项目源里。每个命令会使用爬虫的行为(像 ``user_agent`` 属性来覆盖 user-agent)
如果你获取相关的特定spider。这是正常的，作为 ``fetch`` 命令意味着使用选择如何爬取和下载页面。

.. _topics-commands-ref:

有效的命令
=======================

本节包含一些有效的内置命令来演示和有效例子。记住你可以通过命令来获得更多帮助::

    scrapy <command> -h

你可以看到有效命令::

    scrapy -h

有两种命令，这些都在Scrapy项目里(制定项目命令) 这些通过些许不同，在一个项目里 (使用项目设置).

Global commands:

* :command:`startproject`
* :command:`settings`
* :command:`runspider`
* :command:`shell`
* :command:`fetch`
* :command:`view`
* :command:`version`

Project-only commands:

* :command:`crawl`
* :command:`check`
* :command:`list`
* :command:`edit`
* :command:`parse`
* :command:`genspider`
* :command:`server`
* :command:`deploy`

.. command:: startproject

startproject
------------

* Syntax: ``scrapy startproject <project_name>``
* Requires project: *no*

创建一个Scrapy项目 ``project_name``, under the ``project_name``目录.

Usage example::

    $ scrapy startproject myproject

.. command:: genspider

genspider
---------

* Syntax: ``scrapy genspider [-t template] <name> <domain>``
* Requires project: *yes*

在当前项目里创建一个Spider.

这时一个方便的快捷命令来创建spiders基于事先定义好的模板，但是明显的不只有一条路来创建爬虫。你可以手动创建爬虫源码文件来代替命令。

Usage example::

    $ scrapy genspider -l
    Available templates:
      basic
      crawl
      csvfeed
      xmlfeed

    $ scrapy genspider -d basic
    from scrapy.spider import BaseSpider

    class $classname(BaseSpider):
        name = "$name"
        allowed_domains = ["$domain"]
        start_urls = (
            'http://www.$domain/',
            )

        def parse(self, response):
            pass

    $ scrapy genspider -t basic example example.com
    创建一个爬虫 'example' 使用模板 'basic' :
      mybot.spiders.example

.. command:: crawl

crawl
-----

* Syntax: ``scrapy crawl <spider>``
* Requires project: *yes*

开始一个爬虫：

Usage examples::

    $ scrapy crawl myspider
    [ ... myspider starts crawling ... ]


.. command:: check

check
-----

* Syntax: ``scrapy check [-l] <spider>``
* Requires project: *yes*

进行检查。

Usage examples::

    $ scrapy check -l
    first_spider
      * parse
      * parse_item
    second_spider
      * parse
      * parse_item

    $ scrapy check
    [FAILED] first_spider:parse_item
    >>> 'RetailPricex' field is missing

    [FAILED] first_spider:parse
    >>> Returned 92 requests, expected 0..4

.. command:: server

server
------

* Syntax: ``scrapy server``
* Requires project: *yes*

开始Scrapyd 服务， 这里建成JSON API。更多信息: :ref:`topics-scrapyd`.

Usage example::

    $ scrapy server
    [ ... scrapyd starts and stays idle waiting for spiders to get scheduled ... ]

使用定时爬虫，使用Scrapy JSON API。

.. command:: list

list
----

* Syntax: ``scrapy list``
* Requires project: *yes*

列出当前项目的有效爬虫。输出每个爬虫哦功能。

Usage example::

    $ scrapy list
    spider1
    spider2

.. command:: edit

edit
----

* Syntax: ``scrapy edit <spider>``
* Requires project: *yes*

编辑爬虫使用editor，定义在:setting:`EDITOR` setting.

这个命令提供方便的缩写，开发者容易的选择任何工具或IDE来输入和调试你的spiders。

Usage example::

    $ scrapy edit spider1

.. command:: fetch

fetch
-----

* Syntax: ``scrapy fetch <url>``
* Requires project: *no*

下载指定URL，使用Scrapy的下载器，写入标准输出，不需要创建项目。

有意思的是这个命令让spider自动下载。例如，如果spider有一个 ``USER_AGENT`` 的属性来替换原来的User Agent ，它会用这个新的。

这个命令可以用来“看”你的spider抓下的确实页面。

如果你在项目外使用，没有确定的spider行为被指定，它会用默认的scrapy下载设置。

例子::

    $ scrapy fetch --nolog http://www.example.com/some/page.html
    [ ... html content here ... ]

    $ scrapy fetch --nolog --headers http://www.example.com/
    {'Accept-Ranges': ['bytes'],
     'Age': ['1263   '],
     'Connection': ['close     '],
     'Content-Length': ['596'],
     'Content-Type': ['text/html; charset=UTF-8'],
     'Date': ['Wed, 18 Aug 2010 23:59:46 GMT'],
     'Etag': ['"573c1-254-48c9c87349680"'],
     'Last-Modified': ['Fri, 30 Jul 2010 15:30:18 GMT'],
     'Server': ['Apache/2.2.3 (CentOS)']}

.. command:: view

view
----

* Syntax: ``scrapy view <url>``
* Requires project: *no*

在浏览器中打开指定的URL，你的scrapy会“看”到它。很多时候，spiders和用户看到的页面不一样，所以这个可以看到spider看的是否是你预期的。

Usage example::

    $ scrapy view http://www.example.com/some/page.html
    [ ... browser starts ... ]

.. command:: shell

shell
-----

* Syntax: ``scrapy shell [url]``
* Requires project: *no*

开始Scrapy shell 为URL调试。See :ref:`topics-shell` for more info.

例子::

    $ scrapy shell http://www.example.com/some/page.html
    [ ... scrapy shell starts ... ]

.. command:: parse

parse
-----

* Syntax: ``scrapy parse <url> [options]``
* Requires project: *yes*

抓取指定URL，解析它，使用``--callback``或者``parse`` 选项处理。

Supported options:

* ``--callback`` or ``-c``: spider 方法使用回调来解析响应

* ``--rules`` or ``-r``: 使用 :class:`~scrapy.contrib.spiders.CrawlSpider` 规则来代替回调来解析响应

* ``--noitems``: 不展示提取链接

* ``--nolinks``: 不展示scraped对象

* ``--depth`` or ``-d``: 请求递归的深度 (default: 1)

* ``--verbose`` or ``-v``: 展示深度的信息。

例子::

    $ scrapy parse http://www.example.com/ -c parse_item
    [ ... scrapy log lines crawling example.com spider ... ]

    >>> STATUS DEPTH LEVEL 1 <<<
    # Scraped Items  ------------------------------------------------------------
    [{'name': u'Example item',
     'category': u'Furniture',
     'length': u'12 cm'}]

    # Requests  -----------------------------------------------------------------
    []


.. command:: settings

settings
--------

* Syntax: ``scrapy settings [options]``
* Requires project: *no*

获取Scrapy的设置

如果在项目里，会展示项目的设置值，否则，它会展示默认的Scrapy值

距离::

    $ scrapy settings --get BOT_NAME
    scrapybot
    $ scrapy settings --get DOWNLOAD_DELAY
    0

.. command:: runspider

runspider
---------

* Syntax: ``scrapy runspider <spider_file.py>``
* Requires project: *no*

运营一个python文件爬虫，不需要创建项目

例子::

    $ scrapy runspider myspider.py
    [ ... spider starts crawling ... ]

.. command:: version

version
-------

* Syntax: ``scrapy version [-v]``
* Requires project: *no*

打印scrapy的版本，还会打印python，twisted的版本信息，在碰到bug的时候很有用。

.. command:: deploy

deploy
------

.. versionadded:: 0.11

* Syntax: ``scrapy deploy [ <target:project> | -l <target> | -L ]``
* Requires project: *yes*

部署项目到Scrapy服务器 See :ref:`topics-deploying`.

自定义项目命令
=======================

你可以添加自定义的命令，使用:setting:`COMMANDS_MODULE` setting. 看`scrapy/commands`_ 有更多例子告诉你如歌实现你的命令。

.. _scrapy/commands: https://github.com/scrapy/scrapy/blob/master/scrapy/commands
.. setting:: COMMANDS_MODULE

COMMANDS_MODULE
---------------

Default: ``''`` (empty string)

这个模块用来定制scrapy命令，用来添加自定义的命令

Example::

    COMMANDS_MODULE = 'mybot.commands'
