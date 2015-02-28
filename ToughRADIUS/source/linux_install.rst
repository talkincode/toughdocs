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
     
    #添加系统自启动
    
    $ echo "toughctl --start all" >> /etc/rc.local
    
web管理控制台的使用
----------------------------------------

当安装部署完成后可使用浏览器进入管理控制台进行操作。

默认地址与端口：http://serverip:1816 
 
默认管理员与密码：admin/root


自助服务系统的使用
----------------------------------------

自助服务系统运行于一个独立的进程。

默认地址与端口:http://serverip:1817


ToughRADIUS在linux下使用HTTPS
====================================

ToughRADIUS通过ssl进一步加强了系统的安全性。首先确保系统openssl已安装，如果在安装toughradius的过程中遇到编译错误，可能是遇到了缺少相关依赖库，比较典型的如::

    gcc -pthread -fno-strict-aliasing -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong –param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -DNDEBUG -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong –param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -fPIC -DUSE__THREAD -I/usr/include/ffi -I/usr/include/libffi -I/usr/include/python2.7 -c c/_cffi_backend.c -o build/temp.linux-x86_64-2.7/c/_cffi_backend.o

    c/_cffi_backend.c:13:17: 致命错误：ffi.h：没有那个文件或目录

    ＃include <ffi.h>

                     ^
    编译中断。

    error: command 'gcc' failed with exit status 1

    Command "/usr/bin/python -c "import setuptools, tokenize;__file__='/tmp/pip-build-75iRmo/cffi/setup.py';exec(compile(getattr(tokenize, 'open', open)(__file__).read().replace('\r\n', '\n'), __file__, 'exec'))" install –record /tmp/pip-GbVC1m-record/install-record.txt –single-version-externally-managed –compile" failed with error code 1 in /tmp/pip-build-75iRmo/cffi

这是由于缺少libffi-devel导致，在centos下通过以下指令安装::

    $ yum install -y libffi-devel
    
在ubuntu下通过以下指令安装::

    $ apt-get install -y libffi-dev

生成服务器密钥以及签名
----------------------------------------

 ::
 
    $ cd /var/toughradius
 
    $ openssl genrsa > privkey.pem
    
    $ openssl req -new -x509 -key privkey.pem -out cacert.pem -days 1000


新增配置选项
----------------------------------------

在原配置文件[DEFAULT]选项下新增以下内容

::

    [DEFAULT]
    debug = 1
    tz = CST-8
    secret = LpWE9AtfDPQ3ufXBS6gJ37WW8TnSF920
    ssl = true
    privatekey = /var/toughradius/privkey.pem
    certificate = /var/toughradius/cacert.pem

ssl,privatekey,certificate是新增的三个配置选项，启用ssl就设置为true或on,否则为false或off，privatekeycertificate与certificate文件必须存在。

接下来就可以启动系统了。

注意，只有当使用 toughctl --start 模式启动才会生效。


使用https访问管理控制台和自助服务系统
----------------------------------------

::

    https://127.0.0.1:1816
    
    https://127.0.0.1:1817