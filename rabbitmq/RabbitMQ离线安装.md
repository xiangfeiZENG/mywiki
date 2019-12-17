# RabbitMQ离线安装



因无法连接外网，并且挂载本地iso受限的前提下，在安装rabbitmq时遇到一些问题，记录下。

这里编译的话因为内网依赖的包太多，安装麻烦，采用已经编译好的软件。

下载地址：<https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/>

```
rabbitmq-server-generic-unix-3.7.14.tar.xz
```

里面包含已编译的包。



因为rabbitmq运行依赖于erlang，参考官网文档，有以下方式安装erlang。

- Team RabbitMQ produces [a package](https://github.com/rabbitmq/erlang-rpm) stripped down to only provide those components needed to run RabbitMQ. It might be easiest to use if installing Erlang's dependencies is proving difficult.
- [Erlang Solutions](https://www.erlang-solutions.com/resources/download.html) produces packages that are usually reasonably up to date and involve installation of a potentially excessive list of dependencies.
- [EPEL](http://fedoraproject.org/wiki/EPEL) ("Extra Packages for Enterprise Linux"); part of the Red Hat/Fedora organisation, provides many additional packages, including Erlang. These are the most official packages but tend to be out of date. The packages are split into many small pieces.
- [openSUSE](https://www.opensuse.org/) produces Erlang packages for each distribution (openSUSE and SLES)



采用第一种，安装rabbitmq运行最小依赖，根据连接找到下载仓库：

<https://dl.bintray.com/rabbitmq-erlang/rpm/erlang/20/el/7>

下载对应版本的rpm包：

```
erlang-20.3.8.22-1.el7.x86_64.rpm
```

安装后发现依赖：

openssl-libs-1.0.2k-16.el7.x86_64.rpm，我本地的是：openssl-libs-1.0.1



这里我采用的方法是找到1.0.2的包，强制安装了openssl-libs-1.0.2：

```
sudo rpm -ivh openssl-libs-1.0.2k-16.el7.x86_64.rpm --force --nodeps

sudo yum -y install erlang-20.3.8.22-1.el7.x86_64.rpm 
```



再次安装erlang后运行成功。



总结：erlang的安装在没有网络，无法挂载iso的情况下，安装erlang还是比较困难的，本文采用权宜措施，测试环境中使用，不建议生产中这么操作。