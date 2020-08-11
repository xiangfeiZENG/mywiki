# SYBASE 日常维护

标签（空格分隔）： Sybase

[TOC]

---

## Sybase isql

登陆：
> isql -Usa(用户名) -P(密码) -SABC(服务名)

执行数据库脚本：
```
isql -Usa -P -SABC -i /opt/sybase/test.sql -o /opt/sybase/test.log
isql –Usa –Ppasswd –Sservername –i bcpout.sh –o bcpout.txt
```

isql参数详解
```
usage: isql [-b] [-e] [-F] [-p] [-n] [-v] [-X] [-Y] [-Q]
[-a display_charset] [-A packet_size] [-c cmdend] [-D database]
[-E editor [-h header [-H hostname [-i inputfile]
[-I interfaces_file] [-J client_charset] [-K keytab_file]
[-l login_timeout] [-m errorlevel] [-M labelname labelvalue]
[-o outputfile] [-P password] [-R remote_server_principal]
[-s col_separator] [-S server_name] [-t timeout] [-U username]
[-V [security_options]] [-w column_width] [-z localename]
[-Z security_mechanism]
```


## 常用命令
### 服务启动与关闭
关闭sybase主服务 shutdown with nowait go
关闭sybase某一服务 shutdown SYB_BACKUP(服务名) go
启动Sybase：startserver -f RUN_crmserver 

    查看服务名 sp_helpserver go
    
查看sybase版本 select @@version go

### 设备管理
查看sybase的数据设备信息 sp_helpdevice/select *from master..sysdevices go
#### (1)创建
```
use master go
disk init
name = 'test',physname='/opt/sybase/data/test.dat',vdevno=2,size='1024m',vstart=0,cntrltype=0,dsync=true
go
```
#### (2)删除
```
use master go
exec sp_dropdevice 'test'
go
```
#### (3)修改最大的虚拟设备号
````
sp_configure 'number of devices',25 go
```

### 数据库管理
#### (1)创建
```
use master go
create database test on test='1024M' go
use test go
exec sp_changedbowner 'sa' go
```
#### (2)查看当前数据库 
```
select db_name() 
go
```
#### (3)查看数据库信息 sp_helpdb syk go
#### (4)删除 drop database syk go
删除数据库出现：
```
1> drop database vggdb   
2> go
Msg 3702, Level 16, State 4:
Server 'ggserver', Line 1:
Cannot drop the database 'vggdb' because it is currently in use.
```
解决方法:
kill 掉所有的连接进程，问题解决。
在session比较多的情况下，解决方式如下：
```
A session connected to another database might have an open transaction that also affects your database - sp_who2 will only show one database. It could also be something as simple as Object Explorer or Object Explorer Details open in SSMS, which again would only show one database in sp_who2.

Don't bother trying to find the session that is responsible; just kill them all with one statement (and make sure it isn't your copy of SSMS that is connected, e.g. another query window, Object Explorer, etc.):

USE master;
GO
ALTER DATABASE dbname SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
GO
Now you will be able to drop it, and do that using DDL, not the UI:

DROP DATABASE dbname;
```


#### (5)空间使用情况
```
use syk go
sp_spaceused go
```

#### (6) 默认排序方式、字符集等信息 
sp_helpsort   

9. 查看用户信息 sp_helpuser
10. 用户锁定操作 sp_locklogin /sp_locklogin username,’lock/unlock’
11. 查看登录用户 sp_displaylogin [loginname]/sp_who
12. bcp
bcp dbname..tablename out /opt/sybase/test.bcp –Usa –P –Sservername –c ————数据备份
bcp dbname..tablename in /opt/sybase/test.bcp –Usa –P –Sservername –c ————数据还原
一次性导出所有表
(1)建立导出脚本文件(bcpout.sh):
use test go
select ‘bcp test..’+name+’out /opt/sybase/test.txt’+’-Usa –P –Sservername -c’from sysobjects where type=’U’U表示为用户表。
13. 列出所有库 sp_helpdb
14. 查看表信息 sp_help [tablename]
15. 数据库死进程 select * from master..syslogshold



## SYBASE ASE 事务日志（transaction log）管理

SYBASE ASE 的每一个数据库，无论是系统数据库（master,model,sybsystemprocs,tempdb），还是用户数据库，都有自己的transaction log，每个库都有syslogs表。Log记录用户对数据库修改的操作，所以如果不用命令清除， log会一直增长直至占满空间。清除log可用dump transaction 命令; 或者开放数据库选项trunc log on chkpt，数据库会每隔一段间隔自
动清除log。管理好数据库log 是用户操作数据库必须考虑的一面。
下面就几个方面谈谈log 及其管理:

### ASE 如何记录及读取日志信息

我们知道，ASE 是先记log 的机制。Server Cache Memory 中日志页总是先写于数据页:
Log pages 在commit ,checkpoint,space needed 时写入硬盘。
Data pages 在checkpoint,space needed 时写入硬盘。
系统在recovery 时读每个database 的syslogs 表的信息，回退未完成的事务(transaction)
（数据改变到事务前状态）; 完成已提交的事务(transaction) （数据改变为事务提交后的状态）。
在Log 中记下checkpoint 点。这样保证整个数据库系统的一致性和完整性。

### Transaction logs 和 checkpoint 进程

checkpoint 命令的功能是强制所有“脏”页（自上次写入数据库设备后被更新过的页）
写入数据库设备。自动的checkpoint 间隔是由ASE 根据系统活动和系统表sysconfigures 中
的恢复间隔（recovery interval）值计算出的。通过指定系统恢复所需的时间总量，恢复间隔
决定了checkpoint 的频率。
如果数据库开放trunc log on chkpt 选项，则ASE 在数据库系统执行checkpoint 时自动
清除log。但用户自己写入执行的checkpoint 命令并不清除log，即使trunc log on chkpt 选项
开放。只有在trunc log on chkpt 选项开放时，ASE 自动执行checkpoint 动作，才能自动清
除log 。这个自动的checkpoint 动作在ASE 中的进程叫做checkpoint 进程。当trunc log on
chkpt 选项开放时，checkpoint 进程每隔60 秒左右清除log，而不考虑recovery interval 设置
时间的间隔。

### Transaction log 的大小

没有一个十分严格的和确切的方法来确定一个数据库的log应该给多大空间。对一个新
建的数据库来说，log大小为整个数据库大小的20%左右。因为log记录对数据库的修改，如
果修改的动作频繁，则log 的增长十分迅速。所以说log 空间大小依赖于用户是如何使
用数据库的。 例如:
update,insert 和delete 的频率每个transaction 中数据的修改量ASE 系统参数recovery interval值log是否存到介质上用于数据库恢复还有其它因素影响log大小，我们应该根据操作估计log大小，并间隔一个周期就对log
进行备份和清除。

### 检测log 的大小

若log 在自己的设备上，dbcc checktable (syslogs) 有如下信息:
例:***NOTICE:space used on the log segment is 12.87Mbytes,64.35%
***NOTICE:space free on the log segment is 7.13Mbytes,35.65%
根据log 剩余空间比例来决定是否使用dump transaction 命令来备份和清除log。
用快速方法来判断transaction log 满的程度。
```
1>use database_name
2>go
1>select data_pgs (8,doampg)
2>from sysindexes where id=8
3>go
Note:this query may be off by as many as 16 pages.
```
在syslogs 表用sp_spaceused 命令。

### log 设备

一般来说，应该将一个数据库的data和log存放在不同的数据库设备上。这样做的好处:可以单独地备份(back up)transaction log
防止数据库溢满可以看到log 空间的使用情况。[dbcc checktable (syslogs)]可以镜像log 设备

### log 的清除

数据库的log 是不断增长的，必须在它占满空间之前清除。前面已经讨论过，清除log可以开放数据库选项trunc log on chkpt，使数据库系统每隔一段时间间隔自动清除log，还可以执行命令dump transaction 来清除log.“trunc log on chkpt” 选项同“dump transaction with truncate_only” 命令一样，只是清除log 而不保留log 到备份设备上。所以如果只想清除log 而不做备份，可以使用trunc log on chkpt 选项及dump transaction with truncate_only,dump transaction
with no_log 命令。若想备份，应做dump transaction database_name to dumpdevice。

在命令参考手册中的dump transaction with no_log 条目下，有一条警告信息告诉你，你应该把这条命令作为没有其它办法时的最后一招才使用它。但是“最后一招”究竟是什么意
思呢?当你使用这条命令时会怎样呢?那你应使用哪条命令来代替它呢?最后，若这条命令如此有问题，为什么Sybase 却要提供它呢?
   
Sybase技术支持建议你定期的dump你的transaction log。你必须根据你的数据库中记入日志的活动量的大小以及你的数据库的大小来决定dump 的方式。有些地方按月dumptransaction; 有些地方每夜dump transaction。

若你从未做过dump transaction，transaction log将最终会满。 SQLServer使用log（日志）
是出于恢复目的的。 当log满时，服务器将停止事物的继续进行，因为服务器将不能将这些
事物写进日志，而服务器不能运行大多数的dump tran 命令，因为ASE也需在日志中记录这
些命令。

这就是为什么当其它dumptran命令不能执行时no_log可执行的原因。但是想一下dump transaction with no_log被设计执行的环境，将不做并发性检查。若你在对数据库的修改发生时使用dump transaction with no_log，你就会冒整个数据库
崩溃的风险。在多数情况下，它们被反映成813 或605 错误。为了在数据库被修改时，删除transaction log中的不活跃部分可使用dump transaction with truncate_only。这条命令写进transaction
log 时，并且它还做必要的并发性检查。这两条命令都有与其相关的警告，在命令参考手册中会看到这些警告。请确保在使用其中任一条命令以前，你已理解这些警告和指示。
    
Sybase 提供dump transaction with no_log来处理某些非常紧迫的情况。为了尽量确保你的数据库的一致性，你应将其作为“最后一招”。

### 管理大的transactions

有些操作是大批量地修改数据，log 增长速度十分快，如:
大量数据修改
删除一个表的所有记录
基于子查询的数据插入
批量数据拷贝
下面讲述怎样使用这些transaction 使log 不至溢满:
大量数据修改 例 :
```
1>update large_tab set col_1=0
2>go
```
若这个表很大，则此update动作在未完成之前就可能使log满，引起1105错误（log full）
而且执行这种大的transaction 所产生的exclusive table lock，阻止其他用户在update 期间修
改这个表，这可能引起死锁。为避免这些情况，我们可以把这个大的transaction 分成几个小
的transactions，并执行dump transaction 动作。
上述例子可以分成两个或多个小transactions.
例如:
```
1>update large_tab set col1=0
2>where col2<x
3>go
1>dump transaction database_name with truncate_only
2>go
1>update large_tab set col1=0
2>where col2>=x
3>go
1>dump transaction database_name with truncate_only
2>go
```
若这个transaction 需要备份到介质上，则不用with truncate_only 选项。若执 行dump
transaction with truncate_only，应该先做dump database 命令。
删除一个表的所有记录:
例:
```
1>delete table large_tab
2>go
```
同样，把整个table 的记录都删除，要记很多log，我们可以用truncate table 命令代替上
述语句完成相同功能。
```
1>truncate table large_tab
2>go
```
这样，表中记录都删除了，而使用truncate table 命令，log 只记录空间回收情况，而不
是记录删除表中每一行的操作。
基于子查询的数据插入
例:
```
1>insert new_tab select col1,col2 from large_tab
2>go
```
同样的方法，对这个大的transaction，我们应该处理为几个小的transactions。
```
1>Insert new_tab
2>select col1,col2 from large_tab where col1<=y
3>go
1>dump transaction database_name with truncate_only
2>go
1>insert new_tab
2>select col1,col2 from large_tab where col1>y
3>go
1>dump database database_name with truncate_only
2>go
```
同样，若想保存log 到介质上，则dump transaction 后不加with truncate_only 选项。若
执行dump transaction with truncate_only，应该先做dump database 动作。
批量数据拷贝
在使用bcp把数据拷入数据库时，我们可以把这个大的transaction变成几个小的transactions
处理，避免log 剧增。
开放trunc log on chkpt 选项
```
1>use master
2>go
1>sp_dboption database_name,trunc,true
2>go
1>use database_name
2>go
1>checkpoint
2>go
bcp... -b 100 (on unix)
bcp... /batch_size=100(on vms)
```
关闭trunc log on chkpt 选项，并dump database。
在这个例子中，一个批执行100 行拷贝。也可以将bcp 输入文件分成两或多个分开的文
件，在每个文件执行后做dump transaction 来避免log 满。
若bcp 使用快速方式（无索引，无triggers），这样操作不记log，换句话说，log 只记载
空间分配情况。在这种情况下，要先做dump database（为恢复数据库用）。若log 太小，可
置trunc log on chkpt 选项，这样在每次checkpoint 后清除log。


## 快速bcp in
设置方法如下：
```
use master
go 
execute sp_dboption yourdbname, "select into/bulkcopy", true
go
use yourdbname
go
checkpoint
go
```
设置截断日志方法：
```
use master
go 
execute sp_dboption "yourdbname","trunc.",truego
use yourdbname
go
checkpoint
go
```

## 常见问题汇总
### 修改Sybase字符集
操作步骤：
```
cd $SYBASE/charsets/cp936
charset -Usa -Ppassword -Sserver binary.srt cp936

