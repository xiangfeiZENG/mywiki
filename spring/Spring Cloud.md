# Spring Cloud 微服务实践

[TOC]



## Spring Cloud原生云应用

### 系列介绍

- 核心理念：介绍核心理念、架构及模式
- 使用方法：基本使用方法
- 经验分享：分享真实经验
- 源码分析：通过核心组件源码分析，加深理解



**什么是 Spring Cloud？**

Spring Cloud 为开发⼈员提供快速构建分布式系统的⼀些通⽤模式，其中包括：配置管理、服务发现、服务短路、智能路由、微型⽹关、控制总线、⼀次性令牌、全局锁、领导选举、分布式会话和 集群状态。分布式系统间的协调导向样板模式，并且使⽤ Spring Cloud 的开发⼈员能够快速地构建 实现这些模式的服务和应⽤。这些服务和应⽤也将在任何环境下⼯作良好，⽆论是开发者的笔记本、 还是数据中⼼裸机或者管控平台。

Spring Cloud优势在于提供了一站式服务。

- dubbo：用于服务治理，在远程rpc的调用性能要好些，稳定些，rest走公网不是很安全，走内部网络解析也不是很方便
- springcloud：不止是服务治理



### 背景知识

![image-20190930110928196](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190930110928196.png)

#### **面向服务架构(SOA)**

A **service-oriented architecture (SOA)** is a style of software design where services are provided to the other components by application components, through a communication protocol over a network. **The basic principles of service-oriented architecture are independent of vendors, products and technologies**. A service is a discrete unit of functionality that can be accessed remotely and acted upon and updated independently, such as retrieving a credit card statement online.

![image-20190405210749597](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190405210749597.png)



SOA有两部分内容，一部分是基础设施，一部分是人。

#### 微服务架构（MSA）

Microservices is a variant of the serviceoriented architecture (SOA) architectural style that structures an application as a collection of loosely coupled services. In a microservices architecture, services should be ﬁne-grained and the protocols should be lightweight. The beneﬁt of decomposing an application into different smaller services is that it improves modularity and makes the application easier to understand, develop and test.

![image-20190405211023590](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190405211023590.png)



#### 事件驱动架构（EDA）

Event-driven architecture (EDA), is a software architecture pattern promoting the production, detection, consumption of, and reaction to events.

This architectural pattern may be applied by the design and implementation of applications and systems that transmit events among loosely coupled software components and services. An event-driven system typically consists of event emitters (or agents), event consumers (or sinks), and event channels.

![image-20190405211220118](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190405211220118.png)



### 12-Factor应用

I. Codebase：代码管理

II. Dependencies：依赖

III. Conﬁg：配置，外部化配置，内部化配置

IV. Backing services：后端服务

V. Build, release, run：构建，发布，运行（启动）

VI. Processes: 进程，分布式计算又称进程调用，远程进程互相调用。

VII. Port binding：端口绑定，一种寻址的方式。

VIII. Concurrency：并发

IX. Disposability

X. Dev/prod parity

XI. Logs

XII. Admin processes

![image-20190405211711177](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190405211711177.png)



### Bootstrap上下文

- 理解 Bootstrap 上下文 

  Bootstrap 上下⽂是 Spring Cloud 新引⼊的，与传统 Spring 上下⽂相同，系 ConﬁgurableApplicationContext 实例，由 **BootstrapApplicationListener** 在监听 **ApplicationEnvironmentPreparedEvent** 时创建。

- Spring 事件/监听器器模式

  - ApplicationEvent / ApplicationListener 

- 理解 SpringApplication

SpringApplication 是 **Spring Boot 引导启动类**，与 Spring 上下⽂、事件、监听器以及环境等组件 关系紧密，其中提供了控制 Spring Boot 应⽤特征的⾏为⽅法。

- Spring Boot 应⽤运⾏监听器

  - SpringApplicationRunListener

- 理解 Spring Boot 事件 

  - 事件触发器：EventPublishingRunListener

  ApplicationStartedEvent：启动

  ApplicationEnvironmentPreparedEvent：

  ApplicationPreparedEvent

  ApplicationReadyEvent / ApplicationFailedEvent

