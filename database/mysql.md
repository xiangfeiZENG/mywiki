



# mysql 实战

[TOC]



## 影响数据库性能的因素

数据库需要**完整性**和**一致性**，相对不容易扩展。





![image-20191211092123224](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191211092123224.png)

![image-20191211092301249](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191211092301249.png)

![image-20191211092421262](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191211092421262.png)

（最好不要在主库上进行数据备份）





QPS ：每秒处理的查量

TPS：每秒事务数

1. 超高的QPS和TPS：80%的数据库问题都是慢查询导致。

2. 大量的并发和超高的cpu使用率

   - 大量的并发：数据库连接数倍占满（max_connections,默认100)
   - 超高的cpu使用率：因cpu自由耗尽而出现宕机。

3. 磁盘io，磁盘io性能突然下降。（大促前调整维护计划）

4. 网卡流量：网卡io被占满（1000Mb/8 相当于 100MB）

   - 减少从服务器的数量
   - 进行分级缓存
   - 避免使用“select *”进行查询
   - 分离业务网络和服务器网络

5. 其他

   - 大表带来的问题（数据量超过千万行，表数据文件巨大，超过10G）

     - 慢查询：很难在一定的时间内过滤出所需要的数据
     - 建立索引需要很长时间，mysql<5.5 会锁表，>5.5引起主从延迟。
     - 修改表结构需要长时间锁表
       - 造成长时间主从延迟
       - 影响正常的数据操作

     解决大表问题

     - 分库分表（很少这么做），难点：分表主键选择，分表后跨分区数据的查询和统计。
     - 大表的历史数据归档，减少对前后断业务的影响。难点：归档时间点的选择，如何进行归档操作

   - 大事务带来的问题

     事务时数据库系统区别于其他一切文件系统的重要特性之一

      

     事务是一组具有原子性的sql语句。原子性、一致性、隔离性（未提交读、已提交读、可重复读，可串行化（很少使用））、持久性。

     

     定义：一个事务必须被视为一个不可分割的最小工作单元，整个事物中的所有操作要么全部提交成功，要么全部失败，对于一个事务来说，不可能只执行其中的一部分操作。

     

     事务的隔离型：隔离性要求一个事务对数据库中数据的修改，在未提交完成前对于其他事务是不可见的。


![image-20190201234408042](./images/image-20190201234408042.png)

未提交读：可看到其他事务的修改。

已提交读：大部分默认隔离级别（除了Mysql），只能看到已提交事务的修改

可重复读：MySQL默认，在事务开始后，可重复读，不管其他事务是否已提交。

可串行化：会在读取的每一行数据上都加锁，基本不用。

```
show varibles like '%iso%';

set session tx_isolation='read-commited'
```



事务的持久性（durability）

定义：一旦事务提交，则其所做的修改就会永久保存到数据库中。此时即使系统崩溃，已经提交的修改数据也不会丢失。



大事务：运行时间比较长，操作的数据比较多的事务

风险

- 锁定太多的数据，造成大量的阻塞和锁超时
- 回滚时所需时间比较长
- 执行时间长，容易造成主从延迟

怎样处理大事务：

- 避免一次处理太多的数据
- 移除不必要在事务中的SELECT操作



目前mysql不支持多cpu并发运算。



## 什么影响了Mysql性能

![image-20191211092617802](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191211092617802.png)



那些因素影响数据库性能：

1. 硬件情况
2. 服务器操作系统，参数等
3. 数据库存储引擎
4. 数据库参数配置
5. 数据库结构设计和SQL语句



### 服务器硬件

#### CPU资源

是需要更好的cpu还是更多的cpu。

1. web项目适合更多的cpu，需要更高的并发量。
2. 使用多个cpu需要选择更好的mysql版本



MySQL不支持多CPU对同一SQL并发处理



**特别注意：**

- 不要在64位使用32位的服务器版本。（检查操作系统位数，使用32位操作系统对系统极大限制）
- 64位的CPU一定要工作在64位的系统下
- 对于并发比较高的场景CPU的数量比频率重要
- 对于CPU密集性场景和复杂SQL则频率越高越好





#### 内存大小

内存极大影响数据库性能。

