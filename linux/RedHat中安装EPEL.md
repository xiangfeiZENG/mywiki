# RedHat中安装EPEL

标签（空格分隔）： Linux

---

安装方法：
```
rpm -Uvh http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
```

安装后测试报错：
```
Error: Cannot retrieve repository metadata (repomd.xml) for repository: epel.
```
解决方法：
修改epel.repo文件，修改mirrorlist=https://*****这行，将https修改为http即可正常使用。




