ToughRADIUS在linux下的安装配置
====================================

ToughRADIUS是基于Python及高性能异步网络框架Twisted开发，对linux系统完美支持，可以提供更高的性能和稳定性。

目前在Linux环境下，ToughRADIUS提供了自动化安装脚本，可以轻松的帮你完成安装过程。


安装系统依赖(centos6/7)
--------------------------------------

::

    $ yum update -y
     
    # centos 6
    $ yum install -y  mysql-devel python-devel python-setuptools MySQL-python
     
    #centos7
    $ yum install -y  mariadb-devel python-devel python-setuptools MySQL-python
     


MySQL数据库安装
--------------------------------------

CentOS 6
~~~~~~~~~~~~~~~~~~~~~~

通过yum工具在线安装MySQL数据库::

    $ yum install -y mysql-server mysql-devel
    
初始化MySQL数据库::

    $ mysql_install_db

启动MySQL数据库::

    $ mysqld_safe & 

在centos6环境中可以使用以下指令来以服务模式运行MySQL::

    $ service mysql start 

设置开机启动::

    $ chkconfig mysql on 


CentOS 7
~~~~~~~~~~~~~~~~~~~~~~

通过yum工具在线安装Mariadb数据库::

    $ yum install -y mariadb-server mariadb-devel

初始化Mariadb数据库::

    $ mysql_install_db

启动Mariadb数据库::

    $ mysqld_safe & 

在centos7环境中可以使用以下指令来以服务模式运行Mariadb::

    $ systemctl start mariadb 

设置开机启动::

    $ systemctl enable mariadb 



安装toughradius
----------------------------------------

安装完成后，toughctl命令可用。

::

    $ pip install toughradius
    

创建配置文件
----------------------------------------

请确保你的mysql服务器已经安装运行，根据提示配置正确的数据库连接信息。

::

    $ toughctl --config
    
    [INFO] - set config...
    [INPUT] - set your config file path,[ /etc/radiusd.conf ]
    [INFO] - set default option
    [INPUT] - set debug [0/1] [0]:
    [INPUT] - time zone [ CST-8 ]:
    [INFO] - set database option
    [INPUT] - database type [mysql]:
    [INPUT] - database host [127.0.0.1]:
    [INPUT] - database port [3306]:
    [INPUT] - database dbname [toughradius]:
    [INPUT] - database user [root]:
    [INPUT] - database passwd []:
    [INPUT] - db pool maxusage [30]:
    [INFO] - set radiusd option
    [INPUT] - radiusd authport [1812]:
    [INPUT] - radiusd acctport [1813]:
    [INPUT] - radiusd adminport [1815]:
    [INPUT] - radiusd cache_timeout (second) [600]:
    [INPUT] - log file [ logs/radiusd.log ]:/var/log/radiusd.log
    [INFO] - set mysql backup ftpserver option
    [INPUT] - backup ftphost [127.0.0.1]:
    [INPUT] - backup ftpport [21]:
    [INPUT] - backup ftpuser [ftpuser]:
    [INPUT] - backup ftppwd [ftppwd]:
    [INFO] - set admin option
    [INPUT] - admin http port [1816]:
    [INPUT] - log file [ logs/admin.log ]:/var/log/admin.log
    [INFO] - set customer option
    [INPUT] - customer http port [1817]:
    [INPUT] - log file [ logs/customer.log ]:/var/log/customer.log
    [SUCC] - config save to /etc/radiusd.conf


初始化数据库
----------------------------------------

::
    
    #还未创建数据库，使用参数 initdb 1 或 initdb 2
    $ toughctl --initdb 1
     
    #已创建数据库，使用参数 initdb 3
    $ toughctl --initdb 3
    
运行服务
----------------------------------------

::

    #radius认证计费服务
    $ toughctl --radiusd
     
    #radius管理控制台服务
    $ toughctl --admin
     
    #radius用户自助服务
    $ toughctl --customer
    

以守护进程模式运行
----------------------------------------

::

    #启动服务，参数选择 [all|radiusd|admin|customer]
    
    $ toughctl --start all 
    
    #停止服务 参数选择 [all|radiusd|admin|customer]
    
    $ toughctl --stop all 
     

    
web管理控制台的使用
----------------------------------------

当安装部署完成后可使用浏览器进入管理控制台进行操作。

默认地址与端口：http://serverip:1816 
 
默认管理员与密码：admin/root


自助服务系统的使用
----------------------------------------

自助服务系统运行于一个独立的进程。

默认地址与端口:http://serverip:1817
