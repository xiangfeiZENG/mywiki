# Redis故障排查

Jedis报错：

```
JedisPool "Could not return the resource to the pool" exception

```

从这个角度：

https://github.com/xetorthio/jedis/issues/885

```
resource leak happened in my code not in the library,

i was getting a jedis handler from the jedispool, which is leaked.
As i shared the jedispool among multiple threads, the jedis handler i made it as global variable.

When multiple threads are getting resource at same time using global jedis handler variable, the resource is leaking.
```





redis集群开始不正常，重新设置client-output-buffer-limit，设置成0 0 0 ，redis主从可以正常进行。但是master一直在切换。

报错：

```
Error writing to client: Connection reset by peer
```

这个报错原因是什么？

https://github.com/xetorthio/jedis/issues/906

```
when write 50w data(something like:a hashmap includes 50w row) to redis server,it works.
but when i write 55w data to redis server,this exception has been throwed.
at first,i think it's caused by timeout,but i set maxtimewait from 1000*1000 to -1, it also doesn't work.
then i think there is not enough memery,but i change env to my local, my local mem is 8g,and redisconf's [maxmemory] is default not be set,so i think the problom is not here,
at last,i have changed env to my local,and it also doesn't work,so i think the reason is not on server,it maybe client's problem,i really can't not understand why it doesn't work.help!

jedis version is:2.2.1,(2.6.0 also)
redis info:
```





https://github.com/xetorthio/jedis/issues/1752



sensenl错误：

```
next failover delay i will not start a failover before
```

```
sdown master mymaster   next failover delay i will not start a failover before
```



设置



https://forum.il2sturmovik.com/topic/27136-connection-to-master-server/

https://github.com/antirez/redis/issues/1650

JedisPool "Could not return the resource to the pool" exception #885

https://github.com/xetorthio/jedis/issues/885

sentinel waits long time before failover #2235

https://github.com/antirez/redis/issues/2235

redis Error writing to client: Connection reset by peer