- 内存并不是越多越好，当内存==磁盘，再增加没有意义
- 对性能影响有限度
- 并不能无限增加性能



根据数据库**热数据**选择内存



特别注意：

- 选择主板所能使用的最高频率的内存，内存频率越高，对性能越好。
- 内存大小对性能很重要，所以尽可能的大



#### 磁盘

##### 传统磁盘

选择因素：存储容量、传输速度、访问时间、主轴转速、物理尺寸

1. 传统磁盘，存储空间大，速度较慢。要选择转速快的。

2. raid技术，磁盘冗余队列的简称。简单来说Raid的作用就是把多个容量较小的磁盘组成一组容量更大的磁盘，并提供数据冗余来保证数据完整性的技术。

   主要的raid级别

   - raid 0 ： 组建磁盘整列最简单的形式，简单来说就是多个读磁盘组合在一起，没有提供冗余或错误修复能力。
   - raid 1：有成为**磁盘镜像**。磁盘利用率仅有50%，成本高。数据冗余性好，读性能较好。大部分操作系统都可以通过软件实现raid 0 和raid1.
   - raid 5 ：分布式**奇偶校验磁盘阵列**。整个整列只需要一块冗余。写很慢，随机读很快，适合以读为主的业务。最好使用在从数据库服务器上。
   - raid 10 ：又称为分片的镜像。读写性能好，重建会很简单。

   ![image-20190202161815719](./images/image-20190202161815719.png)

   raid级别选择：

   ![image-20190202161906635](./images/image-20190202161906635.png)

##### 固态存储

优缺点：

- 相比于机械磁盘，固态磁盘有更好的随机读写性能

- 相比机械磁盘，固态磁盘能更好的支持并发

- 相比机械磁盘，固态磁盘更容易损坏

ssd：使用sata接口，可以替代传统磁盘而不需要任何改动。sata接口的ssd同样支持raid技术。

pci-E ssd：无法使用sata接口，需要独特的驱动和配置。价格比ssd要贵，性能比ssd好。

使用场景

- 适用于存在大量随机i/o的场景，热数据大小远大于内存。
- 适用于解决单线程负载的i/o瓶颈，比如从服务器上（单线程，易损耗）。



##### 网络存储san和nas

是两种外部文件存储设备加载到服务器上的方法。

san设备通过**光纤**连接到服务器，设备通过块接口访问，服务器可以将其当作磁盘使用。

- 适合大量顺序读写，*随机读写慢*，不如本地raid磁盘。

nas设备使用**网络连接**，通过基于文件的协议如nfs或smb来访问，通过有网络传输的延迟。

适合场景：

- 网络存储不适合于mysql数据库存放数据，有人使用网络存储来实现高可用（牺牲性能）—牺牲性能，不易维护。
- 适合数据库备份



特别注意（I/O子系统）

- PCIe -》SSD（可以使用传统接口） -〉Raid10 -》磁盘 -〉SAN 





#### 网络性能限制

延迟 带宽

- 采用高性能和高带宽的网络接口设备和交换机
- 对多个网卡进行绑定，增强可用性和带宽
- 尽可能进行网络隔离



- 网络带宽对性能的影响

- 网络质量对性能的影响



建议：

- 采用高性能和高带宽的网络接口设备和交换机
- 对多个网卡进行绑定，增强可用性和带宽
- 尽可能的进行网络隔离







### 服务器系统

- windows：大小写不敏感，linux大小写敏感，可能在windows能跑，linux下不能跑。
- FreeBSD：老版本不行
- Solaris：稳定性著称
- Linux
- CentOs



在CentOS下哪些参数对Mysql性能有影响：

《Linux性能优化大师》

