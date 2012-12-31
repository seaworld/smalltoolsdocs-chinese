.. _topics-commands:

=================
命令行工具
=================

.. versionadded:: 0.10

Scrapy 是通过 ``scrapy``命令行控制的，这里讲一下“Scrapy tool”和子命令行的区别。

Scrapy工具提供几个命令来实现多个功能，每个都使用一组不同的参数和选项。

.. _topics-project-structure:

Scrapy 项目的默认结构
====================================

在学习它的命令行工具和子命令之前，我们想了解一下它的目录结构。

尽管这些可以被修改，但所有的Scrapy项目都有同样的默认结构：

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

目录里的 ``scrapy.cfg`` 文件代表着项目目录，文件包含python模块定义的项目设置，下面是例子::

    [settings]
    default = myproject.settings

使用 ``scrapy`` 工具
=========================

你可以开始运行Scrapy工具布带任何参数，这是会输出很多有用的帮助信息：

    Scrapy X.Y - no active project

    Usage:
      scrapy <command> [options] [args]

    Available commands:
      crawl         Start crawling a spider or URL
      fetch         Fetch a URL using the Scrapy downloader
    [...]

第一行输出当前使用的项目，如果你在一个Scrapy项目里。如果你不在项目里，如果你运行在项目里，你会的到这样的输出：

    Scrapy X.Y - project: myproject

    Usage:
      scrapy <command> [options] [args]

    [...]

创建项目
-----------------

第一件事使用scrapy创建你的Scrapy项目：

    scrapy startproject myproject

这样会创建一个'myproject'的目录

接下来，进入这个新项目的目录：

    cd myproject

这是再使用scrapy命令来管理和控制你的项目。

控制项目
--------------------

你可以使用'scrapy'工具来控制和管理项目。

像这样，创建一个新的爬虫：

    scrapy genspider mydomain mydomain.com

Some Scrapy commands (like :command:`crawl`) must be run from inside a Scrapy
project. See the :ref:`commands reference <topics-commands-ref>` below for more
information on which commands must be run from inside projects, and which not.

Also keep in mind that some commands may have slightly different behaviours
when running them from inside projects. For example, the fetch command will use
spider-overridden behaviours (such as the ``user_agent`` attribute to override
the user-agent) if the url being fetched is associated with some specific
spider. This is intentional, as the ``fetch`` command is meant to be used to
check how spiders are downloading pages.

.. _topics-commands-ref:

Available tool commands
=======================

This section contains a list of the available built-in commands with a
description and some usage examples. Remember you can always get more info
about each command by running::

    scrapy <command> -h

And you can see all available commands with::

    scrapy -h

There are two kinds of commands, those that only work from inside a Scrapy
project (Project-specific commands) and those that also work without an active
Scrapy project (Global commands), though they may behave slightly different
when running from inside a project (as they would use the project overriden
settings).

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

Creates a new Scrapy project named ``project_name``, under the ``project_name``
directory.

Usage example::

    $ scrapy startproject myproject

.. command:: genspider

genspider
---------

* Syntax: ``scrapy genspider [-t template] <name> <domain>``
* Requires project: *yes*

Create a new spider in the current project.

This is just a convenient shortcut command for creating spiders based on
pre-defined templates, but certainly not the only way to create spiders. You
can just create the spider source code files yourself, instead of using this
command.

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
    Created spider 'example' using template 'basic' in module:
      mybot.spiders.example

.. command:: crawl

crawl
-----

* Syntax: ``scrapy crawl <spider>``
* Requires project: *yes*

Start crawling a spider. 

Usage examples::

    $ scrapy crawl myspider
    [ ... myspider starts crawling ... ]


.. command:: check

check
-----

* Syntax: ``scrapy check [-l] <spider>``
* Requires project: *yes*

Run contract checks.

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

Start Scrapyd server for this project, which can be referred from the JSON API
with the project name ``default``. For more info see: :ref:`topics-scrapyd`.

Usage example::

    $ scrapy server
    [ ... scrapyd starts and stays idle waiting for spiders to get scheduled ... ]

To schedule spiders, use the Scrapyd JSON API.

.. command:: list

list
----

