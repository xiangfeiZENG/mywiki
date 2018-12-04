

---
title: Docker基础
date: 2018-10-31 19:42:41
categories: docker
tags: docker
---

﻿# Docker基础

[TOC]

﻿# 1 简介

Docker是一个能够把开发的应用程序自动部署到容器的开源引擎。Docker借鉴了标准集装箱的概念，集装箱运输货物，Docker运输软件。特点：标准型，隔离型。

> Docker核心组件：
>
> - Docker客户端和服务器，也成为Docker引擎；
> - Docker镜像；
> - Registry；
> - Docker容器；

Docker利用Linux内核的命名空间（namespace），用于隔离文件系统、进程和网络；使用cgroups（即control group，linux的内核特性之一）将CPU和内存之类的资源独立分配给每个Docker容器。

### 2 Docker安装

步骤：

#### 2.1 检查先决条件

内核：内核版本要求3.8及以上，`uname -a`确定。

检查Device Mapper。`grep device-mapper /proc/devices`；

#### 2.2 安装Docker

1.配置yum，如安装epel

```
rpm -Uvh http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm 
```
2.安装docker
```
[root@localhost ~]# yum -y install docker
```

3.启动docker
```
[root@localhost ~]# service docker start
Starting docker:        [确定]
```

4.设置docker开机启动
```
[root@localhost ~]# systemctl start docker
```



### 3 创建容器

1. 创建交互式容器

   ```
   docker run -i -t ubuntu /bin/bash
   ```

   - -i：标志保证容器中STDIN是开启的。
   - -t：告诉Docker腰围创建的容器分配一个伪tty终端。

2. 创建守护式容器

   ```
   docker run -d ubuntu /bin/bash -c "while true; do echo hello world; sleep 1; done"
   ```

   -d：detach，分离，docker会将容器放在后台运行。


### 4 常用容器命令

**docker logs 容器** 查看日志。**docker logs -f 容器** 查看实时日志。

**docker top 容器** ：查看容器内的所有进程。

**docker stats** 容器：查看容器cpu、内存、网络I/O，及存储I/O，用于监控。

**docker exec**：通过`docker exec -t -i a170493783b9 /bin/bash`在容器内执行交互式命令。

**docker inspect** ：查看更多容器信息。查看具体ip。

```\
docker inspect --format {{.NetworkSettings.IPAddress}} a170493783b9 
```

**docker rm**：删除容器。

**docker ps**： 查看这在运行的容器

**docker port 容器名 端口** ：查看容器端口映射情况。

**docker cp**：Copy files/folders between a container and the local filesystem.



### 5 Docker镜像和仓库

Docker镜像是用来启动容器的构建基石，Docker采用联合加载（union nount）技术，联合加载指的是一次同时加载多个文件系统，但是在外面看起来只能看到一个文件系统。联合加载会将各层文件系统叠加到一起，这样最终的文件系统会包含所有的底层文件和目录。

当Docker第一次启动一个容器时，初始的读写层是空的。当文件系统发生变化时，这些变化都会应用到这一层上。比如，如果想修改一个文件，这个文件首先会从该读写层下面的只读层复制到该读写层。该文件的只读版本依然存在，但是已经被读写层中的该文件副本所隐藏。通常这种机制被称为写时复制（copy on write），每个只读镜像层都是只读的，并且以后永远不会变化。

相关命令：

docker images：查看下系统中已有镜像

docker pull：拉取镜像

docker search：查询镜像

docker login 登陆远程仓库

##### 5.1 构建镜像

构建镜像有两种方式：

- 使用docker commit命令
- 使用docker build命令和Dockerfile文件

一般采用dockerfile的形式

###### 5.1.1 Dockerfile指令

**RUN**：每条RUN指令都会创建一个新的镜像层，如果该指令执行成功，就会将该镜像层提交，之后继续执行Dockerfile中的下一条指令。

**EXPOSE**：该指令高数Docker该**容器内**的应用程序将会使用容器的指定端口。Docker并不会自动打开该端口，二室需要用户在使用docker run 运行容器时来指定打开哪些端口。（-P 随机绑定宿主机器的一个端口）

**CMD**：用于指定一个容器启动时要运行的命令。有点类似于RUN指令，只是RUN指令是指定镜像被构建时要运行的命令，CMD是指定容器被启动是要运行的命令。Dockerfile中只能指定一条CMD指令，如果指定了多条也只有最后一条会被执行。

**ENTRYPOINT**：该指令与CMD类似，该指定不容易在容器启动时被覆盖，docker run 命令行中指定的任何参数都会被当做参数再次传递给ENTRYPOINT指令中指定的名命令。

**WORKDIR**：该指令用来从镜像创建一个新容器时，在容器内部设置一个工作目录，ENTRYPOING和CMD指定的程序都会在这个目录下执行。

**ENV**：该指令用来在镜像构建过程中设置环境变量。

**USER**：该指令用来指定该镜像会以什么样的用户去运行。

