.. _intro-install:

==================
Installation guide
==================

安装前的要求
==============

安装前假定你下面都已经安装好了：

* Python 2.6 or 2.7
* OpenSSL. This comes preinstalled in all operating systems except Windows (see :ref:`intro-install-platform-notes`)
* `pip`_ or `easy_install`_ Python package managers

安装Scrapy
=================

你可以使用easy_install or pip来安装

使用pip::

   pip install Scrapy

使用easy_install::

   easy_install Scrapy

.. _intro-install-platform-notes:

特殊平台安装笔记
====================================

Windows
-------

安好python后，跟着下面的步骤来安装：

* 添加C:\python27\Scripts 到系统的环境变量。

* 按下面的步骤安装openssh：

* 不想看，直接用windows的 easy_isntall ，方便。

Ubuntu 9.10 or 以前
~~~~~~~~~~~~~~~~~~~~

不要用ubuntu提供的 python-scrapy 包，它的版本太旧。

相对的，使用正式版，解决了所有依赖，不断的更新和修复bug。
