## MOOSEFS总结

### 简介

MooseFS is a fault-tolerant distributed le system. It spreads data over multiple physical
locations (servers), which are visible to user as one resource. For standard le operations
MooseFS acts as any other Unix-alike lesystem。



### 安装

#### 1. 安装编译包

CentOS/RHEL: `sudo yum install gcc make libpcap-devel zlib-devel fuse-devel pkgconfig` 

#### 2. Install at least one Master Server

1. Install `moosefs-master` package
2. Prepare default config (as `root`):

```
cd /etc/mfs
cp mfsmaster.cfg.sample mfsmaster.cfg
cp mfsexports.cfg.sample mfsexports.cfg
```

> File mfsmaster.cfg contains master server settings. You can find out more information about this file in the man pages (man mfsmaster.cfg).
>
> File mfsexports.cfg species which users' computers can mount the le system and with what
> privileges. For example, to specify that only machines addressed as 192.168.2.x can use the
> whole structure of MooseFS resources (/) in read/write mode, in the first line which is not
> commented out change the asterisk (*) to 192.168.2.0/24, so that you'll have:
>
> `192.168.2.0/24 / rw , alldirs , maproot =0`

3. Prepare the metadata file (as `root`):

```
cd /var/lib/mfs
cp metadata.mfs.empty metadata.mfs
chown mfs:mfs metadata.mfs
rm metadata.mfs.empty
```

4. Run Master Server (as `root`): `mfsmaster start`
5. Make this machine visible under `mfsmaster` name (e.g. by adding a DNS entry or adding it in `/etc/hosts` on all servers)

#### 3. Install at least two Chunkservers

1. Install `moosefs-chunkserver` package
2. Prepare default config (as `root`):

```
cd /etc/mfs
cp mfschunkserver.cfg.sample mfschunkserver.cfg
cp mfshdd.cfg.sample mfshdd.cfg
```

3. At the end of `mfshdd.cfg` file make one or more entries containing paths to HDDs / partitions designated for storing chunks, e.g.:

```
/mnt/chunks1
/mnt/chunks2
/mnt/chunks3
```

> It is recommended to use XFS as an underlying filesystem for disks designated to store chunks.
>
> **注意**：该目录为实际存储数据的目录。

4. Change the ownership and permissions to `mfs:mfs` to above mentioned locations, e.g.:

```
chown mfs:mfs /mnt/chunks1 /mnt/chunks2 /mnt/chunks3
chmod 770 /mnt/chunks1 /mnt/chunks2 /mnt/chunks3
```

5. Start the Chunkserver: `mfschunkserver start`

Repeat steps above for second (third, ...) Chunkserver.

#### 3. Client side: mount MooseFS filesystem

1. Install `moosefs-client fuse libfuse2` packages
2. Mount MooseFS (as `root`):

```
mkdir /mnt/mfs
mount -t moosefs mfsmaster: /mnt/mfs
```

or: `mfsmount -H mfsmaster /mnt/mfs`

1. You can also add an `/etc/fstab` entry to mount MooseFS during the system boot:

```
mfsmaster:    /mnt/mfs    moosefs    defaults,mfsdelayedinit    0 0
```

There are more configuration parameters available but most of them  may stay with defaults. We do our best to keep MooseFS easy to deploy  and maintain.

MooseFS, for testing purposes, can even be installed on a single machine!

