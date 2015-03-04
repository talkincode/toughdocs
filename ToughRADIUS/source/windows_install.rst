ToughRADIUS在windows下的安装配置
====================================


ToughRADIUS版本下载
--------------------------------

前往ToughRADIUS主页下载地址，请根据实际发布版本号进行选择

::

    http://www.toughradius.net/download.html


数据库安装配置
--------------------------------

如果使用sqlite数据库，则只需简单配置如下即可,使用sqlite无需安装任何数据库软件。

::

    [database]
    dbtype = sqlite
    dburl = sqlite:///./toughradius.sqlite3


当你需要使用Mysql数据库时，你需要在系统上安装并运行它，如果你并不需要采用mysql作为存储，而是使用默认sqlite作为数据存储，你可以略过下面的步骤。

.. topic:: 温馨提示

    Mysql是一个专业的数据库软件，不过在实际的安装过程中也会出现一些比较专业的难题，建议你在计划使用前多了解关于MySQL的相关知识。
    
    另外Sqlite是一个优秀的，高性能的嵌入式数据库软件，使用备份方便，极力推荐使用。
    

下面我们以MySQL作为示例，首先前往mysql服务器下载地址：

::

    http://dev.mysql.com/downloads/mysql/


本例以ZIP格式安装包为例，根据你的系统类型选择32位或64位的ZIP格式文件：

.. image:: _static/images/winins_mysql_download.jpg


解压下载完成的MySQL压缩包，比如C:\盘，目录结构如下：

.. image:: _static/images/winins_mysql_dir.jpg


创建MySQL配置文件，默认是C:\WINDOWS\my.ini，根据实际系统配置进行定义，以下是一个简单的配置：

::

    [mysqld]
    # set basedir to your installation path
    basedir=C:\\mysql-5.6.23-win32
    # set datadir to the location of your data directory
    datadir=C:\\mysql-5.6.23-win32\\data
    default-storage-engine=InnoDB
    wait_timeout=31536000
    interactive_timeout=31536000
    log-bin=mysql-bin

.. topic:: 注意

    更多的MySQL配置细节请参考MySQL官方文档进行配置，数据库的配置是一项很重要的工作，直接影响到系统的整体性能和稳定。


通过DOS命令运行MySQL服务：

::

    C:\mysql-5.6.23-win32\bin>mysqld —console
    

可以看到控制台输出日志信息：

::

    2015-02-17 01:40:12 0 [Warning] option 'wait_timeout': unsigned value 31536000 a
    djusted to 2147483
    2015-02-17 01:40:12 0 [Warning] option 'wait_timeout': unsigned value 31536000 a
    djusted to 2147483
    2015-02-17 01:40:12 0 [Warning] TIMESTAMP with implicit DEFAULT value is depreca
    ted. Please use --explicit_defaults_for_timestamp server option (see documentati
    on for more details).
    2015-02-17 01:40:12 52440 [Note] Plugin 'FEDERATED' is disabled.
    2015-02-17 01:40:12 52440 [Note] InnoDB: Using atomics to ref count buffer pool
    pages
    2015-02-17 01:40:12 52440 [Note] InnoDB: The InnoDB memory heap is disabled
    2015-02-17 01:40:12 52440 [Note] InnoDB: Mutexes and rw_locks use Windows interl
    ocked functions
    2015-02-17 01:40:12 52440 [Note] InnoDB: Memory barrier is not used
    2015-02-17 01:40:12 52440 [Note] InnoDB: Compressed tables use zlib 1.2.3
    2015-02-17 01:40:12 52440 [Note] InnoDB: Not using CPU crc32 instructions
    2015-02-17 01:40:12 52440 [Note] InnoDB: Initializing buffer pool, size = 128.0M

    2015-02-17 01:40:12 52440 [Note] InnoDB: Completed initialization of buffer pool

    2015-02-17 01:40:12 52440 [Note] InnoDB: Highest supported file format is Barrac
    uda.
    2015-02-17 01:40:12 52440 [Note] InnoDB: 128 rollback segment(s) are active.
    2015-02-17 01:40:12 52440 [Note] InnoDB: Waiting for purge to start
    2015-02-17 01:40:12 52440 [Note] InnoDB: 5.6.23 started; log sequence number 162
    5977
    2015-02-17 01:40:13 52440 [Warning] No existing UUID has been found, so we assum
    e that this is the first time that this server has been started. Generating a ne
    w UUID: dc7aea6c-b602-11e4-ab0e-00ff6f7b965f.
    2015-02-17 01:40:13 52440 [Note] Server hostname (bind-address): '*'; port: 3306

    2015-02-17 01:40:15 52440 [Note] IPv6 is not available.
    2015-02-17 01:40:15 52440 [Note]   - '0.0.0.0' resolves to '0.0.0.0';
    2015-02-17 01:40:15 52440 [Note] Server socket created on IP: '0.0.0.0'.
    2015-02-17 01:40:15 52440 [Note] Event Scheduler: Loaded 0 events
    2015-02-17 01:40:15 52440 [Note] mysqld: ready for connections.
    Version: '5.6.23-log'  socket: ''  port: 3306  MySQL Community Server (GPL)


