# MySQL主从复制及高可用

MySQL5.7开始主从复制有两种方式：基于日志（binlog）、基于GTID（全局事务标示符）。

**优缺点比较**：

基于日志点复制配置步骤

> 优点：
>
> - 是MySQL最早支持的复制技术，Bug相对较少
> - 对SQL查询没有任何限制
> - 故障处理比较容易
>
> 缺点：
>
> - 故障转移时重新获取新主的日志点信息比较困难

基于GTID复制的优缺点

> 优点：
>
> - 可以很方便的进行故障转移
> - 从库不会丢失主库上的任何修改
>
> 缺点：
>
> - 故障处理比较复杂
> - 对执行的SQL有一定的限制





