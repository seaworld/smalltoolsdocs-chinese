.. _topics-items:

=====
Items
=====

.. module:: scrapy.item
   :synopsis: Item and Field classes

主要提取结构数据，Scrapy提供了 :class:`Item` 来支持。

:class:`Item` 对象是一个简单的包含收集的提取数据，他们提供的 `dictionary-like`_ API 有一同的语法来声明可用域。

.. _dictionary-like: http://docs.python.org/library/stdtypes.html#dict

.. _topics-items-declaring:

声明 Items
===============

Items 使用一个简单类定义与饭来声明 :class:`Field` ，这里是一个例子::

    from scrapy.item import Item, Field

    class Product(Item):
        name = Field()
        price = Field()
        stock = Field()
        last_updated = Field(serializer=str)

.. note:: 这个和 `Django`_  很想， Scrapy Items 和 `Django Models`_ 很想, 除了 Scrapy Items 很像，他们不同字段之间没有概念。

.. _Django: http://www.djangoproject.com/
.. _Django Models: http://docs.djangoproject.com/en/dev/topics/db/models/

.. _topics-items-fields:

Item Fields
===========

:class:`Field` 对象使用特定的 metadata 为每个字段，举个例子，序列化的方法 ``last_updated`` 字段field表示图片。

你可以制定不同种类的metadata，这里没有任何限制在`Field`的值上。
同样，不是指所有的metadata关键字。每个key定义在 :class:`Field` 对象用于不同的组建，只有这些组建知道。
你可以定义其他的 :class:`Field` key 在你的项目里，对应你自己的需求。只要的功能 :class:`Field` 对象是提供一个对象来定义所有的字段metadata在同一个地方。
通常情况下，这些组建的行为取决与每个字段使用的明确keys来配置的行为。你需要参考这些文档来明白metadata keys对每个组建的使用。

It's important to note that the :class:`Field` objects used to declare the item
do not stay assigned as class attributes. Instead, they can be accesed through
the :attr:`Item.fields` attribute. 

And that's all you need to know about declaring items. 

Working with Items
==================

Here are some examples of common tasks performed with items, using the
``Product`` item :ref:`declared above  <topics-items-declaring>`. You will
notice the API is very similar to the `dict API`_.

Creating items
--------------

::

    >>> product = Product(name='Desktop PC', price=1000)
    >>> print product
    Product(name='Desktop PC', price=1000)

Getting field values
--------------------

::

    >>> product['name']
    Desktop PC
    >>> product.get('name')
    Desktop PC

    >>> product['price']
    1000

    >>> product['last_updated']
    Traceback (most recent call last):
        ...
    KeyError: 'last_updated'

    >>> product.get('last_updated', 'not set')
    not set

    >>> product['lala'] # getting unknown field
    Traceback (most recent call last):
        ...
    KeyError: 'lala'

    >>> product.get('lala', 'unknown field')
    'unknown field'

    >>> 'name' in product  # is name field populated?
    True

    >>> 'last_updated' in product  # is last_updated populated?
    False

    >>> 'last_updated' in product.fields  # is last_updated a declared field?
    True

    >>> 'lala' in product.fields  # is lala a declared field?
    False

Setting field values
--------------------

::

    >>> product['last_updated'] = 'today'
    >>> product['last_updated']
    today

    >>> product['lala'] = 'test' # setting unknown field
    Traceback (most recent call last):
        ...
    KeyError: 'Product does not support field: lala'

Accesing all populated values
-----------------------------

To access all populated values, just use the typical `dict API`_::

    >>> product.keys()
    ['price', 'name']

    >>> product.items()
    [('price', 1000), ('name', 'Desktop PC')]

Other common tasks
------------------

Copying items::

    >>> product2 = Product(product)
    >>> print product2
    Product(name='Desktop PC', price=1000)

Creating dicts from items::

    >>> dict(product) # create a dict from all populated values
    {'price': 1000, 'name': 'Desktop PC'}

Creating items from dicts::

    >>> Product({'name': 'Laptop PC', 'price': 1500})
    Product(price=1500, name='Laptop PC')

    >>> Product({'name': 'Laptop PC', 'lala': 1500}) # warning: unknown field in dict
    Traceback (most recent call last):
        ...
    KeyError: 'Product does not support field: lala'

Extending Items
===============

You can extend Items (to add more fields or to change some metadata for some
fields) by declaring a subclass of your original Item.

For example::

    class DiscountedProduct(Product):
        discount_percent = Field(serializer=str)
        discount_expiration_date = Field()

You can also extend field metadata by using the previous field metadata and
appending more values, or changing existing values, like this::

    class SpecificProduct(Product):
        name = Field(Product.fields['name'], serializer=my_serializer)

That adds (or replaces) the ``serializer`` metadata key for the ``name`` field,
keeping all the previously existing metadata values.

Item objects
============

.. class:: Item([arg])

    Return a new Item optionally initialized from the given argument. 
    
    Items replicate the standard `dict API`_, including its constructor. The
    only additional attribute provided by Items is:
    
    .. attribute:: fields

        A dictionary containing *all declared fields* for this Item, not only
        those populated. The keys are the field names and the values are the
        :class:`Field` objects used in the :ref:`Item declaration
        <topics-items-declaring>`.

.. _dict API: http://docs.python.org/library/stdtypes.html#dict

Field objects
=============

.. class:: Field([arg])

    The :class:`Field` class is just an alias to the built-in `dict`_ class and
    doesn't provide any extra functionality or attributes. In other words,
    :class:`Field` objects are plain-old Python dicts. A separate class is used
    to support the :ref:`item declaration syntax <topics-items-declaring>`
    based on class attributes.

.. _dict: http://docs.python.org/library/stdtypes.html#dict


