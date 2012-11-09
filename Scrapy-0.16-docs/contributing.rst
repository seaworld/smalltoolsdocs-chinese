.. _topics-contributing:

======================
参与 Scrapy
======================

这里有很多方法来参与Scrapy，这里有几个：

* 写Scrapy相关的blog，告诉世界你怎么用Scrapy，这样可以帮助新人得到更多的例子，Scrapy项目也会增加。

* 提交bugs和功能需求 到`issue tracker`_, 试着跟随`Reporting bugs`_下面的详细的向导。

* 为新的功能或者bug修复提交新的分支。看一下`Writing patches`_ and `Submitting patches`_下面的介绍详细来知道怎么写和提交分支。

* 加入`scrapy-developers`_ 邮箱列表，分享你的主意怎么提高Scrapy。我们总想听听意见。

反馈bugs
==============

一个好的bug报告是非常有用的，报告新的bug的时候想想下面的指南：

* 打开`FAQ <faq>`，先看看你的问题是否在已知问题中。

* 打开`open issues`_看是否已经提交过，如果是，不要删除报告，检查这条的历史记录和评论，你可能会贡献出有一些有用的信息。

* search the `scrapy-users`_ list to see if it has been discussed there, or
  if you're not sure if what you're seeing is a bug. You can also ask in the
  `#scrapy` IRC channel.

* write complete, reproducible, specific bug reports. The smaller the test
  case, the better. Remember that other developers won't have your project to
  reproduce the bug, so please include all relevant files required to reproduce
  it.

* include the output of ``scrapy version -v`` so developers working on your bug
  know exactly which version and platform it occurred on, which is often very
  helpful for reproducing it, or knowing if it was already fixed.

Writing patches
===============

The better written a patch is, the higher chance that it'll get accepted and
the sooner that will be merged.

Well-written patches should:

* contain the minimum amount of code required for the specific change. Small
  patches are easier to review and merge. So, if you're doing more than one
  change (or bug fix), please consider submitting one patch per change. Do not
  collapse multiple changes into a single patch. For big changes consider using
  a patch queue.

* pass all unit-tests. See `Running tests`_ below.

* include one (or more) test cases that check the bug fixed or the new
  functionality added. See `Writing tests`_ below.

* if you're adding or changing a public (documented) API, please include 
  the documentation changes in the same patch.  See `Documentation policies`_
  below.

Submitting patches
==================

The best way to submit a patch is to issue a `pull request`_ on Github,
optionally creating a new issue first.

Alternatively, we also accept the patches in the traditional way of sending
them to the `scrapy-developers`_ list.

Regardless of which mechanism you use, remember to explain what was fixed or
the new functionality (what it is, why it's needed, etc). The more info you
include, the easier will be for core developers to understand and accept your
patch.

You can also discuss the new functionality (or bug fix) in `scrapy-developers`_
first, before creating the patch, but it's always good to have a patch ready to
illustrate your arguments and show that you have put some additional thought
into the subject.

Coding style
============

Please follow these coding conventions when writing code for inclusion in
Scrapy:

* Unless otherwise specified, follow :pep:`8`.

* It's OK to use lines longer than 80 chars if it improves the code
  readability.

* Don't put your name in the code you contribute. Our policy is to keep
  the contributor's name in the `AUTHORS`_ file distributed with Scrapy.

Documentation policies
======================

* **Don't** use docstrings for documenting classes, or methods which are
  already documented in the official (sphinx) documentation. For example, the
  :meth:`ItemLoader.add_value` method should be documented in the sphinx
  documentation, not its docstring.

* **Do** use docstrings for documenting functions not present in the official
  (sphinx) documentation, such as functions from ``scrapy.utils`` package and
  its sub-modules.

Tests
=====

Tests are implemented using the `Twisted unit-testing framework`_ called
``trial``.

Running tests
-------------

To run all tests go to the root directory of Scrapy source code and run:

    ``bin/runtests.sh`` (on unix)

    ``bin\runtests.bat`` (on windows)

To run a specific test (say ``scrapy.tests.test_contrib_loader``) use:

    ``bin/runtests.sh scrapy.tests.test_contrib_loader`` (on unix)

    ``bin\runtests.bat scrapy.tests.test_contrib_loader`` (on windows)

Writing tests
-------------

All functionality (including new features and bug fixes) must include a test
case to check that it works as expected, so please include tests for your
patches if you want them to get accepted sooner.

Scrapy uses unit-tests, which are located in the ``scrapy.tests`` package
(`scrapy/tests`_ directory). Their module name typically resembles the full
path of the module they're testing. For example, the item loaders code is in::

    scrapy.contrib.loader

And their unit-tests are in::

    scrapy.tests.test_contrib_loader

.. _issue tracker: https://github.com/scrapy/scrapy/issues
.. _scrapy-users: http://groups.google.com/group/scrapy-users
.. _scrapy-developers: http://groups.google.com/group/scrapy-developers
.. _Twisted unit-testing framework: http://twistedmatrix.com/documents/current/core/development/policy/test-standard.html
.. _AUTHORS: https://github.com/scrapy/scrapy/blob/master/AUTHORS
.. _scrapy/tests: https://github.com/scrapy/scrapy/tree/master/scrapy/tests
.. _open issues: https://github.com/scrapy/scrapy/issues
.. _pull request: http://help.github.com/send-pull-requests/