可以看到MySQL服务器启动成功，不过这种方式对管理太不方便，我们可以将MySQL加入到系统服务

::

    C:\mysql-5.6.23-win32\bin>mysqld --install


看到以下输出 说明安装系统服务成功。

::

    Service successfully installed.
    
    
启动MySQL

::

    C:\>net start mysql

看到输出：

::

    MySQL 服务正在启动 ..
    MySQL 服务已经启动成功。


说明启动成功。

.. topic:: 

    更多的MySQL管理细节请参考MySQL官方文档。
    

初始化ToughRADIUS数据库
--------------------------------

解压缩下载的ToughRADIUS Windows版本，目录结构如下：

.. image:: _static/images/winins_tr_dir.jpg


修改radiusd.conf配置文件数据库部分，请修改数据库地址用户名密码等选项与实际相符.

::

    [database]
    dbtype = sqlite
    dburl = sqlite:///./toughradius.sqlite3
    echo = false
    #dbtype = mysql
    #dburl = mysql://root:root@127.0.0.1/toughradius?charset=utf8
    #pool_size = 30
    #pool_recycle = 300


关于更多的配置细节请参考章节《系统全局配置说明》

在ToughRADIUS工作目录下，有一个可执行工具文件toughctl.exe,可以用他来初始化toughradius的数据库：
在命令行下执行：

::

    toughctl.exe —initdb -c radiusd.conf

输出如下：

::

    C:\toughradius>toughctl.exe --initdb -c radiusd.conf
    starting update database...
    update database done

.. topic:: 注意

    运行工具会尝试删除原有数据库表并重建，如果非首次安装，建议备份数据


运行ToughRADIUS
--------------------------------

ToughRADIUS的三个子系统以独立进程的方式启动，可以通过三个exe可执行万件来运行
点击radiusd.exe即可运行Radius核心认证计费服务

.. image:: _static/images/winins_radiusd_run.jpg

点击admin.exe即可运行web管理控制台服务

.. image:: _static/images/winins_admin_run.jpg

点击customer.exe即可运行自助服务系统

.. image:: _static/images/winins_customer_run.jpg


现在，你可以通过浏览器访问管理控制台和自助服务系统了，具体的端口根据配置文件中设置的进行修改。
管理控制台的访问url：

http://127.0.0.1:1816

自助服务系统的url：

http://127.0.0.1:1817


.. topic:: 关于系统日志

    系统在运行期间产生的日志被记录在日志文件，每个子系统都有独立的日志文件，每天会产生一个新的日志文件。当系统出现问题或故障时，通过日志分析原因是一个很有效的方法，你应该总是保存最近一段时间的系统日志。日志文件的配置可以在radiusd.conf文件中设置。


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


Radius核心认证计费服务配置

::

    [radiusd]
    # 认证端口
    authport = 1812
    # 计费端口
    acctport = 1813
    # 管理端口，提供管理控制台调用
    adminport = 1815
    # radiusd子系统的日志文件位置
    logfile = logs/radiusd.log
    # Radius数据缓存最大时间，默认600秒
    cache_timeout = 600


管理控制台配置

::

    [admin]
    # 管理控制台web端口
    port = 1816
    # admin子系统的日志文件位置
    logfile = logs/radiusd.log


自助服务系统配置

::

    [customer]
    # 自助服务系统web端口
    port = 1817
    # customer子系统的日志文件位置
    logfile = logs/radiusd.log


配置文件的位置：

在windows环境下，ToughRADIUS会从当前目录下，或系统环境变量%WINDIR%目录（一般是C:\WINDOWS）下查找名称为radiusd.conf的配置文件。

使用toughctl.exe时，优先从 -c 参数指定的文件加载。