- 理解 Spring Boot / Spring Cloud 上下⽂层次关系（事件和上下文关系非常紧密）

  - Spring Boot 上下⽂

    - ⾮ Web 应⽤：AnnotationConﬁgApplicationContext

    -  Web 应⽤：AnnotationConﬁgEmbeddedWebApplicationContext

  - Spring Cloud 上下⽂：Bootstrap （⽗）

    - Spring Cloud Bootstrap上下文优先于Spring Boot 应用上下文，并且Bootstrap上下文是Spring Boot 应用上下文的 双亲（parent）



### Actuator Endpoints

- 理解 Actuator Endpoints

Actuator 中⽂直译为“传动装置”，在 Spring Boot 使⽤场景中表示为“⽣产⽽准备的特性” （Production-ready features），这些特性通过 HTTP 端⼝的形式，帮助相关⼈员管理和监控应 ⽤。⼤致上可以归类为：

监控类：“端点信息”、“应⽤信息”、“外部化配置信息”、“指标信息”、“健康检查”、“Bean 管 理”、“Web URL 映射管理”、“Web URL 跟踪” 管理类：“外部化配置”、“⽇志配置”、“线程dump”、“堆dump”、“关闭应⽤” 

注意：Spring Boot 1.5 开始 Actuator 增强了安全能⼒



- Spring Cloud 扩展 Actuator Endpoints

上下⽂重启：/restart 暂停：/pause 恢复：/resume

注意：Spring Boot 1.5 开始 Actuator 增强了安全能⼒





#### 问答互动







## 配置客户端

![image-20190922105154818](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190922105154818.png)



### 技术回顾

#### Spring Environment 

Environment

Environment 是⼀种在容器内以 配置（Proﬁle） 和 属性（Properties） 为模型的应⽤环境抽象整合。 把配置和属性抽象起来，提供api整合。

Spring Framework 提供了两种 Environment 的实现，即

• ⼀般应⽤ ： StandardEnvironment

• Web 应⽤： StandardServletEnvironment

#### Spring Proﬁles 

配置（Proﬁle）

在 Spring 容器，Proﬁle 是⼀种命名的 Bean 定义逻辑组。⼀个 Spring 应⽤可以同时激活多个 Proﬁle，常⻅见的使⽤场景如：应⽤部署环境（test、stage、production）、单元测试等。

应⽤程序可通过调⽤ **ConﬁgurableEnvironment** 接⼝控制 Proﬁle 的激活，如：

- setActiveProﬁles(String…) 
- addActiveProﬁle(String)
-  setDefaultProﬁles(String…)

#### Spring Properties

属性（Properties）

属性⼜称之为配置项，Key-Value的形式。在 Spring 应⽤中常⽤作占位符（Placeholder），⽽在 API 层⾯， Spring Framework 如下抽象来表述：

- 组合属性：PropertySources

- 单⼀属性：PropertySource

####  Spring 事件监听器 

事件（Event）

ApplicationEvent 

ApplicationListener

事件监听器（EventListener） since 4.2 

#### ConﬁgFileApplicationListener

**ConﬁgFileApplicationListener**

在 Spring Boot 场景中，⽤于读取默认以及Proﬁle 关联的配置⽂件（application.properties）

- application.properties

  加载器：PropertiesPropertySourceLoader

- application.yml

  加载器：YamlPropertySourceLoader





### Environment 端点

请求URI：/env

数据来源：EnvironmentEndpoint

Controller来源：EnvironmentMvcEndpoint



### Bootstrap 配置属性

- Bootstrap配置

  通过BootstrapApplicationListener监听器加载

- Bootstrap 配置文件

  ```
  String configName = environment
      .resolvePlaceholders("${spring.cloud.bootstrap.name:bootstrap}");
  ```

  当spring.cloud.bootstrap.name存在时，使用该配置，否则，使用bootstrap配置。

调整 Bootstrap 配置⽂件路径

 覆盖远程配置属性 

⾃定义 Bootstrap 配置 

1. 创建`META-INF/spring.factories`文件
2. 编写相应的配置类。

⾃定义 Bootstrap 配置属性源



### 本节⼩结



### 问答互动

1. spring boot 已有一套配置，为什么spring cloud也要提供一套配置

   spring Cloud的配置来源于Spring Boot基础，自然都来源于Spring Framework。

