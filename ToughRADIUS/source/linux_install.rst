ToughRADIUS在linux下的安装配置
====================================

ToughRADIUS是基于Python及高性能异步网络框架Twisted开发，对linux系统完美支持，可以提供更高的性能和稳定性。


安装系统依赖(centos6/7)
--------------------------------------

安装系统python依赖 python-devel python-setuptools,安装python-setuptools后，easy_install命令将可用

::

    $ yum update -y  && yum install -y  python-devel python-setuptools 
    
toughradius以python标准模块发布，因此我们需要用到python的专用pip包管理工具来安装，以下指令可以安装pip工具，安装完成后，pip命令将可用。

::

    $ easy_install pip
    

.. topic:: 关于数据库的选择

    toughradius默认采用嵌入式数据库sqlite，sqlite通常已经系统内置，不需要另行安装。

    如果你需要使用mysql数据库，则需要安装额外的mysql客户端驱动::
    
        # centos 6
        $ yum install -y  mysql-devel MySQL-python
        
        # centos7
        $ yum install -y  mariadb-devel MySQL-python
        
.. topic:: 温馨提示

    Mysql是一个专业的数据库软件，不过在实际的安装过程中也会出现一些比较专业的难题，建议在计划使用前多了解关于MySQL的相关知识。
    
    另外Sqlite是一个优秀的，高性能的嵌入式数据库软件，使用备份方便，极力推荐使用。


Mysql数据库安装
--------------------------------------

当你需要使用Mysql数据库时，你需要在系统上安装并运行它，如果你并不需要采用mysql作为存储，而是使用默认sqlite作为数据存储，你可以略过下面的步骤。

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

.. topic:: 温馨提示

    在实际生产环境中，要发挥MySQL的优势，还需要针对性的进行多项优化，请根据你的实际环境参考MySQL相关文档进行操作。

安装toughradius
----------------------------------------

安装完成后，toughctl命令可用。

::

    $ pip install toughradius
    
.. topic:: 注意

    在某些linux发行版，pip会有些bug，如果以上指令无法安装成功，可以尝试：
    
    pip install -U https://github.com/talkincode/ToughRADIUS/archive/stable.zip
    

创建配置文件
----------------------------------------

通过toughctl --echo_radiusd_cnf 进行配置文件的初始化，

::

    $ toughctl --echo_radiusd_cnf > /etc/radiusd.conf
    
以上操作将会生成/etc/radiusd.conf配置文件，根据实际环境进行修改，关于配置文件的内容，请参考章节《系统全局配置说明》
    

如果你使用mysql数据库，请请确保你的mysql服务器已经安装运行，根据提示配置正确的数据库连接信息。

对于mysql，dburl格式为

::

    mysql://user:passwd@host:port/dbname?charset=utf8


初始化数据库
----------------------------------------

手工创建数据库，sqlite可以忽略这一步

::

    $ echo "create database toughradius DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci" | mysql

使用toughctl工具初始化所有表，注意此操作会重建所有数据库表，请注意备份重要数据。

::

    $ toughctl --initdb 


    
运行服务
----------------------------------------

::

    #radius认证计费服务
    $ toughctl --radiusd
     
    #radius管理控制台服务
    $ toughctl --admin
     
    #radius用户自助服务
    $ toughctl --customer
    
    #通过一个进程运行所有服务（radiusd，admin，customer）
    $ toughctl --standalone
    

以守护进程模式运行
----------------------------------------

::

    #启动服务，参数选择 [all|radiusd|admin|customer|standalone]
    
    $ toughctl --start all 
    
    #停止服务 参数选择 [all|radiusd|admin|customer|standalone]
    
    $ toughctl --stop all 
     
    #添加系统自启动,该脚本不能用于centos7，根据实际环境可对/etc/init.d/radiusd进一步修改。
    
    $ toughctl --echo_radiusd_script > /etc/init.d/radiusd
    
    $ chmod +x /etc/init.d/radiusd
    
当启动all时，将会同时启动radiusd,admin,customer三个服务进程，当启动standalone模式时，只会启动一个进程. 
    
web管理控制台的使用
----------------------------------------

当安装部署完成后可使用浏览器进入管理控制台进行操作。

默认地址与端口：http://serverip:1816 
 
默认管理员与密码：admin/root


自助服务系统的使用
----------------------------------------

自助服务系统运行于一个独立的进程。

默认地址与端口:http://serverip:1817


数据库的备份管理
----------------------------------------

toughradius可以支持多种类型数据库，在备份格式上，采用了通用的json格式来备份。

