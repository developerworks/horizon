=================
部署 Horizon
=================

本指南针对在产品环境中部署Horizon时遇到的一些常见问题,关注和陷阱.

会话存储
===============

Horizon使用 `Django的会话框架`_ 处理用户会话数据;但是还有各种会话后端可以使用,
可通过``local_settings.py``文件中的``SESSION_ENGINE``设置控制. What follows is a quick discussion of the
pros and cons of each of the common options as they pertain to deploying
Horizon specifically.

.. _Django的会话框架: https://docs.djangoproject.com/en/dev/topics/http/sessions/

本地内存高速缓存
------------------

启用::

    SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
    CACHES = {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache'
    }

本地内存存储是最快捷,最容易设置的会话后端,无任何外部依赖,但是有两个严重的缺点:

  * 不能在进程间共享存储.
  * 数据非持久化,进程终止后数据丢失.

因为没有依赖性,Horizon默认启用本地内存后端,但是不推荐在产品环境中使用,甚至是高强度的开发工作.
更好的选择是使用Memcached, 它解决了上述第一个缺点.

Memcached
---------

启用::

    SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
    CACHES = {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache'
        'LOCATION': 'my_memcached_host:11211',
    }

应用程序使用的外部缓存,比如Memcached,提供了持久性和共享存储,在小规模的部署和开发中非常有用.
但是对于分布式和高可用场景,memcached有一些固有的问题,这些问题超出了本文的讨论范围.

Memcached是一个极快和高效的缓存后端,能满足部署的需要,但仍不适用于所有场景.

要求:

  * Memcached 服务运行并可访问.
  * 安装Python memcached模块.

数据库
--------

启用::

    SESSION_ENGINE = 'django.core.cache.backends.db.DatabaseCache'
    DATABASES = {
        'default': {
            # Databe configuration here
        }
    }

数据库会话后端是可伸缩的(采用合适的数据库策略),持久的,并可以使之高并发,以及高可用.

此方式的一个负面因素是数据库会话后端是一个比较慢的存储方式,使用过多会招致很高的负荷.
Proper configuration of your database deployment can also be a substantial
undertaking and is far beyond the scope of this documentation.

缓存数据库
---------------

为了减轻数据库查询的性能问题,可以考虑使用Django的``cached_db``会话后端,他使用了数据库和缓存架构
来执行连续写入缓存和高效的检索,你可以配置上述所示的数据库和缓存的配置,并设置SESSION_ENGINE启用混合方式::

    SESSION_ENGINE = "django.contrib.sessions.backends.cached_db"

Cookies
-------

如果使用Django 1.4以及更高版本, 有一个新的会话后端可用,它避免了服务器负载和伸缩问题: ``signed_cookies``后端!

此后端存储会话数据于用户的浏览器的Cookie中. 此后端使用了签名技术保证会话数据在传输的过程中不被篡改
(**这不同于加密,会话数据仍然是可读的**)

The pros of this session engine are that it doesn't require any additional
dependencies or infrastructure overhead, and it scales indefinitely as long
as the quantity of session data being stored fits into a normal cookie.

The biggest downside is that it places session data into storage on the user's
machine and transports it over the wire. It also limits the quantity of
session data which can be stored.

一个最大的缺点是,这些会话数据是存放在用户的机器中,并通过网络传输.它限制了可以储存的会话数据量.

关于此会话后端的安全方面的详尽讨论,请阅读 `Django documentation on cookie-based sessions`_.

.. _Django documentation on cookie-based sessions: https://docs.djangoproject.com/en/dev/topics/http/sessions/#using-cookie-based-sessions
