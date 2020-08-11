# CentOS 7.4 JDK安装

jdk 安装过程

1. 下载jdk

```
[root@xfei-server jdk]# wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" https://download.oracle.com/otn/java/jdk/8u241-b07/1f5b5a70bf22433b84d0e960903adac8/jdk-8u241-linux-x64.rpm
```

上述方法目前已不可用。

2. 安装jdk

```
# 如果已安装openjdk，则需要卸载
rpm -qa | grep java
yum remove openjdk
# 安装jdk
[root@xfei-server jdk]# rpm -ivh jdk-8u241-linux-x64.rpm 
```



3. 配置环境变量

```
[root@xfei-server jdk1.8.0_241-amd64]# vim /etc/profile
JAVA_HOME=/usr/java/jdk1.8.0_241-amd64
CLASSPATH=%JAVA_HOME%/lib:%JAVA_HOME%/jre/lib
PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin
export PATH CLASSPATH JAVA_HOME
[root@xfei-server jdk1.8.0_241-amd64]# source /etc/profile
```



