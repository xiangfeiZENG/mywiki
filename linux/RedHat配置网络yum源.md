# RedHat配置网络yum源

标签（空格分隔）： Linux

---

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





