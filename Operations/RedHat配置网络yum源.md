# RedHat配置yum源

标签（空格分隔）： Linux

---

### 1 配置网络yum源

编辑`/etc/yum.repos.d/rhel-source.repo`修改为以下内容：

```
[base]
name=CentOS-$releasever - Base
baseurl=http://mirrors.163.com/centos/6/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-6
[updates]
name=CentOS-$releasever - Updates
baseurl=http://mirrors.163.com/centos/6/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-6
[extras]
name=CentOS-$releasever - Extras
baseurl=http://mirrors.163.com/centos/6/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-6
[centosplus]
name=CentOS-$releasever - Plus
baseurl=http://mirrors.163.com/centos/6/centosplus/$basearch/
gpgcheck=1
enabled=0
```

清理缓存：
```
yum clean all
```
验证：
```
yum list
```
到此，网络yum元配置成功。

### 2 配置本地yum源

#### 2.1 开机自动挂载cdrom

编辑fstab文件，加入以下配置。

```
[root@localhost ~]# vi /etc/fstab 
#add by zengxf
/dev/sr0               /media/cdrom             iso9660 auto,ro         0 0
```

- /dev/device：需要挂载的设备
- mountpoint ：挂载点。
- type：文件系统类形。
- rules 是指挂载时的规则。下面列举几个常用的：

​        auto 开机自动挂载

​        default 按照大多数永久文件系统的缺省值设置挂载定义

​        noauto 开机不自动挂载

​        nouser 只有超级用户可以挂载

​        ro  按只读权限挂载

​        rw 按可读可写权限挂载

​        user 任何用户都可以挂载

**请注意**光驱和软驱只有在装有介质时才可以进行挂载，因此它是noauto

- 0 是指dump(系统备份工具）。这一项为0，就表示从不备份。如果上次用dump备份，将显示备份至今的天数。
- order 指fsck（启动时fsck检查的顺序）。为0就表示不检查，（/）分区永远都是1，其它的分区只能从2开始，当数字相同就同时检查（但不能有两1）。

修改了/etc/fstab后，一定要重新引导系统才会有效。

#### 2.2 修改yum配置

备份yum配置，修改media的enable为**1**。	

```
[root@localhost yum.repos.d]# mkdir bak&&mv CentOS* ./bak
[root@localhost yum.repos.d]# mv ./bak/CentOS-Media.repo ./
[root@localhost yum.repos.d]# ls
bak  CentOS-Media.repo
[root@localhost yum.repos.d]# vi CentOS-Media.repo 

# CentOS-Media.repo
#
#  This repo can be used with mounted DVD media, verify the mount point for
#  CentOS-7.  You can use this repo and yum to install items directly off the
#  DVD ISO that we release.
#
# To use this repo, put in your DVD and use it with the other repos too:
#  yum --enablerepo=c7-media [command]
#
# or for ONLY the media repo, do this:
#
#  yum --disablerepo=\* --enablerepo=c7-media [command]

[c7-media]
name=CentOS-$releasever - Media
baseurl=file:///media/CentOS/
        file:///media/cdrom/
        file:///media/cdrecorder/
gpgcheck=1
enabled=0
```

执行`yum clean all`，再执行`yum list`进行测试。