2. 本节的应用场景时什么？

   了解Environment以及PropertySource关系以及编程的模式，可以极大地提高实现弹性，比如你可以抛弃Spring Cloud官方的分布式配置实现，比如Git，可以结合Zookeeper以及Ali Diamond来实现。



## 配置服务器



### 分布式配置

#### 分布式配置架构 

![image-20191004151105448](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191004151105448.png)

![image-20191004151337708](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191004151337708.png)





#### Spring Cloud 配置服务器 

- Spring Cloud Conﬁg Server

  Spring Cloud 配置服务器提供分布式、动态化集中管理应⽤配置信息的能⼒

- 构建 Spring Cloud 配置服务器

  @EnableConﬁgServer



#### 服务端 Environment 仓储 

- EnvironmentRepository

  Spring Cloud 配置服务器管理多个客户端应⽤的配置信息，然⽽这些配置信息需要通过⼀定的规 则获取。Spring Cloud Conﬁg Server 提供 EnvironmentRepository 接⼝供客户端应⽤获取，其中获 取维度有三：

  - {application} : 配置客户端应⽤名称，即配置项：spring.application.name

  - {proﬁle}： 配置客户端应⽤当前激活的Proﬁle，即配置项：spring.proﬁles.active

  - {label}：配置服务端标记的版本信息，如 Git 中的分⽀名



#### Spring Cloud 配置客户端

实战演示

##### • 基于⽂件系统实现

1. 激活配置服务器，`@EnableConfigServer`

2. 建立本地配置仓库

   ```
   spring.cloud.config.server.git.uri=${user.dir}/config-server/src/main/resources/configs
   
   # 初始化仓库
   git init .
   git add .
   git commit -am "first commit"
   ```

3. 测试：<http://localhost:9090/segmentfault/test>



##### • 基于远程Git 仓库

1. 激活配置服务器
2. 配置远程Git仓库

```
spring.cloud.conﬁg.server.git.uri = https://github.com/mercyblitz/tmp.git
spring.cloud.config.server.git.force-pull = true
```







### 动态配置属性Bean

使用场景：动态修改bean的值

@ConfigurationProperties



问题，如果`spring-cloud-config-client`需要调整所有机器的配置如何操作？

> 注意，配置客户端应用所关联的分布式配置内容，优先于传统`application.properties(application.yml)`或者`bootstrap.properties(bootstrap.yml)`



- @RefreshScope

- **/refresh**** Endpoint

  除非整合总线，否则需要refresh。

- ContextRefresher



### 健康指标 

http://localhost:8080/health

```
{
status: "UP",
diskSpace: {
status: "UP",
total: 499963174912,
free: 389886550016,
threshold: 10485760
},
refreshScope: {
status: "UP"
},
configServer: {
status: "UNKNOWN",
error: "no property sources located"
}
}
```



### 管理端点 

/pause 

/resume

/restart



### 问答互动

1. Spring Cloud中如何处理多个Service互相循环引用？比如A依赖B，B依赖A

   一般而言，这种药避免，尤其重启的时候，互相依赖，关联的应用都可能起不来。

   如果是相互强依赖，尽量避免。

2. 哪些配置应该写在boostrap.yml，哪些写在application.yml，哪些写在外部配置文件中动态更新？

   bootstrap.yml配置Spring Cloud属性

   application.yml配置当前Spring Boot 应用属性，比如server.port等

   动态配置，比如开关、数据阈值，可以放在配置服务器中。

3. 如何理解EndPoint

   Spring Boot Endpoint是一种管理端点，它提供一种以HTTP通讯方式的类似于JMX的功能。

4. 连接池能不能动态刷新？比如祥动态调整数据库连接池的大小或者tomcat连接池的大小。

   可以，比较麻烦，需要增加@RefreshScope。好的方式，通过反射替代DataSource。

5. 什么时候用Spring Profile，什么时候用maven profile

   Spring Profile用于运行时，maven profile用于打包编译。



## 服务注册与发现

### 服务发现/注册 

#### 服务发现

- 服务发现（Service Discovery）

  在计算机⽹络中，⼀种⾃动发现设备或者服务的技术，通过服务发现协议（Service Discovery Protocol）实现。

