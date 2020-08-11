# GC日志相关

> -XX:+PrintGC 输出GC日志 
>  -XX:+PrintGCDetails 输出GC的详细日志
>  -XX:+PrintGCTimeStamps 输出GC的时间戳（以基准时间的形式）
>  -XX:+PrintGCDateStamps 输出GC的时间戳（以日期的形式，如 2013-05-04T21:53:59.234+0800）
>  -XX:+PrintHeapAtGC 在进行GC的前后打印出堆的信息
>  -Xloggc:../logs/gc.log 日志文件的输出路径



GC日志分析工具：

[https://github.com/chewiebug/GCViewer](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fchewiebug%2FGCViewer)







## 避免使用 -XX : +UseGCLogFileRotation 

参考链接：https://dzone.com/articles/try-to-avoid-xxusegclogfilerotation



开发者通过 JVM 参数 `-XX:+UseGCLogFileRotation` 实现 GC 日志轮转。

像下面这样：

```shell
"-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:/home/GCEASY/gc.log -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=20M"
```

指定上述参数，当日志文件大小增加到 20MB，JVM 会进行 GC 日志轮转生成最多5个文件，扩展名分别为 `gc.log.0`、`gc.log.1`、`gc.log.2`、`gc.log.3` 和 `gc.log.4`。

**这种做法会带来以下问题：**

**1. 丢失旧的 GC 日志**

如果设置参数 `-XX:NumberOfGCLogFiles=5`，一段时间过后会创建5个 GC 日志文件：

- gc.log.0 ← `最老的 GC 日志内容`
- gc.log.1
- gc.log.2
- gc.log.3
- gc.log.4 ← `最新的 GC 日志内容`

最新的 GC 日志写入 `gc.log.4`，而过去的 GC 日志内容存入 `gc.log.0`。

使用 `-XX:NumberOfGCLogFiles` 配置时，如果应用不断产生更多的 GC 日志，`gc.log.0` 中的旧日志内容会被删除，新产生的 GC 事件将写入 `gc.log.0`。这意味着日志内容完整性早到破坏，即无法看到所有 GC 事件。

**2. GC 日志混合**

假设某个应用创建了5个 GC 日志文件：

- gc.log.0
- gc.log.1
- gc.log.2
- gc.log.3
- gc.log.4

接着，如果重启这个应用，现在新 GC 日志会写入 `gc.log.0`。重启前的日志信息保存在 `gc.log.1`, `gc.log.2`, `gc.log.3`, `gc.log.4` 中。

- gc.log.0 ← 重启后的 GC 日志文件内容
- gc.log.1 ← 重启前的 GC 日志文件内容
- gc.log.2 ← 重启前的 GC 日志文件内容
- gc.log.3 ← 重启前的 GC 日志文件内容
- gc.log.4 ← 重启前的 GC 日志文件内容

因此，重启后的 GC 新日志与旧日志混在一起。要解决这个问题，可以在重启应用前把所有旧日志移动到一个独立的文件夹中。

**3. 将 GC 日志转发到中央存储**

采用这种方法，当前写入的日志文件会标记 `.current` 扩展，例如当前写入 `gc.log.3`，会命名为 `gc.log.3.current`。

如果要把 GC 日志从单个服务器汇总到中央存储，大多数 DevOps 工程师会使用 `rsyslog`。然而，正如[这篇博客][1]讨论的那样，这种命名方式给 `rsyslog` 带来了巨大挑战。

[1]: http://www.planetcobalt.net/sdb/forward_gc_logs.shtml

**4. 工具**

现在，有许多像 [GCeasy][2]、GCViewer 这样的工具可以用来分析 GC 日志，支持上传多个日志文件。

[2]: https://gceasy.io/



**5. 推荐的解决方案**

当 JVM 重启后可以为 GC 日志加上时间戳作为后缀，这样 GC 日志文件路径能够保持唯一。这样，新日志就不会覆盖掉旧的 GC 日志。通过为 GC 日志文件指定 `%t` 后缀可以实现，像下面这样：

```
"-XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:/home/GCEASY/gc-%t.log"
```

`%t` 后缀格式生成的时间戳格式为 `YYYY-MM-DD_HH-MM-SS`。因此，生成的日志文件名看起来像这样 `gc-2019-01-29_20-41-47.log`。

这种简单的方法解决了 `-XX:+UseGCLogFileRotation` 参数的所有缺点。

