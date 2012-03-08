=================
���� Horizon
=================

��ָ������ڲ�Ʒ�����в���Horizonʱ������һЩ��������,��ע������.

�Ự�洢
===============

Horizonʹ�� `Django�ĻỰ���`_ �����û��Ự����;���ǻ��и��ֻỰ��˿���ʹ��,
��ͨ��``local_settings.py``�ļ��е�``SESSION_ENGINE``���ÿ���. What follows is a quick discussion of the
pros and cons of each of the common options as they pertain to deploying
Horizon specifically.

.. _Django�ĻỰ���: https://docs.djangoproject.com/en/dev/topics/http/sessions/

�����ڴ���ٻ���
------------------

����::

    SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
    CACHES = {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache'
    }

�����ڴ�洢������,���������õĻỰ���,���κ��ⲿ����,�������������ص�ȱ��:

  * �����ڽ��̼乲��洢.
  * ���ݷǳ־û�,������ֹ�����ݶ�ʧ.

��Ϊû��������,HorizonĬ�����ñ����ڴ���,���ǲ��Ƽ��ڲ�Ʒ������ʹ��,�����Ǹ�ǿ�ȵĿ�������.
���õ�ѡ����ʹ��Memcached, �������������һ��ȱ��.

Memcached
---------

����::

    SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
    CACHES = {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache'
        'LOCATION': 'my_memcached_host:11211',
    }

Ӧ�ó���ʹ�õ��ⲿ����,����Memcached,�ṩ�˳־��Ժ͹���洢,��С��ģ�Ĳ���Ϳ����зǳ�����.
���Ƕ��ڷֲ�ʽ�͸߿��ó���,memcached��һЩ���е�����,��Щ���ⳬ���˱��ĵ����۷�Χ.

Memcached��һ������͸�Ч�Ļ�����,�����㲿�����Ҫ,���Բ����������г���.

Ҫ��:

  * Memcached �������в��ɷ���.
  * ��װPython memcachedģ��.

���ݿ�
--------

����::

    SESSION_ENGINE = 'django.core.cache.backends.db.DatabaseCache'
    DATABASES = {
        'default': {
            # Databe configuration here
        }
    }

���ݿ�Ự����ǿ�������(���ú��ʵ����ݿ����),�־õ�,������ʹ֮�߲���,�Լ��߿���.

�˷�ʽ��һ���������������ݿ�Ự�����һ���Ƚ����Ĵ洢��ʽ,ʹ�ù�������ºܸߵĸ���.
Proper configuration of your database deployment can also be a substantial
undertaking and is far beyond the scope of this documentation.

�������ݿ�
---------------

Ϊ�˼������ݿ��ѯ����������,���Կ���ʹ��Django��``cached_db``�Ự���,��ʹ�������ݿ�ͻ���ܹ�
��ִ������д�뻺��͸�Ч�ļ���,���������������ʾ�����ݿ�ͻ��������,������SESSION_ENGINE���û�Ϸ�ʽ::

    SESSION_ENGINE = "django.contrib.sessions.backends.cached_db"

Cookies
-------

���ʹ��Django 1.4�Լ����߰汾, ��һ���µĻỰ��˿���,�������˷��������غ���������: ``signed_cookies``���!

�˺�˴洢�Ự�������û����������Cookie��. �˺��ʹ����ǩ��������֤�Ự�����ڴ���Ĺ����в����۸�
(**�ⲻͬ�ڼ���,�Ự������Ȼ�ǿɶ���**)

The pros of this session engine are that it doesn't require any additional
dependencies or infrastructure overhead, and it scales indefinitely as long
as the quantity of session data being stored fits into a normal cookie.

The biggest downside is that it places session data into storage on the user's
machine and transports it over the wire. It also limits the quantity of
session data which can be stored.

һ������ȱ����,��Щ�Ự�����Ǵ�����û��Ļ�����,��ͨ�����紫��.�������˿��Դ���ĻỰ������.

���ڴ˻Ự��˵İ�ȫ������꾡����,���Ķ� `Django documentation on cookie-based sessions`_.

.. _Django documentation on cookie-based sessions: https://docs.djangoproject.com/en/dev/topics/http/sessions/#using-cookie-based-sessions