- 常⻅见协议

  -  Java：Jini（Apache River）

  - REST：HATEOAS
  - Web Services：UDDI（Universal Description Discovery and Integration）

#### 服务注册

- 服务注册（Service Registration）

  在计算机⽹络中，为了更好地治理多个设备或者服务，这些设备或者服务主动或者被动注册到管理 中⼼，以便服务被发现和消费。

- 常⻅见注册中⼼

  • Apache Zookeeper：高一致性，性能稍差

  • Netﬂix Eureka：高可用，不是高一致。

  • Consul：折中方案

![image-20191005115644461](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191005115644461.png)





### ⾼可⽤架构

⾼可⽤（High Availability）

⼀种系统特性，致⼒于确保可接受程度的操作执⾏，通常采⽤上线时间作为基准。其中，以⼀年内的 上线时间与⾃然时间的⽐率来描述可⽤性。

基本原则

•消灭单点故障

•可靠性交迭

•故障探测

  ![image-20191005120304761](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191005120304761.png)



### Spring Cloud Netﬂix Eureka

服务发现：Eureka

Eureka 是由 Netﬂix 公司发明的服务发现中间件，包括服务发现服务器和客户端的。

核⼼组件

- Eureka Server

- Eureka Client



服务端：Eureka Server

Eureka Server 是 Eureka Client 的注册服务中⼼，管理所有注册服务、以及其实例信息和状态。

#### 运⾏ Eureka Server

- 依赖：org.springframework.cloud:spring-cloud-starter-eureka-server 
- 激活：@EnableEurekaServer

关于错误：

```java
com.netflix.discovery.shared.transport.TransportException: Cannot execute request on any known server
	at com.netflix.discovery.shared.transport.decorator.RetryableEurekaHttpClient.execute(RetryableEurekaHttpClient.java:111) ~[eureka-client-1.6.2.jar:1.6.2]
	at 
```

原因：Eureka Server即是注册服务器，也是客户端，默认情况也需要配置注册中心地址。

<http://localhost:8080/health>

```
{
description: "Spring Cloud Eureka Discovery Client",
status: "UP",
discoveryComposite: {
description: "Spring Cloud Eureka Discovery Client",
status: "UP",
discoveryClient: {
description: "Spring Cloud Eureka Discovery Client",
status: "UP",
services: [ ]
},
eureka: {
description: "Remote status from Eureka server",
status: "UNKNOWN",
applications: { }
}
},
diskSpace: {
status: "UP",
total: 499963174912,
free: 389845549056,
threshold: 10485760
},
refreshScope: {
status: "UP"
},
hystrix: {
status: "UP"
}
}
```



#### 运行Eureka Client

客户端：Eureka Client

Eureka Client 为当前服务提供注册、同步、查找服务以及其实例信息或状态等能⼒。

运⾏ Eureka Client

- 依赖：org.springframework.cloud:spring-cloud-starter-eureka
-  激活：@EnableEurekaClient 或者 **@EnableDiscoveryClient**，建议使用后则，更换注册中心时不用改代码。



调整状态⻚页⾯

- 配置：eureka.instance.statusPageUrlPath 调整健康检查⻚页⾯

  ```
  eureka.instance.status-page-url-path=/heath
  ```

  

-  配置：eureka.instance.healthCheckUrlPath 
- 整主⻚



Eureka 客户端配置 API

- **EurekaClientConﬁgBean** Eureka 实例配置 API

- **EurekaInstanceConﬁgBean**





###  Spring Cloud Conﬁg 整合 

1. 调整Spring Cloud Config Server作为Eureka客户端
   - 添加maven依赖
   - 激活客户端
   - 修改配置注册到注册中心
2. 调整Spring Cloud Config Client作为Config客户端
   - 创建bootstrap.properties
   - 添加相关配置



### 问答互动

1. 注册中心是否可以跨机房部署？

   可以实现，Eureka Server之间的相互复制。

2. 在生产环节中使用Eureka作为注册中心，是否需要关闭自我保护功能？如果不关闭，如何查询实时的服务列表？如果关闭了，会有什么不良影响？

3. Spring Cloud配置中心和携程开源的阿波罗配置中心

   Spring Cloud Config配置的优势天然支持，不足在于基于Hppt，并且服务端基于Git。阿波罗的方式也是Http同步的，服务端存储关系数据库，动态性比较好。

   生产中用Spring Cloud Config比较少。

