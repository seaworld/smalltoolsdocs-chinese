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

要注意的是 :class:`Field` 对象要声明分配属性。取而代之的是，它们访问 :attr:`Item.fields` 属性。 

所有的都需要知道怎么声明。 

使用Item
==================

这个一个共用任务平台，使用 ``Product`` item :ref:`declared above  <topics-items-declaring>`. 你会发现这个API和dict非常像。

创建 items
--------------

::

    >>> product = Product(name='Desktop PC', price=1000)
    >>> print product
    Product(name='Desktop PC', price=1000)

创建field值
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

访问所有的值
-----------------------------

访问所有的值，使用dict的API_::

    >>> product.keys()
    ['price', 'name']

    >>> product.items()
    [('price', 1000), ('name', 'Desktop PC')]

其它常见的任务
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

到处items
===============

你可以扩展Items（加更多的fields或者修改metadata）来声明一个item的子类。

For example::

    class DiscountedProduct(Product):
        discount_percent = Field(serializer=str)
        discount_expiration_date = Field()

你可以扩展字段metadata，通过使用前一个域metadata和添加更多的values，或者修改存在的value，像这样::

    class SpecificProduct(Product):
        name = Field(Product.fields['name'], serializer=my_serializer)

添加（或替换） ``serializer`` metadata key 对 ``name`` field,保存住所有存在的metadata values.

Item objects
============

.. class:: Item([arg])

    返回一个新Item选项通过给定的参数来初始化。 
    
    Items复制着标准的 `dict API`_, 包含它的构造方法。只额外增加的是:
    
    .. attribute:: fields

        一个dict包含 *所有声明字段* 不仅仅是这些常用。keys是一个fiels的名字，值就是 :class:`Field` 对象，
        在 :ref:`Item declaration  <topics-items-declaring>`.

.. _dict API: http://docs.python.org/library/stdtypes.html#dict

Field objects
=============

.. class:: Field([arg])

    关于 :class:`Field` 类只是内置dict的别名，不提共任何额外方法和属性。换句话说，
    :class:`Field` 对象是老的oython dicts，一个独立的类用于支持 :ref:`item 声明语法 <topics-items-declaring>`

.. _dict: http://docs.python.org/library/stdtypes.html#dict


