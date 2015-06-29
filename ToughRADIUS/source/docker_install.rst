使用Docker镜像部署ToughRADIUS
=======================================

Docker 是一个开源项目，诞生于 2013 年初，最初是 dotCloud 公司内部的一个业余项目。它基于 Google 公司推出的 Go 语言实现。 项目后来加入了 Linux 基金会，遵从了 Apache 2.0 协议，项目代码在 GitHub 上进行维护。

Docker 自开源后受到广泛的关注和讨论，以至于 dotCloud 公司后来都改名为 Docker Inc。Redhat 已经在其 RHEL6.5 中集中支持 Docker；Google 也在其 PaaS 产品中广泛应用。

Docker 项目的目标是实现轻量级的操作系统虚拟化解决方案。 Docker 的基础是 Linux 容器（LXC）等技术。

在 LXC 的基础上 Docker 进行了进一步的封装，让用户不需要去关心容器的管理，使得操作更为简便。用户操作 Docker 的容器就像操作一个快速轻量级的虚拟机一样简单。


Docker 安装
-------------------------------

关于Docker安装的更多详细内容请见：http://docs.docker.com/installation/

CentOS6
~~~~~~~~~~~~~~~~~~~~~~~~~

::

    $ sudo yum install http://mirrors.yun-idc.com/epel/6/i386/epel-release-6-8.noarch.rpm
   
    $ sudo yum install docker-io

    $ sudo service docker start


CentOS7
~~~~~~~~~~~~~~~~~~~~~~~~~

::

    $ sudo yum install docker

    $ sudo service docker start


Ubuntu
~~~~~~~~~~~~~~~~~~~~~~~~~

安装最新版本的Ubuntu包（可能不是最新的docker版本包）::

    $ sudo apt-get update
    $ sudo apt-get install docker.io
    $ sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker
    $ sudo sed -i '$acomplete -F _docker docker' /etc/bash_completion.d/docker.io

Windows
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

请下载Windows安装文件：https://github.com/boot2docker/windows-installer/releases/download/v1.4.1/docker-install.exe



获取ToughRADIUS镜像
------------------------------------

::

    docker pull index.alauda.cn/toughstruct/toughradius


创建并运行容器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

通过Docker部署ToughRADIUS，需要创建一个容器，以后的更新可以直接在此容器上进行::

    docker run -d --name trserver --net host toughstruct/toughradius:latest

关于trserver这个容器名称，可以修改为你想要的名称，比如myradius等，注意各个操作环节保持名称一致

运行 docker ps -a 可以看到容器进程信息

运行 docker logs trserver 查看容器日志输出

打开浏览器访问 http://serverip:1816,可以进入web管理登陆界面了。

打开浏览器访问 http://serverip:1818,可以进入web升级管理界面，可以方便的通过web控制面板实现版本的升级切换。


启动，停止，重启容器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    $ docker start trserver

    $ docker stop trserver

    $ docker restart trserver


ToughRADIUS版本更新
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

当ToughRADIUS版本更新时，不需要重新创建容器，只需要执行简单地更新指令即可::

    $ docker exec trserver toughrad upgrade

