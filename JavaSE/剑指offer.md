---
title: 剑指offer
date: 2019-03-21 21:21:20
categories: java
tags: java
---

# 剑指offer-Java

[TOC]



Jdk8 支持到2025

jdk11支持到2026



## 网络基础知识

### OSI开放式互联参考模型

1. 物理层：

   机械、电子、定时通信信道上的原始比特流传输。

   模数转换与模数转换，网卡工作在这一层。

2. 数据链路层

   物理寻址、同时将原始比特流转换为逻辑传输线路

   定义了如何格式化数据进行传输、以及如何控制对物理介质的访问，交换机工作在这一层。

3. 网络层

   控制子网的运行，如逻辑编址、分组传输、路由选择。

   路由器属于网络层。需关注**IP**协议

4. 传输层

   接收上一层的数据，在必要的时候把数据**分割**，并将这些数据交给网络层，且保证这些数据段有效到达对端（批量和可靠性的需求，于是有了网络层）

   需关注TCP协议、UDP协议

5. 会话层

   不同机器上的用户之间建立及管理会话。（自动收发包、自动寻址）

6. 表示层

   信息的语法语义以及他们的关联，如加密解密、转换翻译、压缩解压缩。

7. 应用层

   应用层方便应用更方便的传输数据。

   需关注HTTP协议



先自上而下，后自下而上处理数据头部

![image-20191124220006550](/Users/zengxiangfei/Documents/mywiki/java/images/image-20191124220006550.png)



### TCP/IP

OSI的"实现”：TCP/IP

![image-20191124220246501](/Users/zengxiangfei/Documents/mywiki/java/images/image-20191124220246501.png)



先自上而下，后自下而上处理数据头部

![image-20191124220458011](/Users/zengxiangfei/Documents/mywiki/java/images/image-20191124220458011.png)



### TCP的三次握手

传输控制协议TCP简介：

![image-20191124220847500](/Users/zengxiangfei/Documents/mywiki/java/images/image-20191124220847500.png)



TCP报文头

- 没有ip，ip是ip层的事
- **两个进程在计算机内部通信**，可以通过管道、内存共享、信号量、消息队列等方法进行通信。
- Sequence Number：序号
- Ack：期望收到的下一个报文号
- TCP Flags
  - URG：紧急指针标志
  - **ACK：确认序号标志**
  - PSH：push标志
  - RST：重制连接标志
  - **SYN：同步序号，用于建立连接过程**
  - **FIN：finish标志，用于释放连接**
- 

![image-20191124220957799](/Users/zengxiangfei/Documents/mywiki/java/images/image-20191124220957799.png)

"握手"是为了建立连接，TCP三次握手的流程图如下：

![image-20191124221923103](/Users/zengxiangfei/Documents/mywiki/java/images/image-20191124221923103.png)