* Syntax: ``scrapy list``
* Requires project: *yes*

List all available spiders in the current project. The output is one spider per
line.

Usage example::

    $ scrapy list
    spider1
    spider2

.. command:: edit

edit
----

* Syntax: ``scrapy edit <spider>``
* Requires project: *yes*

Edit the given spider using the editor defined in the :setting:`EDITOR`
setting.

This command is provided only as a convenient shortcut for the most common
case, the developer is of course free to choose any tool or IDE to write and
debug his spiders.

Usage example::

    $ scrapy edit spider1

.. command:: fetch

fetch
-----

* Syntax: ``scrapy fetch <url>``
* Requires project: *no*

Downloads the given URL using the Scrapy downloader and writes the contents to
standard output.

The interesting thing about this command is that it fetches the page how the
the spider would download it. For example, if the spider has an ``USER_AGENT``
attribute which overrides the User Agent, it will use that one.

So this command can be used to "see" how your spider would fetch certain page.

If used outside a project, no particular per-spider behaviour would be applied
and it will just use the default Scrapy downloder settings.

Usage examples::

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

Opens the given URL in a browser, as your Scrapy spider would "see" it.
Sometimes spiders see pages differently from regular users, so this can be used
to check what the spider "sees" and confirm it's what you expect.

Usage example::

    $ scrapy view http://www.example.com/some/page.html
    [ ... browser starts ... ]

.. command:: shell

shell
-----

* Syntax: ``scrapy shell [url]``
* Requires project: *no*

Starts the Scrapy shell for the given URL (if given) or empty if not URL is
given. See :ref:`topics-shell` for more info.

Usage example::

    $ scrapy shell http://www.example.com/some/page.html
    [ ... scrapy shell starts ... ]

.. command:: parse

parse
-----

* Syntax: ``scrapy parse <url> [options]``
* Requires project: *yes*

Fetches the given URL and parses with the spider that handles it, using the
method passed with the ``--callback`` option, or ``parse`` if not given.

Supported options:

* ``--callback`` or ``-c``: spider method to use as callback for parsing the
  response

* ``--rules`` or ``-r``: use :class:`~scrapy.contrib.spiders.CrawlSpider`
  rules to discover the callback (ie. spider method) to use for parsing the
  response

* ``--noitems``: don't show extracted links

* ``--nolinks``: don't show scraped items

* ``--depth`` or ``-d``: depth level for which the requests should be followed
  recursively (default: 1)

* ``--verbose`` or ``-v``: display information for each depth level

Usage example::

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

Get the value of a Scrapy setting.

If used inside a project it'll show the project setting value, otherwise it'll
show the default Scrapy value for that setting.

Example usage::

    $ scrapy settings --get BOT_NAME
    scrapybot
    $ scrapy settings --get DOWNLOAD_DELAY
    0

.. command:: runspider

runspider
---------

* Syntax: ``scrapy runspider <spider_file.py>``
* Requires project: *no*

Run a spider self-contained in a Python file, without having to create a
project.

Example usage::

    $ scrapy runspider myspider.py
    [ ... spider starts crawling ... ]

.. command:: version

version
-------

* Syntax: ``scrapy version [-v]``
* Requires project: *no*

Prints the Scrapy version. If used with ``-v`` it also prints Python, Twisted
and Platform info, which is useful for bug reports.

.. command:: deploy

deploy
------

.. versionadded:: 0.11

* Syntax: ``scrapy deploy [ <target:project> | -l <target> | -L ]``
* Requires project: *yes*

Deploy the project into a Scrapyd server. See :ref:`topics-deploying`.

Custom project commands
=======================

You can also add your custom project commands by using the
:setting:`COMMANDS_MODULE` setting. See the Scrapy commands in
`scrapy/commands`_ for examples on how to implement your commands.

.. _scrapy/commands: https://github.com/scrapy/scrapy/blob/master/scrapy/commands
.. setting:: COMMANDS_MODULE

COMMANDS_MODULE
---------------

Default: ``''`` (empty string)

A module to use for looking custom Scrapy commands. This is used to add custom
commands for your Scrapy project.

Example::

    COMMANDS_MODULE = 'mybot.commands'