- 内核相关参数（/etc/sysctl.conf)

  - 网络相关：

    ```
    // 每个端口最大监听队列长度，负载大的一般会修改
    net.core.somaxconn=65535
    net.core.netdev_max_backlog=65535
    net.ipv4.tcp_max_syn_backlog=65535
    
    
    // tcp连接处理的等待状态的时间，用于加快tcp连接回收速度
    net.ipv4.tcp_fin_timeout=10
    net.ipv4.tcp_tw_reuse=1
    net.ipv4.tcp_tw_recycle=1
    
    
    // tcp 连接接收和发送缓冲区大小的默认值和最大值，对于数据库应用应该把这几个值调整的大一点
    net.core.wmem_default=87380
    net.core.wmem_max=16777216
    net.core.rmem_default=87380
    net.core.rmem_max=16777216
    
    
    // 用于减少失效连接所占用的tcp系统资源数量，加快资源回收效率,可以适当改小些
    net.ipv4.tcp_keepalive_time=120
    net.ipv4.tcp_keepalive_intvl=30
    net.ipv4.tcp_keepalive_probes=3
    
    ```

  - 内存相关

    ```
    // linux 内核参数中最重要的参数之一，用于定义单个共享内存段的最大值
    // 注意：
    // 1. 这个参数应该设置的足够大，以便能在一个共享内存段下荣大整个的Innodb缓冲池的大小。
    // 2. 这个值的大小对于64位linux系统，可取的最大值为物理内存值-1byte，建议值为大于物理内存的一半，一般取值大于Innodb缓着冲池即可，可以取物理内存-1byte
    kernel.shmmax=4294967295
    // 这个参数当内存不足时会对性能产生比较明显的影响
    // linux系统内存交换区：在linux系统安装时都会有一个特殊的磁盘分区，称之为系统交换分区，free -m可以查看到
    // 在MySQL服务器上是否使用交换分区有一些争议：在MySQL服务所在的Linux系统上完全禁用交换分区
    // 带来的风险：
    // 1. 降低操作系统的性能
    // 2. 容易造成内存溢出，崩溃，或都被操作系统kill掉
    // 结论：在MySQL服务器上保留交换区还是很必要的，但是要控制何时使用交换分区 
    // 就是告诉Linux内核除非虚拟内存完全满了，否则不要使用交换区
    vm.swappiness=0
    ```

  - 其他配置

    ![image-20191216225731879](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191216225731879.png)

    ![image-20191216230005937](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191216230005937.png)

  - 磁盘性能相关

    ![image-20191216230156417](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191216230156417.png)

    cfq：完全公平队列，适合桌面系统，会在队列中插入不必要请求，响应时间慢

    ![image-20191216230524055](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191216230524055.png)

    ![image-20191216230549968](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191216230549968.png)

    ![image-20191216230615470](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191216230615470.png)

    修改方法：

    ![image-20191216230707999](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191216230707999.png)

文件系统对性能的影响

![image-20191216231336470](/Users/zengxiangfei/Documents/mywiki/database/images/image-20191216231336470.png)





### MySQL体系结构

![image-20191221102058107](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191221102058107.png)

客户端：jdbc，odbc，php等

MySQL服务层：实现与存储引擎无关的所有特性。

存储引擎层

注意：存储引擎是针对于表的而不是针对于库的（一个库中的不同表可以使用不同的存储引擎，不建议使用）



### 数据库存储引擎的选择

#### **插件式存储引擎**

MyISAM（5.58和之前版本）：不支持事务，表级锁

- MyISAM存储引擎表由MYD和MYI组成

  ![image-20191221102510682](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191221102510682.png)

- 特性

  - 并发性与锁级别，使用的是表级锁，而不是行级锁，对读写混合并发性不好
  - 表损坏修复 ,`check table tablename`、`repair table tablename`
  - 支持的索引类型：fullText
  - MyISAM表支持数据压缩
  - 版本MySQL5.0时默认表大小为4G

- 使用场景

  - 非事务型应用
  - 只读类应用
  - 空间类应用：支持空间函数，gprs数据等。



#### InnoDB存储引擎：MySQL5.5及之后版本默认存储引擎，

- Innodb使用表空间进行，数据存储

  ![image-20191221105835345](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191221105835345.png)

  ![image-20191221105936852](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191221105936852.png)

  ![image-20191221110010389](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191221110010389.png)

  ![image-20191221110049548](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191221110049548.png)

  系统表空间和独立表空间要如何选择：

  比较

  - 系统表空间无法简单的收缩文件大小
  - 独立表空间可以通过optimize table命令收缩系统文件
  - 系统表空间会产生IO瓶颈
  - 独立表空间可以同时向多个文件刷新数据

  建议：

  - 对Innodb使用独立表空间

  ![image-20191221110657264](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191221110657264.png)

  系统表空间还存储了什么信息：

  - Innodb数据字典信息，通过b数进行数据管理
  - undo回滚段