以下指令可以用来进行数据库备份：

    $ toughctl --dumpdb /var/toughradius/data/toughradius-db-20150203-01.json.gz

以下指令可以用来从备份数据文件恢复数据库：

    $ toughctl --restoredb /var/toughradius/data/toughradius-db-20150203-01.json.gz
    
.. topic:: 注意

    数据备份恢复，并不包括表结构，在恢复数据库前，可以先用toughctl --initdb初始化表结构。
    
    数据库备份管理是一项严谨的工作，请根据你的实际环境进行操作。

在web管理界面上,提供了手动备份方式。

另外可以结合linux的crontab来实现数据库的自动备份。


在linux下使用HTTPS
----------------------------------------

ToughRADIUS通过ssl进一步加强了系统的安全性。首先确保系统openssl已安装，并安装python的openssl相关依赖包

::

    $ yum install -y openssl
    
    $ yum install -y libffi-devel
    
    $ pip install pyOpenSSL>=0.14
    
    $ pip install service_identity


生成服务器密钥以及签名证书
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

 ::
 
    $ cd /var/toughradius
 
    $ openssl genrsa > privkey.pem
    
    $ openssl req -new -x509 -key privkey.pem -out cacert.pem -days 1000


新增配置选项
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在原配置文件[DEFAULT]选项下新增以下内容

::

    [DEFAULT]
    debug = 1
    tz = CST-8
    # ------新增内容-------- #
    secret = LpWE9AtfDPQ3ufXBS6gJ37WW8TnSF920
    ssl = true
    privatekey = /var/toughradius/privkey.pem
    certificate = /var/toughradius/cacert.pem
    # ------新增内容-------- #

ssl,privatekey,certificate是新增的三个配置选项，启用ssl就设置为true或on,否则为false或off，privatekeycertificate与certificate文件必须存在。

接下来就可以启动系统了。


使用https访问管理控制台和自助服务系统
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    https://127.0.0.1:1816
    
    https://127.0.0.1:1817


系统全局配置说明
--------------------------------

radiusd.conf是ToughRADIUS的全局配置文件，可以指定所有的系统参数。

通用选项

::

    [DEFAULT]
    # 是否以debug模式启动，0为否，1为是，在debug模式下，可以输出更多的信息
    debug = 1
    # 时区设置，适用于linux环境
    tz = CST-8
    # 系统用户数据加密，cookie加密使用的密钥，长度为8的倍数，注意不要泄露
    secret = 0UhbGOuqKXnMmpfRbma76hkzWTl4WUER
    # 如果启用https，需要加入以下三个选项
    ssl = true
    privatekey = /var/toughradius/privkey.pem
    certificate = /var/toughradius/cacert.pem


数据库配置选项

::

    [database]
    # 数据库类型，支持Sqlite, Oracle, MySQL, PostgreSQL, MSSQL
    dbtype = mysql
    # dbtype = sqlite
    # dburl = sqlite:////tmp/toughradius.sqlite3
    # 是否打印sql语句调试
    echo = false
    # 数据库地址，每种类型的数据库都不太一样，注意安装文档说明
    dburl = mysql://root:root@127.0.0.1/toughradius?charset=utf8
    # 数据库连接池最大数
    pool_size = 30
    # 数据库连接检测间隔，秒
    pool_recycle = 300
    # 数据库备份路径
    backup_path = /var/toughradius/data


Radius核心认证计费服务配置

::

    [radiusd]
    # 监听地址,如果需要监听指定地址，可使用host选项
    # host = 127.0.0.1
    # 认证端口
    authport = 1812
    # 计费端口
    acctport = 1813
    # 管理端口，提供管理控制台调用
    adminport = 1815
    # radiusd子系统的日志文件位置
    logfile = /var/log/radiusd.log
    # Radius数据缓存最大时间，默认600秒
    cache_timeout = 600


管理控制台配置

::

    [admin]
    # 监听地址,如果需要监听指定地址，可使用host选项
    # host = 127.0.0.1
    # 管理控制台web端口
    port = 1816
    # admin子系统的日志文件位置
    logfile = /var/log/admin.log


自助服务系统配置

::

    [customer]
    # 监听地址,如果需要监听指定地址，可使用host选项
    # host = 127.0.0.1
    # 自助服务系统web端口
    port = 1817
    # customer子系统的日志文件位置
    logfile = /var/log/customer.log


配置文件的位置：

使用toughctl -c 选项可以指定配置文件的位置，默认情况下会从/etc/radiusd.conf位置查找。
