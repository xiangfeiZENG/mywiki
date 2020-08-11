# CentOS 7.4 Yum仓库配置

## Yum仓库配置

仓库配置文件位置`/etc/yum.repos.d/`

例如阿里的仓库配置：

```
[epel]
name=Extra Packages for Enterprise Linux 7 - $basearch
enabled=1
failovermethod=priority
baseurl=http://mirrors.cloud.aliyuncs.com/epel/7/$basearch
gpgcheck=0
gpgkey=http://mirrors.cloud.aliyuncs.com/epel/RPM-GPG-KEY-EPEL-7
```



配置mysql5.7-community 仓库

```
# Enable to use MySQL 5.7
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```



```
[root@xfei-server yum.repos.d]# yum clean all
[root@xfei-server yum.repos.d]# yum list |grep mysql
```



## Yum仓库中的包下载

yumdownloader 是下载rpm包的工具，是yum-utils 下的子集，所以安装时直接将yum-utils 安装即可。

```
[root@xfei-server yum.repos.d]# yum install yum-utils
```

默认下载到当前目录： yumdownloader <package-name>      

常用附加参数： --resolve  下载关联依赖包   --destdir  指定目录

例如：

```
# 将mysql57-community包及其所有依赖下载到/root/software目录下
[root@xfei-server software]# yumdownloader mysql-community-server --resolve --destdir=/root/software
```