4. Eureka心跳机制的原理？

   服务端检查已注册客户端应用是否健康，同时客户端也会上报信息。

![image-20191005223544590](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191005223544590.png)

本节内容一般用于开发环境，生产环境请参考下节内容。



## Spring Cloud 高可用服务

### Spring Cloud Eureka ⾼可⽤

#### ⾼可⽤客户端（HA Client）

多⽤于⽣产环境，客户端应⽤关联或配置注册中⼼服务器集群，避免注册中⼼单点故障

- 常⻅见配置⼿段
  - 多注册中⼼主机
  - 注册中⼼ DNS
  - ⼴播

- ⾼可⽤客户端（HA Client）

![image-20191005223925575](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191005223925575.png)





Spring Cloud Eureka Client

如果 Eureka 客户端应⽤配置多个 Eureka 注册服务器，那么默认情况只有第⼀台可⽤的服务器，存 在注册信息。如果 第⼀台可⽤的 Eureka 服务器 Down 掉了，那么 Eureka 客户端应⽤将**会选择下 ⼀台**可⽤的 Eureka 服务器。

- 配置属性
  - eureka.client.serviceUrl.defaultZone=http://${eureka.server1.host}:${eureka.server1.port}/ eureka,${eureka.server2.host}:${eureka.server2.port}/eureka

- 效果演示



Spring Cloud Eureka Client

- 配置项：eureka.client.serviceUrl
  - 源码分析：EurekaClientConﬁgBean



Spring Cloud Eureka Client

• 思考

• 当注册应⽤之间存在相互关联时，那么上层应⽤如何感知下层服务的存在？

• 如果上层应⽤感知到下层服务，那么它是怎么同步下层服务信息？

• 如果应⽤需要实时地同步信息，那么确保⼀致性？



Spring Cloud Eureka Client • 应⽤元信息

获取间隔

• 配置项：eureka.client.registryFetchIntervalSeconds 同步间隔

• 配置项：eureka.client.instanceInfoReplicationIntervalSeconds





#### ⾼可⽤注册中⼼（HA Registry Center）

⾼可⽤注册中⼼不但需要提供集群环境，解决单点故障的问题。同时，也许优雅地处理注册中⼼之 间信息同步的问题

配置属性

eureka.server.host1 : eureka.client.serviceUrl.defaultZone=http://${eureka.server2.host}:$ {eureka.server2.port}/eureka

eureka.server.host2 : eureka.client.serviceUrl.defaultZone=http://${eureka.server1.host}:$ {eureka.server1.port}/eureka



⾼可⽤注册中⼼（HA Registry Center）

![image-20191005224156764](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191005224156764.png)



Spring Cloud Eureka Server

配置属性

eureka.server.host1 : eureka.client.serviceUrl.defaultZone=http://${eureka.server2.host}:$ {eureka.server2.port}/eureka

eureka.server.host2 : eureka.client.serviceUrl.defaultZone=http://${eureka.server1.host}:$ {eureka.server1.port}/eureka

效果演示







### Spring Cloud Consul

Consul 组件(consul 领事)

- 服务发现（Service Discovery） 
- 健康检查（Health Check）

- 键值存储（KV Store） 
- 多数据中⼼（Multi Datacenter）

理解 Raft 协议：http://thesecretlivesofdata.com/raft/



快速上⼿

- 安装
-  Agent 启动 
- 键值存储（KV Store） 
- UI 控制台 

Spring Cloud 整合



日后再学。



### 问答互动

1. 生产环境获取和同步时间间隔一般取值多少？

   获取参数一般而言，看客户端应用的需要，如果获取过于频繁，当服务实例过多时，那么会对自身产生一定影响。一般是，按需要拉取

   客户端同步信息过于频繁的话，对服务器有很多副作用，一般不太建议调整到5s以下。





## Spring Cloud 负载均衡

### 客户端负载均衡 

客户端负载均衡

![image-20191006112120427](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191006112120427.png)

优势

- 稳定性⾼

不⾜

- 升级成本⾼





### 服务端负载均衡 



服务端负载均衡