**VOLUME**：该指令用来想基于镜像创建的容器添加卷。一个卷可以存在于一个或者多个容器内的特定的目录，该目录可以绕过联合文件系统，提供共享数据或者对数据进行持久化的功能。卷功能让我们可以将数据（如源代码）、数据库或者其他内容添加到镜像而不是将这些内容提交到镜像中，并且允许我们在多个容器见共享这些内容。`VOLUME ["OPT/PROJECT"]`。

**ADD**：该指令用来构建环境下的文件和目录复制到镜像中。ADD支持url，支持压缩文件解压。**ADD会是得构建缓存变得无效。**

**COPY**：类似于add，不支持文件提取和解压。具体过程是将文件从构建环境中上传到Dcoker守护进程，复制是在Docker守护进程中进行的。任何位于构建环境之外的东西是不可用的。COPY指令的目的位置必须是容器内部的一个绝对路径。

**LABEL**:该指令用于为Docker镜像添加元数据。元数据是以键值对的形式展现。

**STOPSIGNAL**：该指令用来设置停止容器是发送什么系统调用信号给容器。

**ARG**：该指令用来定义可以在docker build命令运行是传递构建运行时的变量。Docker预定义了一组ARG变量，可以在构建时直接使用。使用方法：`docker build --build-arg <variable>=<value>`

**ONBUILD**：该指令为镜像添加触发器（trigger）。当一个镜像被用作其他镜像的基础镜像时，该镜像中的触发器将被执行。ONBUILD只会被继承一次。





### 6 Docker应用

通过docker build构建镜像,进入dockerfile所在目录执行：

```
docker build -t tag .
```

通过docker images可以查看已经构建的镜像。

通过以下命令启动容器：

```
docker run -d -p 80 --name website -v /root/sample/website:/var/www/html/website jamtur01/nginx nginx
```

注意事项：

1.  启动容器是报错“iptables..."，无法启动。docker启动依赖于防火墙，不能关闭防火墙。

2. Docker挂载主机目录Docker访问出现Permission denied，主要原因和解决方法如下：

   ```
      问题原因及解决办法
      原因是CentOS7中的安全模块selinux把权限禁掉了，至少有以下三种方式解决挂载的目录没有权限的问题：
      1.在运行容器的时候，给容器加特权，及加上 --privileged=true 参数：
      docker run -i -t -v /soft:/soft --privileged=true 686672a1d0cc /bin/bash
      2.临时关闭selinux：
      setenforce 0
      3.永久性关闭
      vi /etc/selinux/config 将SELINUX=enforcing的值改为disabled，重启机器即可。
   ```


#### 6.1 Docker网络

docker容器的应用可以互联，一般采用以下几种方式：

- Docker的内部网络。
- 从Docker1.9 及之后的版本开始，可以使用Docker Networking 以及docker network命令。
- Docker链接。一个可以将具体容器链接到一起来进行通信的抽象层。

推荐使用Docker Networking，优点如下：

- Docker Networking 可以将容器连接到不同宿主机上的容器。
- 通过Docker Networking 链接的容器可以在无需更新连接的情况下，对停止、启动或者重启容器。而使用Docker链接，则可能需要更新一些配置，或者重启相应的容器来维护Docker容器之间的链接。
- 使用Docker Networking，不必实现创建容器再去连接它。同样，也不必关系容器的运行顺序，读者可以在网络内部获得容器名解析和发下。



##### 6.1.1 Docker内部连网

安装Docker时，会创建一个新的网络接口，名字是docker0。每个Docker容器都会在这个接口上分配一个IP地址。docker0是一个虚拟的以太网桥，用于连接容器和本地宿主网络。

```
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 0.0.0.0
        ether 02:42:e1:15:52:bb  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

启动容器后，会发现一些列以名字veth开头的接口：

```
vethfe9257f: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::bc54:2ff:feb4:8fa2  prefixlen 64  scopeid 0x20<link>
        ether be:54:02:b4:8f:a2  txqueuelen 0  (Ethernet)
        RX packets 8  bytes 648 (648.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 22  bytes 2406 (2.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

##### 6.1.2 Docker Networking

容器之间的连接用网络创建，这被称为Docker Networking。Docker Networking 允许用户创建自己的网络，容器可以通过这个网上互相通信。实质上，Docker Networking以新的用户管理的网络补充了现有的docker0.

常用命令：

- docker network create mynet ：创建一个桥接网络，命名为mynet，这个命令返回新创建的网络ID。
- docker network inspect mynet：查看mynet网络。
- docker network ls：列出当前系统中的所有网络。
- docker network connect mynet App：将正在运行的容器添加到已有的网络中。
- docker network disconect mynet app：从网络中断开一个容器。

##### 6.1.3 通过Docker链接链接容器

通过docker run中加入参数`--link`，该标志创建两个容器间的客户-服务链接。这个标志需要两个参数，一个是要链接的容器名字，另一个是链接的别名。，如：`--link redis:db`,redis是“服务”，并且为这个服务增加了db作为别名。



#### 6.2 Docker实例：用于持续集成

需进一步学习jenkins



