==============================
改变Horizon的商标
==============================

修改页面标题
=======================

OpenStack Dashboard页面标题(例如. "**OpenStack** Dashboard")可以通过添加``SITE_BRANDING``属性到``local_settings.py``进行覆盖,并设置你期望的名字.

文件``local_settings.py``位于如下Horizon目录路径
``horizon/openstack-dashboard/local/local_settings.py``.

改变页面图标
=======================

OpenStack图片是在``style.css``样式表中设置的::

    #splash .modal {
        background: #fff url(../images/logo.png) no-repeat center 35px;

    h1.brand a {
        background: url(../images/logo.png) top left no-repeat;

要覆盖OpenStack图标图像,替换卫浴如下目录路径的图像
``horizon/openstack-dashboard/dashboard/static/dashboard/images/logo.png``.

尺寸应为 ``宽度: 108px, 高度: 121px``.