![image-20191006112156222](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191006112156222.png)



优势

- 统⼀维护，成本低

不⾜

- ⼀旦故障，影响⼤





### 调度算法 

调度算法

- 先来先服务（First Come First Served） 
- 最早截⽌时间优先（Earliest deadline ﬁrst）

- 最短保留时间优先（Shortest remaining time ﬁrst） 
- 固定优先级（Fixed Priority）

- 轮训（Round-Robin） 
- 多级别队列（Multilevel Queue）







### 特性

特性

- ⾮对称负载（Asymmetric load） 
- 分布式拒绝服务攻击保护 
- 直接服务返回 
- 健康检查
- 优先级队列 
- 其他



### 技术回顾

Spring Framework

RestTemplate

- 序列化/反序列化：HttpMessageConvertor 
- 实现适配：ClientHttpRequestFactory 
- **请求拦截**：ClientHttpRequestInterceptor



### Netﬂix Ribbon

依赖

- org.springframework.cloud:spring-cloud-starter-ribbon

激活

@RibbonClient

```java
@SpringBootApplication
@RibbonClients({
        @RibbonClient(name = "spring-cloud-service-provider")
})
//@EnableDiscoveryClient
public class RibbonClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(RibbonClientApplication.class, args);
    }

    // 声明 RestTemplate
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```



配置

- `${serviceId}.ribbon.listOfServers = ​${serviceUrl},${serviceUrl2},…`





### Netﬂix Ribbon 整合 Eureka

激活服务发现

- @EnableDiscoveryClient

触发负载均衡

- @LoadBalanced

客户端调⽤

- RestTemplate



### Netﬂix Ribbon 核⼼接⼝

实际请求客户端

- LoadBalancerClient

- RibbonLoadBalancerClient

负载均衡上下⽂

- LoadBalancerContext

- RibbonLoadBalancerContext



负载均衡器

•ILoadBalancer

• BaseLoadBalancer

• DynamicServerListLoadBalancer

• ZoneAwareLoadBalancer

• NoOpLoadBalancer



规则接⼝

•IRule

• 随机规则：RandomRule

• 最可⽤规则：BestAvailableRule

• 轮训规则：RoundRobinRule

• 重试实现：RetryRule



规则接⼝

•IRule

• 客户端配置：ClientConﬁgEnabledRoundRobinRule

• 可⽤性过滤规则：AvailabilityFilteringRule

• RT权重规则：WeightedResponseTimeRule

• 规避区域规则：ZoneAvoidanceRule



•PING 策略

•IPingStrategy

• NoOpPing

• DummyPing

• PingConstant

• PingUrl

• NIWSDiscoveryPing



### 问答互动





## Ribbon 源码解读







## Spring Cloud 服务短路

### 服务短路

名词由来

- https://martinfowler.com/bliki/ CircuitBreaker.html

- ⽬的：系统整体性保护

![image-20191007114852284](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191007114852284.png)

近义词

- 服务容错（Fault tolerance）：强调容忍错误，不⾄于整体故障 
- 服务降级（downgrade）：强调服务⾮强依赖，不影响主要流程



### Netﬂix Hystrix

- Hystrix: Latency（延迟） and Fault Tolerance（容错） for Distributed Systems https://github.com/Netﬂix/Hystrix 

reactive，rxjava来实现。

Maven 依赖

• com.netﬂix.hystrix:hystrix-core



**整合 Spring**

• 激活：@EnableHystrix 

编程模型

• 注解⽅式：@HystrixCommand

• 编程⽅式：HystrixCommand



**整合 Spring Cloud**

• 激活：@EnableCircuitBreaker

• 依赖：org.springframework.cloud:spring-cloud-starter-hystrix 端点

• Endpoint：/hystrix.stream





### 生产准备特性

Netﬂix Hystrix Dashboard

整合 Spring Cloud

• 激活：@EnableHystrixDashboard

• 依赖：org.springframework.cloud:spring-cloud-starter-hystrix-dashboard 收集



Turbine 在stream那块再讲。



### 问答互动

1. spring boot中ControllerAdvice可以拦截404和500吗？

   spring boot使用了BasicErrorController破坏了原有的spring web mvc统一处理方式。

2. HystrixCommand在阿里是否通用？

   阿里没有普遍在用。

