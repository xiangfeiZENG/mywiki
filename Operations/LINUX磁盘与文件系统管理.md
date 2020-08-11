## Linux磁盘与文件系统管理

重要概念：

* 扇区（Sector）为最小的物理存储单元，每个扇区为512bytes；
* 柱面：将扇区组成一个院，就是柱面（Cylinder），柱面是分区（partition）的最小单元。
* 第一个扇区最重要，里面有硬盘主应道记录（Masterbootrecord，MBR）及分区表（partition table），其中MBR占有446bytes，而partition table则占用64bytes。

文件系统最前面有一个启动扇区（boot sector），这个启动扇区可以安装应道装载程序。

![1537259654711](.\images\1537259654711.png)

* data block（数据块）：放置文件呃逆荣的地方，在Ext3文件系统中所支持的block大小有1KB，2KB及4KB三种。