#### Innodb存储引擎的特性

- Innodb是一种事务性存储引擎

- 完全支持事务的ACID特性

- Redo Log 和Undo Log

  ![image-20191222225151368](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191222225151368.png)

- Innodb支持行级锁

- 行级锁可以最大程度的支持并发

- 行级锁是由存储引擎层实现的

  什么是锁（数据库系统区别于文件系统的重要特性）：

  - 锁对主要作用是管理共享资源的并发访问
  - 锁用于实现事务的隔离性

  锁的类型

  - 共享锁（也称读锁）

  - 独占锁（也称写锁）

    ![image-20191222230057021](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20191222230057021.png)

  锁的粒度-锁的策略

  - 表级锁
  - 行级锁

  阻塞和死锁：

  - 什么是阻塞：在有些时刻，一个事务中的锁需要等待另一个锁的释放
  - 什么是死锁：是指两个或两个以上的事务在执行中相互占用了对方等待的资源而产生的一种异常。死锁系统会发现并自动处理，少量的死锁不影响系统的运行。

- Innodb状态检查

  `show engine innodb status`

  参考mysql官方手册。

- 适用场景

  - Innodb适合大多数OLTP应用



#### MySql常用存储引擎之CSV

文件系统存储特点

- 数据以文本方式存储在文件中
- .csv文件存储表内容
- .csm文件存储表的元数据如表状态和数据量
- .frm文件存储表结构信息
- 所有列必须都不能为NULL的
- 不支持索引，不支持大表，不适合在线处理
- 可以对数据文件直接编辑

适用场景

- 适合作为数据交换的中间表



#### Mysql常用存储引擎之Archive

文件系统存储特点

- 以zlib对表数据进行压缩，磁盘I/O更少
- 数据存在在ARZ为后缀的文件中，表结构存储在frm中。
- 只支持insert和select操作
- 只允许在自增ID列上加索引



适用场景

- 日志和数据采集类应用



#### MySQL常用存储引擎之Memory

文件系统存储引擎特点

- 也称HEAP存储引擎，所以数据保存在内存中
- 数据易失性，一旦mysql重启，表中数据会丢失，表结构会保留
- 只有一个frm文件

功能特点

- 支持HASH索引（等值查询快，无法进行范围查询）和BTree索引（范围查找快），默认为HASH索引。
- 所有字段都是固定长度varchar(10) = char(10)
- 不支持BLOG和TEXT等大字段
- Memory存储引擎适用表级锁
- 最大大小由max_heap_table_size参数决定，默认16M，修改后需要对表重建



容易混淆的概念：

- Memory存储引擎表
- 临时表
  - 系统临时表
    - 超过限制使用Mysam临时表
    - 未超限制使用Memory表
  - create temporary table 建立的临时表

使用场景

- 用于查找或者是映射表，例如邮编和地区的对应表（hash对于等值查找很高效）
- 用于保存数据分析中产生的中间表
- 用于换成周期性聚合数据的结果表

注意：Memory数据易丢失，所以要求数据可再生



#### MySQL常用存储引擎之Federated

特点

- 提供了访问远程MySQL服务器上表的方法
- 本地不存储数据，数据全部放到原创服务器上
- 本地需要保存表结构和原创服务器的连接信息（存在一个frm文件）

如何使用

- 很少使用，禁止，启用需要在启动时增加federated参数

   ```
  mysql://user_name[:password]@host_name[:port_num]/db_name/tbl_name
  show engines;
  在my.cnf中加入：
  federated=1
  重启mysql服务器
  ```

  ![image-20200203134159881](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200203134159881.png)

- 可以通过复制实现该特性

使用场景

偶尔的统计分析及手工查询。



#### 如何选择存储引擎

大部分情况下，选择innodb。除非万不得已，不要混合使用存储引擎。

参考条件

- 事务
- 备份 innodb支持免费在线热备方案 mysqldump不是在线热备方案
- 奔溃恢复
- 存储引擎的特有特性



#### MySQL服务器参数介绍

