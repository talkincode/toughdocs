.. ToughRADIUS documentation master file, created by
   sphinx-quickstart on Thu Jan  8 16:28:26 2015.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


ToughRADIUS手册
=======================================

简介
--------

ToughRADIUS 是一个开源的Radius服务软件，自1.1版本起采用于AGPL许可协议发布，1.0.x版本仍然使用BSD协议。

ToughRADIUS 支持标准RADIUS协议，提供完整的AAA实现。支持灵活的策略管理，支持各种主流接入设备并轻松扩展，具备丰富的计费策略支持。

ToughRADIUS 支持使用Oracle, MySQL, PostgreSQL, MSSQL等主流数据库存储用户数据，并支持数据缓存，极大的提高了性能。

ToughRADIUS 支持Windows，Linux，BSD跨平台部署，部署使用简单。

ToughRADIUS 提供了RADIUS核心服务引擎与Web管理控制台,用户自助服务三个子系统，核心服务引擎提供高性能的认证计费服务，Web管理控制台提供了界面友好，功能完善的管理功能。用户自助服务系统提供了一个面向终端用户的网上服务渠道。

ToughRADIUS 网站：http://www.toughradius.net


.. _install-docs:

安装手册
--------

.. toctree::
    :maxdepth: 3

    docker_install
    linux_install


.. _bas-docs:

设备对接手册
------------

.. toctree::
    :maxdepth: 3

    case/routeros
    case/routeros_pppoe
    case/routeros_attrs


