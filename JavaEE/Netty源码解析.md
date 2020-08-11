

# Netty源码解析

[TOC]

使用netty的开源软件：

- Dubbo
- RocketMQ
- Spark
- Elasticsearch
- Cassandra
- Flink
- Netty-SocketIO
- spring5：http协议框架
- Play
- Grpc



Netty是什么

- 异步事件驱动框架，用于快速开发高性能服务端和客户端
- 封装了JDK底层BIO和NIO模型，提供高度可用的API
- 自带编解码器解决拆包粘包问题，用户只用关心业务逻辑
- 精心设计的reactor线程模型支持高并发海量连接
- 自带各种协议栈让你处理任何一种通用协议都几乎不用亲自动手 



reactor线程模型，设计模式，优秀代码设计。





## Netty基本组件



![image-20191116082828889](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116082828889.png)





### NioEventLoop

对应Thread

发动机，启动了两种类型的线程，启动了监听客户端连接，处理客户端的读写



### Channel

对应socket



对一些连接的封装

### ByteBuf

IO Bytes



### Pipeline

logic chain



数据的读写可以看出逻辑的处理链



### ChannelHandler

logic





## Netty服务端启动

```java
public final class Server {

    public static void main(String[] args) throws Exception {
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .childOption(ChannelOption.TCP_NODELAY, true)
                    .childAttr(AttributeKey.newInstance("childAttr"), "childAttrValue")
                    .handler(new ServerHandler())
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        public void initChannel(SocketChannel ch) {
                            ch.pipeline().addLast(new AuthHandler());
                            //..

                        }
                    });

            ChannelFuture f = b.bind(8888).sync();

            f.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }
}
```



### 创建服务端Channel

![image-20191116155248249](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116155248249.png)

NioServerSocketChannel构造函数处理流程：

![image-20191116160215490](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116160215490.png)



### 初始化服务端Channel

![image-20191116165530103](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116165530103.png)

![image-20191116165622605](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116165622605.png)



### 注册Selector

```java
ChannelFuture regFuture = config().group().register(channel);
```

会调用到AbstractChannel中的register方法

```java
@Override
public final void register(EventLoop eventLoop, final ChannelPromise promise) {
    if (eventLoop == null) {
        throw new NullPointerException("eventLoop");
    }
    if (isRegistered()) {
        promise.setFailure(new IllegalStateException("registered to an event loop already"));
        return;
    }
    if (!isCompatible(eventLoop)) {
        promise.setFailure(
                new IllegalStateException("incompatible event loop type: " + eventLoop.getClass().getName()));
        return;
    }

    AbstractChannel.this.eventLoop = eventLoop;

    if (eventLoop.inEventLoop()) {
        register0(promise);
    } else {
        try {
            eventLoop.execute(new Runnable() {
                @Override
                public void run() {
                    register0(promise);
                }
            });
        } catch (Throwable t) {
            logger.warn(
                    "Force-closing a channel whose registration task was not accepted by an event loop: {}",
                    AbstractChannel.this, t);
            closeForcibly();
            closeFuture.setClosed();
            safeSetFailure(promise, t);
        }
    }
}
```

![image-20191116170522249](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116170522249.png)

### 端口绑定

![image-20191116172319451](/Users/zengxiangfei/Documents/mywiki/netty/images/image-20191116172319451.png)