MySQL获取配置路径

- 命令行参数

  `mysqld_safe --datadir=/data/sql_data`

- 配置文件

  通过以下命令获取mysql读取配置文件的顺序。

  ![image-20200314084440957](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200314084440957.png)

Mysql配置参数作用域：

- 全局参数(可能需要重新登录才能生效)

  - set global 参数名=参数值;
  - set @@global.参数名  :=参数值;

- 会话参数

  set [session] 参数名=参数值

  set @@session.参数名:=参数值;

mysql5.7之后有部分参数支持修改全局参数后不需要重新登录就可以生效。



### 数据库参数配置（重要）

#### 内存相关参数

- 确定可以使用的内存的上限，如果32位操作系统（Mysql单进程）就只能使用3G以内内存

- 确定Mysql的每个连接使用的内存

  sort_buffer_size：在查询需要排序时，会给所有连接分配该指定内存。分配时要小心，不宜过大。

  join_buffer_size：连接缓冲，当多表查询时会分配多个

  read_buffer_size:4K倍数 有查询需要时

  read_rnd_buffer_size

- 确定需要为操作系统保留多少内存

- 如何为缓存池分配内存

  - Innodb_buffer_pool_size ：缓存索引，数据等数据，延迟写入。必须保证足够的内存。

    总内存-（每个线程所需要的内存*连接数）-系统保留内存

    （MySQL手册建议的时服务器内存的75%）

  - key_buffer_size:主要用于myisam，myisam缓存池只缓存索引，数据依赖于操作系统的缓存。Mysql的系统表还在使用myisam存储引擎，所以还是需要给key_buffer_size分配一定的空间。【

    ![image-20200314103659184](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200314103659184.png)

    

#### I/O相关配置参数

##### InnoDB I/O相关配置

- Innodb_log_file_size 控制了单个事务日志的大小 。跟业务情况相关，如果业务非常繁忙，设置大一些有好处。大小应为记录服务器一个小时左右的事务信息。

- Innodb_log_files_in_group 控制着事务日志文件的个数

- 事务日志的总大小=Innodb_log_file_size*Innodb_log_files_in_group

- Innodb_log_buffer_size :   能够保留1秒钟的事务就足够了。 32M～128M

- Innodb_flush_log_at_trx_commit : 刷新事务日志的频繁程度

  设置为2时mysql进程奔溃不会丢失数据，但是在整个服务器宕机可能会丢失1秒数据。

  ![image-20200314145337744](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200314145337744.png)

![image-20200314145900954](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200314145900954.png)



##### MyISAM I/O相关配置

- delay_key_write

  ![image-20200314150146672](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200314150146672.png)



#### 安全相关配置

- expire_logs_days 指定自动清理binlog的天数

- max_allowed_packet 控制mysql可以接收包的大小 32M 主从复制，主从这个参数要保持一致。

- skip_name_resolve 禁用DNS查找

- sys date_is_now 确定sys date()返回确定性日期，和now()结果一致。 建议增加

- read_only 禁止非super权限的用户写权限 建议在从库中启用。在给用户授权时注意，不要给予super权限。

- skip_slave_start 禁用slave自动恢复 用在从库上，阻止mysql在重启后试图自动的启动复制。

- sql_mode 设置MySQL所使用的SQL模式

  ![image-20200314162522117](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200314162522117.png)

#### 其他常用配置参数

- sync_binlog 控制MySQL如何向磁盘刷新binlog 默认为0，表示默认由操作系统控制 主从复制的主DB来说，建议设置为1，避免由于主DB奔溃而造成cache中的日志没有同步到二进制文件中。
- tmp_table_size 和 max_heap_table_size 控制内存临时表大小（memory存储引擎）
- max_connections 控制允许的最大连接数 默认值只有100，同城情况下会设置为2000或者稍微更大些。





### 数据库结构设计和SQL语句（影响最大）

数据库设计对性能的影响

- 过分的反范式化为表建立太多的列
- 过分的范式化造成太多的表关联（最多关联61个表），关联表应控制在10个以内
- OLTP环境中使用不恰当的分区表
- 使用外键保证数据的完整性（强烈建议不要使用外键约束）



性能优化顺序

