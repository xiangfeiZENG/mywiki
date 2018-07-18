# Linux常用软件安装

标签（空格分隔）： Linux

[TOC]
---

## jdk安装
环境：redhat6.5_64
jdk版本：1.8
1. 官网下载安装包jdk-8u40-linux-x64.gz
2. jdk一般安装在`/usr/lib`下，解压缩安装包。
3. 解压后的jdk目录为jdk1.8.0 ，执行mv jdk1.8.0 jdk1.8 ，修改文件夹名字。
4. 修改/etc/profile,执行`vi /etc/profile`，添加以下内容,之后执行`.  /etc/profile`使配置文件生效。此时可以`echo $PATH`或者`echo $JAVA_HOME`检查是否配置成功。：
```
export JAVA_HOME=/usr/lib/jdk1.8
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```
5. 修改默认jdk，分别执行命令
```
update-alternatives --install /usr/bin/java java /usr/lib/jdk1.8/bin/java 300
update-alternatives --install /usr/bin/javac javac /usr/lib/jdk1.8/bin/javac 300
```
6. 执行update-alternatives --config java，选择java命令的版本,执行 update-alternatives --config javac，选择编译命令版本。
7. 执行`java -version`查看是否安装成功。


## redis安装







