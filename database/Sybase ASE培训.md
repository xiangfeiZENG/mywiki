# Sybase ASE培训

标签（空格分隔）： Sybase

[TOC]

---

讲师：禹老师

主表 附表数据一致。

Transact-SQL
Manage transaction
游标

## 



## Transact-SQL(T-SQL)

It include Standard SQL commands, is a Sybase extensions to SQL commands.

Clustered indexs & Nonclustered indexs.

DML Extensions - select into 性能好。

## System Procedures

sp_help + tablename
sp_helpdb
sp_who
sp_syntax  (like linux man)


测试环境试下。



## 注意

1. Sybase 只有一个进程，不像Oracle，可能有多个进程。
2. Sybase 一个 Server，一个端口，里面有多个数据库。每个数据库拥有自己的用户。
3. union all 不用检查重复，union 需要检查重复，union all的性能比union要好。
4. 


















