---
title: Docker And k8s 实战
date: 2019-03-14 10:39:20
categories: docker
tags: docker
---



## Docker And k8s 实战

课程内容：

## 初识微服务

- 微服务架构图

微服务带来问题和解决方案

- 传统服务+解决方案

类似框架：

- mesos，Swarm，kubernetes

kubernetes

cicd和devops



初识微服务：

- 软件架构：软件架构是在软件的内部，经过**综合各种因素**的考量、权衡，选择特定的技术，将系统划分成不同的部分并使这些部分互相分工，彼此协作，为用户提供需要的价值。各种因素：

  - 业务需求
  - 技术栈
  - 成本
  - 组织架构
  - 可扩展性
  - 可维护性

- 什么是单体架构：功能、业务集中在一个发布包里，部署运行在同一个进程中。

  - 优势：
    - 易于开发
  - 劣势：
    - 代码膨胀，难于维护
    - 构建、部署成本大
    - 新人上手困难
    - 创新困难
    - 可扩展性差

- 微服务架构：使用一套小服务来开发单个应用的方式，每个服务运行在**独立进程**里，一般采用**轻量级的通讯机制**互联，并且他们还可以以**自动化的方式部署**。特征：

  - 单一职责
  - 轻量级通信，比如http
  - 隔离性
  - 有自己的数据
  - 技术的多样性

  诞生背景：

  - 互联网行业快速发展
  - 敏捷开发，精益方法深入人心
  - 容器技术的成熟

  优势与不足：

  优势：

  - 独立性
  - 敏捷性
  - 技术栈灵活
  - 高效团队

  不足：

  - 额外的工作：如服务的拆分，DDD：领域驱动设计
  - 数据一致性
  - 沟通成本





## 微服务架构引入的问题及解决方案

- 微服务间如何通讯？

  - 从通讯模式角度考虑

    - 一对一还是一对多，同步还是异步？

  - 从通讯协议考虑

    - REST API：表现层的状态转移，在网络中，客户端和服务端的交互方式
    - RPC：dubbo，dubbox，motan，grpc，thrift
    - MQ：实际场景中用的不多，如发布订阅类似业务场景

  - 如何选择RPC框架：需考虑以下问题：

    - I/O、线程调度模型
    - 序列化方式：效率直接影响rpc通信效率
    - 多语言支持
    - 服务治理

  - 流行的RPC框架

    - Dubbo/Dubbox：nio，序列化是：hessian

      ![image-20190312143437518](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312143437518.png)

    - Thrift：apache

      ![image-20190312143910052](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312143910052.png)

    - Motan

      ![image-20190312144429516](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312144429516.png)

    - Grpc：google

      ![image-20190312144127904](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312144127904.png)

    - 比较

      ![image-20190312144246025](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312144246025.png)

- 微服务如何发现彼此？

  服务发现：

  传统服务VS微服务：

  ![image-20190312144734160](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312144734160.png)

  如何发现：

  - 客户端发现

    ![image-20190312144819664](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312144819664.png)

  - 服务端发现

    ![image-20190312144911947](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312144911947.png)

- 微服务怎样部署？更新？扩容？

  传统服务：

  ![image-20190312145041625](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312145041625.png)

  服务编排：服务发现，服务部署、更新、扩容。

  流行的服务编排工具：

  - Mesos
  - Docker Swarm 
  - Kubernetes



### SpringBoot、SpringCloud与微服务

Springboot的使命：化繁为简

核心功能：

- 独立运行 java -jar xxx.jar
- 内嵌web服务器
- 简化配置
- 准生产的应用监控

Springboot与微服务的关系：Java开发微服务的润滑剂。



SpringCloud的使命：简化Java的分布式系统，比如session共享，负载均衡，分布式的事务管理。

核心功能：

- 统一的配置管理
- 服务注册与发现
- 服务负载均衡
- 消息总线
- 服务容错与熔断

深入理解：

- 一系列框架
- 简化java的分布式系统
- SpringBoot的封装



SpringCloud VS SpringBoot

- SpringBoot意在简化，是一种开发、配置风格
- SpringCloud意在简化分布式，是功能的集合，风格的统一

SpringCloud VS 微服务

- Java的微服务
- 侧重功能，侧重开发

**SpringCloud是devops的dev部分。**



SpringCloud核心组件：

- Netflix Eureka

  ![image-20190312150726659](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312150726659.png)

- Netflix Ribbon

  ![image-20190312150925756](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312150925756.png)

- Netflix Hystrix

  ![image-20190312151125092](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312151125092.png)

- Netflix Zuul：服务路由、安全等

  ![image-20190312151410228](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312151410228.png)

- Spring Cloud Config

  传统配置文件解决方案：

  - 同步手动修改测试环境配置
  - zookeeper配置管理，zk负责人负责维护
  - maven的profile，maven构建时直接指定配置文件

  ![image-20190312151529579](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190312151529579.png)






## 微服务开发

业务场景：

- 用户可以注册和登陆，避免使用session
- 登陆用户可以对课程进行curd操作

分解：

用户服务

- 用户登陆
- 用户注册
- 用户基本信息查询
- 无状态，无session
- 单点登录

课程服务

- 登陆验证
- 课程curd

信息服务

- 发送邮件
- 发送短信

用户edgeservice

课程edgeservice

API GATEWAY

![image-20190313125040808](/Users/zengxiangfei/Documents/mywiki/java/images/image-20190313125040808.png)

### Thrift安装与验证