3. ![image-20191014131225242](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191014131225242.png)





## Spring Cloud 服务调用

![image-20191014153339077](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191014153339077.png)



### 核⼼概念 

远程过程调⽤（RPC） 

接⼝定义语⾔（IDL） 

通讯协议（Protocol）

 Netﬂix Feign 

Spring Cloud 技术回顾 （服务短路 、负载均衡、服务发现、分布式配置等）



远程过程调⽤（RPC）

⼀个计算机通信协议。该协议允许运⾏于⼀台计算机的程序调⽤另⼀台计算机的⼦程序，⽽程序员 ⽆需额外地为这个交互作⽤编程。如果涉及的软件采⽤⾯向对象编程，那么远程过程调⽤亦可称作 远程调⽤或远程⽅法调⽤

例如

Java RMI（⼆进制协议） WebServices（⽂本协议）



消息传递

RPC 是⼀种请求-响应协议，⼀次 RPC在客户端初始化，再由客户端将请求消息传递到远程的服 务器，执⾏指定的带有参数的过程。经过远程服务器执⾏过程后，将结果作为响应内容返回到客 户端。

存根（Stub）

在⼀次分布式计算 RPC 中，客户端和服务器转化参数的⼀段代码。 由于存根的参数转化，RPC 执⾏过程如同本地执⾏函数调⽤。存根必须在客户端和服务器两端均装载，并且保持兼容。





### Spring Cloud Feign 

依赖：org.springframework.cloud:spring-cloud-starter-feign

激活

@EnableFeignClients

申明

@FeignClient



默认组件

Decoder/Encoder : ResponseEntityDecoder / SpringEncoder 

Logger : Slf4jLogger 

Contract : SpringMvcContract 

Feign.Builder : HystrixFeign.Builder 

Client : LoadBalancerFeignClient（Ribbon 激活的话）





### Spring Cloud 整合 

整合负载均衡：Netﬂix Ribbon 

整合服务短路：Netﬂix Hystrix

整合服务发现：Netﬂix Eureka

整合配置服务器：Conﬁg Server 

整合配置客户端：Conﬁg Client



### 问答互动 

### 下期预告









## Spring Cloud 服务网关

![image-20191006194632154](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191006194632154.png)

### 核心概念

⽹关

⽹关是程序或者系统之间的**连接节点**，扮演着程序或系统之间的⻔门户，允许它们之间通过通讯协议 交换信息，它们可能是同构和异构的系统。

例如

- REST API ⽹关

- WebServices ⽹关



使⽤场景

- 监控（Monitoring）：数据打点，可以做一些访问日志
- 测试（Testing） ：压力测试
- 动态路由（Dynamic Routing） 
- 服务整合（Service Integration） 
- 负荷减配（Load Shedding）

- 安全（Security） 
- 静态资源处理（Static Resources handling）

- 活跃流量管理（Active trafﬁc management）



数据来源

- 服务发现 
- 服务注册

通讯⽅式

- 协议：⼆进制、本⽂ 
- ⽅式：同步、异步



服务接⼝

平台⽆关

- XML、JSON、HTML 

平台相关

- IDL、RMI、Hession



### Spring Cloud Zuul

00:31:24

依赖

•org.springframework.cloud:spring-cloud-starter-zuul

激活

•@EnableZuulProxy

配置

zuul.*



路由设置

- 配置模式
  - 服务-映射：zuul.routes.${service-id} = ${url-pattern} 

- 路径模式
  - 当前层级匹配：/*
  - 递归层级匹配：/**



HTTP 客户端

HttpClient（默认）

• 装配：HttpClientRibbonConﬁguration 

OkHttp（条件）

• 激活配置：ribbon.okhttp.enabled = true

• 装配：OkHttpRibbonConﬁguration



端点（Endpoint）

- 实现：RoutesEndpoint 
- 路径：/routes

过滤器：ZuulFilter



### Spring Cloud 整合Zuul

整合服务调⽤：Feign 

整合负载均衡：Netﬂix Ribbon 

整合服务短路：Netﬂix Hystrix 

整合服务发现：Netﬂix Eureka 

整合配置服务器：Conﬁg Server 

整合配置客户端：Conﬁg Client



### 问答互动