isql -Usa -Ppassword -Sserver
1> select id,name from syscharsets
2> go

1> sp_configure "default character set id",171
2> go
Msg 5824, Level 16, State 4:
Server 'ggserver', Procedure 'sp_configure', Line 1321:
Cannot reconfigure server to use sort order ID 45, because the row for its
underlying character set (ID 171) does not exist in syscharsets.
(1 row affected)
(return status = 1)
```
如上，报错了。

解决过程：
```
sp_configure 'default sortorder id'
```
发现改值已被改为45
于是修改：
```
1> sp_configure 'default sortorder id',50
2> go
Msg 5824, Level 16, State 4:
Server 'ggserver', Procedure 'sp_configure', Line 1321:
Cannot reconfigure server to use sort order ID 50, because the row for its
underlying character set (ID 190) does not exist in syscharsets.
(1 row affected)
(return status = 1)
```
修改是发现报错：
1> sp_configure 'default sortorder id',50,'cp936'
2> go

这样修改成功了

然后执行：
```
1> sp_configure "default character set id",171
2> go
```
完成修改。




### 导出表结果
脚本内容
```
#exportTable.sh

export SYBROOT=/syb64bit
export SYBASE_JRE=/syb64bit/shared/jre142_015
>table.ddl
cat table.list|while read tab
do
ddlgen -Usa -P -SSYB_SERV -Duserdb -TU -N${tab} -O tmp.ddl
cat tmp.ddl|sed '/^create/,/^go/!d' >>table.ddl
done
rm tmp.ddl
```
ddlgen的其他用法:
1、存储过程
`ddlgen -Usa -P -SSYB_SERV -Duserdb -TP -N% -O proc.ddl`
2、数据库
`ddlgen -Usa -P -SSYB_SERV -Duserdb -TDB -Nuserdb -O db.ddl`
3、表结构
`ddlgen -Usa -P -SSYB_SERV -Duserdb -TU -N% -O table.ddl`
4、视图
`ddlgen -Usa -P -SSYB_SERV -Duserdb -TV -N% -O view.ddl`



日志满分为两种情况：1.非master(用户库或者其他系统库sybsystemprocs)库的日志满，导致用户库不能访问；2.系统库master日志满，导致服务都不能正常启动。针对以上情况要分别对待处理。
1.非master库日志满的处理：
重庆数据库后台sybsystemprocs库日志报满：09:00000:01631:2011/11/13 06:45:36.16 server  Error: 3475, Severity: 21, State: 7
09:00000:01631:2011/11/13 06:45:36.16 server  There is no space available in SYSLOGS to log a record for which space has been reserved. This process will retry at intervals of one minute.
11:00000:04104:2011/11/13 06:20:11.87 server  Space available in the log segment has fallen critically low in database 'sybsystemprocs'.  All future modifications to this database will be suspended until the log is successfully dumped and space becomes available.
00:00000:02407:2011/11/13 06:20:11.89 server  Error: 2812, Severity: 16, State: 5
00:00000:02407:2011/11/13 06:20:11.89 server  Stored procedure 'sp_thresholdaction' not found. Specify owner.objectname or use sp_help to check whether the object exists (sp_help may produce lots of output).
或某个用户库etoh2_cq的日志空间满报：
00:00000:00001:2008/12/02 17:28:07.38 server  Error: 1105, Severity: 17, State: 3
00:00000:00001:2008/12/02 17:28:07.38 server  Can't allocate space for object 'syslogs' in database 'etoh2_cq' because 'logsegment' segment is full/has no free extents. If you ran out of space in syslogs, dump the transaction log. Otherwise, use ALTER DATABASE or sp_extendsegment to increase size of the segment.
00:00000:00001:2008/12/02 17:28:07.38 server  Error: 3475, Severity: 21, State: 7
00:00000:00001:2008/12/02 17:28:07.38 server  There is no space available in SYSLOGS for process 1 to log a record for which space has been reserved. This process will retry at intervals of one minute. The internal error number is -4.
处理方法步骤：
1、马上增加日志设备，并把设备分配给数据库。
alter database name log on 设备名=200;--设备名泛指日志设备，如果设备文件还没在磁盘上初始化，则先需要disk init初始化设备文件，
--如果是系统库，可以考虑先不加设备。
Go
2、把数据库置于-32768状态，再把日志清除。
-- -32768这个状态值根据实际情况作出调整.
--各状态值的含义详见我的blog:Sybase ASE12.5数据库状态值的含义。
--http://blog.csdn.net/xujinyang/article/details/6968650
sp_configure "allow update",1
go
update master..sysdatabases set status=-32768 where name='数据库名'
go
shutdown with nowait  --(注意：可能会延长启动SYBASE数据库时间，若确认没有用户在用，请使用shutdown with wait)
go
--在sybase install目录下
startserver -f RUN_etoh
--启动完成
isql -Usa -P -Sservername
1>dump tran database_name with no_log
2>go
3>update master..sysdatabases set status=0 where name='数据库名'
4>go
5>sp_configure "allow update",0
6>go
7>shutdown with nowait
8>go
重启SYBASE后，日志满报错数据库正常了。

2.master库日志满，已经导致数据库服务都不正常启动情况下
日志如下：
Recovering database 'master' 
00:00000:00001:2004/03/12 10:05:27.70 server Redo pass of recovery has processed 10 committed and 0 aborted transactions. 
00:00000:00001:2004/03/12 10:05:27.81 server No such message: 1105 
00:00000:00001:2004/03/12 10:05:27.81 server There is no space available in SYSLOGS for process 1 to log a record for which space has been reserved. This process will retry at intervals of one minute. The internal error number is -4. 
处理方法步骤：
1、在启动文件中加-T3607 -m,然后用sa进 
dump tran master with no_log或 
alter database master on 设备名=xxxM 即可 --设备名泛指master库设备
如还遇到以下问题： 
启动报：Error: 3475, Severity: 21, State: 7 
00:00000:00006:2004/03/12 10:38:55.71 server There is no space available in SYSLOGS for process 6 to log a record for which space has been reserved. This process will retry at intervals of one minute. The internal error number is -4. 
用sa登陆进不去：报： 
Can't allocate space for object 'syslogs' in database 'master' because 
'logsegment' segment is full/has no free extents. If you ran out of space in 
syslogs, dump the transaction log. Otherwise, use ALTER DATABASE or 
sp_extendsegment to increase size of the segment. 
Msg 3475, Level 21, State 7: 
There is no space available in SYSLOGS for process 6 to log a record for which 
space has been reserved. This process will retry at intervals of one minute. The 

internal error number is -4. 
CT-LIBRARY error: 
ct_connect(): user api layer: internal Client Library error: Read from t 
he server has timed out. 

2、 再加 -T699(Turn off transaction logging for the entire SQL dataserver) 
这次sa可进 
执行 
alter database master on master_dev=xxxM 报和上面一样的错 
dump tran master with no_log也不行 

3、 再加-T7409 
执行 alter database master on master_dev=xxxM ok 
问题解决

--其中 http://manuals.sybase.com/onlinebooks/group-as/asg1250e/svrtsg/ 是针对sybase ASE 12.5很多出错问题的处理指南
顶
1
踩