- 数据库结构设计和SQL语句
- 数据库存储引擎的选择和参数配置
- 系统选择及优化
- 硬件升级



## MySQL基准测试

测量系统的性能 判断优化是否有效

基准测试：基准测试是一种测量和评估软件性能指标的活动用于建立某个时刻的性能基准，以便当系统发生软硬件变化时重新进行基准测试以评估变化对性能的影响。

基准测试是针对系统设置的一种压力测试。

基准测试：直接、简单、易于比较、用于评估服务器的处理能力。简化了的压力测试。

压力测试：对真实的业务数据进行测试，获得真实系统所承受的压力。



### 如何进行基准测试

基准测试的目的：

- 建立MySQL服务器的性能基准线，确定当前MySQL服务器运行情况。

- 模拟比当前系统更高的负载，以找出系统扩展瓶颈

  增加数据库并发、观察QPS、TPS变化，确定并发量与性能最优关系

- 测试不同的硬件、软件和操作系统配置

- 证明新的硬件设备是否配置正确。



对整个系统进行基准测试

从系统入口进行测试（如网站Web前端、手机App前端）

优点：

- 能够测试整个系统的性能，包括Web服务器缓存、数据库等。
- 能够反映出系统中各个组件接口间的性能问题体现真实性能状况。

缺点

- 测试设计复杂，消耗时间长



单独对MySQL进行基准测试

优点：

- 测试设计简单，所需耗费时间短

缺点：

- 无法全面了解整个系统的性能基线



指标

- 单位时间内所处理的事务数（TPS）

- 单位时间内所处理的查询数（QPS）

- 响应时间

  平均响应时间、最小响应时间、最大响应时间、各个时间所占百分比

- 并发量：同时处理的查询请求的数量（并发量并不等于连接数），指的是正在工作中的并发的操作数或同时工作的数量。



### 基准测试演示实例

计划和设计基准测试

- 对整个系统还是某一组件

- 使用什么样的数据

- 准备基准测试及数据收集脚本

  CPU使用率、IO、网络流量、状态与计数器信息 等

  Get_Test_info.sh 

  ![image-20200314174531475](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200314174531475.png)



- 运行基准测试

- 保存和分析基准测试结果

  analyze.sh

  ![image-20200314183753638](/Users/zengxiangfei/Documents/mywiki/Database/images/image-20200314183753638.png)



基准测试容易忽略的问题：

- 使用生产环境数据时只使用了部分数据
- 在多用户场景中，只做单用户的测试 。推荐多线程并发测试。
- 在单服务器上测试分布式应用 **推荐：**使用相同的架构进行测试。
- 反复执行统一查询



### Mysql基准测试工具

ab，httploader，对系统进行整体测试。

MySQL基准测试工具之 mysqlslap，mysql服务器自带，无需安装。

特点：

- 可以模拟服务器负载，并输出相关统计信息。
- 可以指定也可以自动生成查询语句。



常用参数说明：

- `--auto-generate-sql`由系统自动生成SQL脚本进行测试
- `--auto-generate-sql-add-autoincrement`在生成的表中增加自增ID
- `--auto-generate-sql-load-type`指定测试中使用的查询类型 默认使用混合（读写删除查询更新）
- `--auto-generate-sql-write-number`指定初始化数据生成的数据量。
- `--concurrency`指定并发线程数量。
- `--engine`指定要测试表的存储引擎，可以用逗号分隔多个存储引擎。
- `--no-drop`指定不清理测试数据
- `--iterations`指定测试运行的次数。每次测试都会重新生成测试数据，指定了这个参数，就不能指定no-drop参数。
- `--number-of-queries`指定每一个线程执行的查询数量
- `--debug-info` 指定输出额外的内存及CPU统计信息
- `--number-int-cols`指定测试表中包含INT类型列的数量
- `--number-char-cols`指定测试表中包含的varchar类型的数量
- `--create-schema`指定了用于执行测试的数据库的名字
- `--query`用于指定自定义SQL的脚步
- `--only-print`并不允许测试脚本，而是把生成的脚本打印出来





## MySQL数据库结构优化





## MySQL高可用架构设计





## 数据库索引优化





## SQL查询优化





## 数据库分库分表





## 数据库监控









