Spring Boot 实践



[TOC]

## Spring Boot初体验

关注技术思想、技术的历史、技术的脉络，融汇贯通。

SUN Java（SCJP、SCWCD、SCBCD），Oracle OCP

了解技术体系

微服务介绍

- 概念

  - Microservices are a more concrete and modern interpretation of service-oriented architectures (SOA) used to build distributed software systems（Source:wikipedia）

    微服务是更细粒度的soa。

- 目的

- - 解决单体应用（Monolithic Application）规模增加时所带来的问题

- 关键字

- - Monolithic、SOA、架构、分布式系统



- 单体应用（Monolithic） VS 微服务

- - 优势

  - - 开发（ Development ）
    - 稳定（ Stability ）
    - 性能（Performance）
    - 部署（Deployment）

  - 不足

  - - 中心化（ Centralization ）
    - 耦合（ Coupling ）
    - 学习成本（Learning Cost）
    - 伸缩（Scale）：水桶效应，最差板
    - 持续交付（Continuous Delivery）

- 不要为了技术而技术，不要为了概念而概念。



- 面向服务架构（Service-oriented architecture）

![image-20190413094443528](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190413094443528.png)

- 面向服务架构（SOA） VS 微服务

- - 类同

  - - 面向服务（ Service-Oriented ）
    - 松耦合（Loose-Coupling）
    - 模块化（Modular）
    - 分布式计算（Distributed Computing）
    - 平台无关性（Independent Platform）

  - 差异

  - - “原子性”（Atomic）：微服务相对于soa而言更加原子性，粒度更小。
    - 领域驱动设计（DDD）：太过于理想化，实际使用比较少
    - 开发运维体系（DevOps）：



微服务发展的历程

- 进程间通讯（Inter-Process Communication）

- - 近端通讯（Local ）

  - - 文件（File)
    - 消息队列(Message)
    - 共享内存（Shared Memory）
    - 其他（Pipe、MMF）

  - 远端通讯（ Remote ）

  - - 套接字（Socket）
    - 远程过程调用（RPC）:rest,rmi等

- 分布式计算（ Distributed Computing）

- - 模型（Model）

  - - 客户端-服务端（Client-Server）
    - 点对点（Peer To Peer）

- - 分类（Type）

  - - 同步（ Synchronous ）
    - 异步（ Asynchronous ）

  - 通讯协议（Communication Protocol）

  - - HTTP（ Hypertext Transfer Protocol ）
    - 自定义协议（ User-Defined Protocol ）

  - 传输介质（Media）

  - - 文本（Text）
    - 二进制（Binary）

- SOA（ Service-oriented architecture）

- - 层次划分

  - - 消费接口（Consumer Interface）
    - 业务处理（Business Process）
    - 服务组件（Services Component）

  - 技术

  - - SOAP
    - CORBA：很老的东西
    - RMI
    - REST：rest的暴露形式经常是xml和json，这是一个风格
    - ESB：企业服务总线，比较笨重，用的比较少。

- 技术挑战

- - 注册与发现（Registry and Discovery）
  - 路由（Routing）：如为了负载均衡；
  - 可靠性（ Reliability ）：CAP中的A，是否可用
  - 延迟（Latency ）
  - 热点（Hotspot）：负载均衡为了解决热点的问题。
  - 短路（ Circuit Break ）：为了保护
  - 伸缩（Scale）：和容器及机器部署有一定的关系，docker，k8s能解决伸缩的问题。
  - 异步（Async）
  - 监控（Monitoring）
  - 配置（Configuration）：
  - 数据同步（Data Sync）：数据一致性，CAP中最难解决的部分。
  - 安全（Security）



- 元编程（Meta Programming）

  在Java编程语言中，元编程是一种新型的变成模式，目的是减少代码行数，得到事半功倍的效果。

- - 主要模式

  - - 注解驱动（Annotation-Driven）
    - 反射驱动（Reflection-Driven）
    - 表达式驱动（Expression-Driven）
    - Lambda（Java 8 Introduced）
    - Script On JVM（Groovy、JavaScript等）

  -  接口编程（Interface Programming）

  - 又称之为契约编程，在OOP语言中，其契约范围包括方法名称、方法入参（类型和顺序）、方法返回值（类型）以及异常情况等元数据。



platform.spring.io：版本仲裁



spring boot：

- 一个独立的sping 应用，内嵌了tomcat

- 自动配置
- 除了将开发整合，还将运维整合进来了。



- 监控（Monitoring And Control）
  - DevOps  =  Dev  + Ops  
  - Dev In Spring Boot  = Services 
  - Ops  In Spring Boot = Management
  - Management = Endpoints
  - Endpoints = Monitoring And Control



spring boot actuator

actuator：执行器。

- JVM

- - 通用（General）
  - 线程（Threading）
  - 内存（Memory）
  - 日志（Logging）

- 指标（Metrics）

- - Counter: 计数器, 事件发生的总次数, 会员登录的总次数, cache请求的总次数等.
  - Gauge: 计量表, 某一时刻的数据,如仪表盘上的温度,速度等,网络上如内存,cpu,网络等
  - Meters: 事件发生的频率, 如1分钟,5分钟和15分钟总共发生多少次. 如1分钟我们消费了多少消息, 5分钟发送了多少消息, 如linux下的uptime和top工具.
  - Histogram: 柱状图, 主要是处理事件值的区间值, 如最大,最小,平均, 95%的标准偏差. 通常和处理的时长或个数有关联,如处理某一类任务耗费的事件,单位事件处理的任务数等.
  - Timers: timer是发生频率和柱状图的组合,如QPS,我们要统计请求的频率,同时在统计每次请求的时间等.

为什么要提供：

- devops的使用场景

技巧：

jconsole pid



## Web篇（上）

### 静态 Web 内容

- 基本解释	

​	HTTP 请求内容由Web 服务器文件系统提供，常见静态Web内容如：HTML、CSS、JS、JPEG、Flash等等

- 特点

- - 计算类型：I/O 类型
  - 交互方式：单一
  - 资源内容：相同（基本）
  - 资源路径：物理路径（文件、目录）
  - 请求方法：GET（主要）

- 常见使用场景

- - 信息展示
  - 样式文件（CSS）
  - 脚本文件（JS）
  - 图片（JPEG、GIF）
  - 多媒体（Flash、Movie）
  - 文件下载

- 常见Web服务器

- - Apache HTTP Server:LAMP : 建站常用方式：linux，apache，mysql，php	
  - Nginx
  - Microsoft IIS
  - GWS

- ![image-20190413203831434](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190413203831434.png)

- 思考

​      为什么 Java Web Server 不是常用 Web Server？

- 参考答案

- - 内存占用比较大，且内存分配是一段一段的。

  - - 类型:  Java中都是32位，统一，内存占用会大
    - 分配：完整一段一段分配

  - 垃圾回收

  - - 被动回收
    - 停顿： stop the world，full gc

  - 并发处理

  - - 线程池
    - 线程开销比较大（静态页面请求很多，大量创建线程，消耗大）

- 标准优化技术

- - 资源变化

  - - 相应头：Last-Modified
    - 请求头：If-Modified-Since，如果没有修改，返回403，没有主体，只有相应头。

- - 资源缓存

  - - 相应头：ETag
    - 请求头：If-None-Match

### 动态 Web 内容

- 基本解释

​	与静态 Web 内容不同，请求内容通过服务器计算而来

- 特点

- - 计算类型：混合类型（I/O、CPU、内存等）
  - 交互方式：丰富（用户输入、客户端特征等）
  - 资源内容：多样性
  - 资源路径：逻辑路径（虚拟），路径是不存在的
  - 请求方法：GET、HEAD、PUT、POST等

- 常见使用场景

- - 页面渲染
  - 表单交互（Form）
  - AJAX：异步、javascript、xml
  - XML
  - JSON/JSONP
  - Web Services(SOAP、WSDL)
  - WebSocket

- 流行 Java Web 服务器

- - Servlet 容器（Tomcat、Jetty）
  - 非 Servlet容器（Undertow）

- 请求

- - 资源定位（URI）：
  - 请求协议（Protocol）
  - 请求方法（Method）
  - 请求参数（Parameter）
  - 请求主体（Body）
  - 请求头（Header）
  - Cookie

- 响应

- - 响应头（Header）
  - 响应主体（Body）

- 技术/架构演变

- - CGI（Common Gateway Interface）：python
  - Servlet
  - JSP（Java Server Page)
  - Model 1（JSP + Servlet + JavaBeans）
  - Model 2（MVC）



- Spring Web Mvc

  ![image-20190413215302888](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190413215302888.png)



- Model 2 与 MVC的细微差异

- - Model 2 为面向 Web 服务的架构，MVC 则是面向所有应用场景（比如：PC应用、无线应用）
  - 相对于MVC，Model 2 中 Controller 细化为 Front Controller（FC）和Application Controller（AC)，前者（FC)负责路由后者（AC)，后者（AC）负责跳转视图（View）。



### 模板引擎

JSP：el，SpringEl



Velocity:  spring4.3之后被移除了。



Thymeleaf



性能：jsp > velocity > thymeleaf





### 问答互动

问题一：js、图片封装在jar中性能的问题

Spring boot jar是fat jar，没有压缩率。ThreadExcetePool，每次请求并不会创建新的线程。实际影响性能是因为缓存的问题。

问题二：什么时候用模版语言，什么时候用js渲染？

模版语言是服务器语言，需要服务器计算，js在客户端（浏览器）上执行。服务器处理数据强，前端处理动态效果。

问题三：目前阿里使用spring boot的情况

spring boot + Springcloud + spring Cloud Stream + Spring Cloud DataFlow + Spring Security





## Web篇（中）

### REST理论基础

- 基本概念	

​	REST = RESTful = Representational State Transfer，is one way of providing interoperability between computer systems on the Internet.

- 历史

​       REST 来自于Roy Thomas Fielding 2000年的博士论文 - 《Architectural Styles and the Design of Network-based Software Architectures》

- 类似形式

- - Web Services：WSDL、SOAP

- 架构属性

- - 性能（Performance）
  - 可伸缩性（Scalability）
  - 统一接口简化性（Simplicity of a uniform Interface）
  - 组件可修改性（Modifiability of components）
  - 组件通讯可见性（Visibility of communication between components）
  - 组件可移植性（Portability of component）
  - 可靠性（Reliability）

- 架构约束

- - C/S架构（Client-Server）
  - 无状态（Stateless）
  - 可缓存（Cacheable）
  - 分层系统（Layered System）
  - 按需代码（Code on demand）
  - 统一接口（Uniform interface)

- - - 资源识别（Identification of resources）

    - - URI（Uniform Resource Identifier ）

    - 资源操作（Manipulation of resources through representations）

    - - HTTP verbs：GET、PUT、POST、DELETE
      - Post是幂等的，其他都不是

    - 自描述消息（Self-descriptive messages）

    - - Content-Type:
      - MIME-Type
      - Media Type： application/javascript、 text/html

    - 超媒体（HATEOAS）

    - - Hypermedia As The Engine Of Application State



### REST服务端实践

- Spring Boot REST

  rest返回的并不是只是json，response中也可能是xml、html等。

- - 核心接口

  - - 定义相关

    - - @Controller 只有controller，没有@ResponseBody时被渲染位模版服务。
      - @RestController

    - 映射相关

    - - @RequestMapping
      - @PathVariable

    - 请求相关

    - - @RequestParam：可以进行类型转换
      - @RequestHeader
      - @CookieValue
      - RequestEntity

    - 响应相关

    - - @ResponseBody
      - ResponseEntity



- HATEOAS 

​	在json的基础上增加了服务发现的机制。



- REST文档生成

/mappings

endpoint.enable

endpoint.sensible=false 取消rest稳定的身份验证。





### REST客户端实践

Rest可以适配httpclient





### 问答互动



## Web 篇（下）

### 传统 Servlet 回顾

- 什么是Servlet？

​	Servlet 是一种基于 Java 技术的 Web 组件，用于生成动态内容，由容器管理。类似于其他 Java 技术组件，Servlet 是平台无关的 Java 类组成，并且由 Java Web 服务器加载执行。



- 什么是Servlet容器？

​	Servlet 容器，有时候也称作为 Servlet 引擎，作为Web服务器或应用服务器的一部分。通过请求和响应对话，提供 Web 客户端与 Servlets 交互的能力。容器管理Servlets实例以及它们的生命周期。



- 历史

​	1997年六月，Servlet 1.0 版本发行，最新版本 Servlet 4.0 处于研发状态。



- Servlet 3.0 前时代

- - 服务组件

  - - javax.servlet.Servlet
    - javax.servlet.Filter（since Servlet 2.3）

  - 上下文组件

  - - javax.servlet.ServletContext
    - javax.servlet.http.HttpSession：http协议是无状态协议，需要通过httpSession保存状态。
    - javax.servlet.http.HttpServletRequest
    - javax.servlet.http.HttpServletResponse
    - javax.servlet.http.Cookie（客户端）

- - 配置

  - - javax.servlet.ServletConfig
    - javax.servlet.FilterConfig（since Servlet 2.3 ）

  - 输入输出

  - - javax.servlet.ServletInputStream
    - javax.servlet.ServletOutputStream

  - 异常

  - - javax.servlet.ServletException

- - 事件（since Servlet 2.3 ）

  - - 生命周期类型

    - - javax.servlet.ServletContextEvent
      - javax.servlet.http.HttpSessionEvent
      - java.servlet.ServletRequestEvent

    - 属性上下文类型

    - - javax.servlet.ServletContextAttributeEvent
      - javax.servlet.http.HttpSessionBindingEvent
      - javax.servlet.ServletRequestAttributeEvent

- - 监听器（since Servlet 2.3）

  - - 生命周期类型

    - - javax.servlet.ServletContextListener
      - javax.servlet.http.HttpSessionListener
      - javax.servlet.http.HttpSessionActivationListener
      - javax.servlet.ServletRequestListener

    - 属性上下文类型

    - - javax.servlet.ServletContextAttributeListener
      - javax.servlet.http.HttpSessionAttributeListener
      - javax.servlet.http.HttpSessionBindingListener
      - javax.servlet.ServletRequestAttributeListener

- Servlet 3.0 后时代

- - 组件申明注解: 为spring boot 打成jar包，提供嵌入式容器提供基础

  - - @javax.servlet.annotation.WebServlet
    - @javax.servlet.annotation.WebFilter
    - @javax.servlet.annotation.WebListener
    - @javax.servlet.annotation.ServletSecurity
    - @javax.servlet.annotation.HttpMethodConstraint
    - @javax.servlet.annotation.HttpConstraint

  - 配置申明

  - - @javax.servlet.annotation.WebInitParam

- - 上下文：异步上下文

  - - javax.servlet.AsyncContext

  - 事件

  - - javax.servlet.AsyncEvent

  - 监听器

  - - javax.servlet.AsyncListener

- - Servlet 组件注册

  - - javax.servlet.ServletContext#addServlet()
    - javax.servlet.ServletRegistration

  - Filter 组件注册

  - - javax.servlet.ServletContext#addFilter()
    - javax.servlet.FilterRegistration

  - 监听器注册

  - - javax.servlet.ServletContext#addListener()
    - javax.servlet.AsyncListener

- - 自动装配

  - - 初始器：3.0之后，提供了web.xml外的其他方式

    - - javax.servlet.ServletContainerInitializer

    - 类型过滤

    - - @javax.servlet.annotation.HandlesTypes

- Servlet 生命周期

- - 初始化

  - - 当容器启动或者第一次执行时，Servlet#init(ServletConfig)方法被执行，初始化当前Servlet 。

  - 处理请求

  - - 当HTTP 请求到达容器时，Servlet#service(ServletRequest,ServletResponse) 方法被执行，来处理请求。

  - 销毁

  - - 当容器关闭时，容器将会调用Servlet#destroy 方法被执行，销毁当前Servlet。

- Filter 生命周期

- - 初始化

  - - 当容器启动时，Filter#init(FilterConfig)方法被执行，初始化当前Filter。

  - 处理请求

  - - 当HTTP 请求到达容器时，Filter#doFilter(ServletRequest,ServletResponse,FilterChain) 方法被执行，来拦截请求，在Servlet#service(ServletRequest,ServletResponse) 方法调用前执行。

  - 销毁

  - - 当容器关闭时，容器将会调用Filter#destroy 方法被执行，销毁当前Filter。

### Servlet on Spring Boot 

#### Servlet

- Servlet 组件扫描   : 必须制定包扫描才能生效

  - @org.springframework.boot.web.servlet.ServletComponentScan

  - - 指定包路径扫描

      - String[] value() default {}

      - String[] basePackages() default {}

    - 指定类扫描

      - Class<?>[] basePackageClasses() default {}

- 注解方式注册

  - Servlet 组件

  1. - 扩展 javax.servlet.Servlet

  - - - javax.servlet.http.HttpServlet  :  
      - org.springframework.web.servlet.FrameworkServlet

  - - 标记 @javax.servlet.annotation.WebServlet

  1. 

#### Filter

- Filter 组件

1. 1. 实现 javax.servlet.Filter

- - - org.springframework.web.filter.OncePerRequestFilter

1. 1. 标记 @javax.servlet.annotation.WebFilter

#### Listener

- 监听器组件

1. 实现Listener接口

- Listener接口包括：
  - javax.servlet.http.HttpSessionListener
  - javax.servlet.ServletContextListener
  - javax.servlet.http.HttpSessionActivationListener
  - javax.servlet.ServletRequestListener
  - javax.servlet.ServletContextAttributeListener
  - javax.servlet.http.HttpSessionAttributeListener
  - javax.servlet.http.HttpSessionBindingListener
  - javax.servlet.ServletRequestAttributeListener

2. 标记 @javax.servlet.annotation.WebListener



#### Spring Boot API方式注册(优先使用这种方式)

**Servlet 组件**

1. 扩展 javax.servlet.Servlet

   - javax.servlet.http.HttpServlet

   - org.springframework.web.servlet.FrameworkServlet

2. 组装 Servlet

- - -  Spring Boot 1.4.0 开始支持

    - - org.springframework.boot.web.servlet.ServletRegistrationBean

    - Spring Boot  1.4.0 之前

    - - org.springframework.boot.context.embedded.ServletRegistrationBean

3. 暴露 Spring Bean

   - @Bean

     ```java
     @Bean
     public ServletRegistrationBean servletRegistrationBean() {
       ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean();
     
       servletRegistrationBean.setServlet(new Myservlet2());
       servletRegistrationBean.setName("myservlet2");
       servletRegistrationBean.addUrlMappings("/myservlet2");
       servletRegistrationBean.addInitParameter("myservlet", "myvalue");
     
       return servletRegistrationBean;
     }
     ```

     

**Filter 组件**

1. 实现 javax.servlet.Filter
   - org.springframework.web.filter.OncePerRequestFilter
2. 组装 Filter

- Spring Boot 1.4.0 开始
  -  org.springframework.boot.web.servlet.FilterRegistrationBean

- Spring Boot  1.4.0 之前
  - org.springframework.boot.context.embedded.FilterRegistrationBean

3. 暴露 Spring Bean
   - @Bean

**监听器组件**

1. 实现 Listener

1. 组装 Listener

- Spring Boot 1.4.0 开始
  -  org.springframework.boot.web.servlet.ServletListenerRegistrationBean

- Spring Boot  1.4.0 之前
  - org.springframework.boot.context.embedded.ServletListenerRegistrationBean

3. 暴露 Spring Bean
   - @Bean

### JSP on Spring Boot

- 激活

1. 激活 传统Servlet Web部署
   - Spring Boot 1.4.0 开始
     - org.springframework.boot.web.support.SpringBootServletInitializer
2. 组装 org.springframework.boot.builder.SpringApplicationBuilder
   - builder.source(...)
3. 配置JSP视图

- org.springframework.boot.autoconfigure.web.WebMvcProperties
  - - spring.mvc.view.prefix
    - spring.mvc.view.suffix





### 问答互动





## 嵌入式Web容器

### 传统 Servlet 容器

- Eclipse Jetty
- Apache Tomcat

​	Tomcat是由Apache软件基金会下属的Jakarta项目开发的一个Servlet容器，按照Sun Microsystems提供的技术规范，实现了对Servlet和JavaServer Page（JSP）的支持，并提供了作为Web服务器的一些特有功能，如Tomcat管理和控制平台、安全域管理和Tomcat阀等。由于Tomcat本身也内含了一个HTTP服务器，它也可以被视作一个单独的Web服务器。

标准实现：

- - Servlet
  - JSP
  - Expression Language
  - WebSocket

- Apache Tomcat

- - 核心组件（Components)
  - 静态资源处理
  - 欢迎页面（Welcome file list）
  - JSP处理
  - 类加载（Classloading）
  - 连接器（Connectors）
  - JDBC 数据源（DataSource）
  - JNDI（Java Naming and Directory Interface）

- 核心组件（Components)

- - - Engine

  - - Host
    - **Context**: 类似于ServletContext

- 静态资源处理

- - org.apache.catalina.servlets.DefaultServlet
  - ![image-20190427130228363](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190427130228363.png)

- JSP处理

- - org.apache.jasper.servlet.JspServlet
  - ![image-20190427163341946](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190427163341946.png)

- 欢迎页面（Welcome file list）

- ![image-20190427164040149](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190427164040149.png)

- 类加载（Classloading）: 双亲委派

- - Bootstrap ClassLoader:
  - System ClassLoader
  - Common ClassLoader
  - Webapp ClassLoader

```
org.apache.catalina.loader.ParallelWebappClassLoader
java.net.URLClassLoader
sun.misc.Launcher$AppClassLoader
sun.misc.Launcher$ExtClassLoader
```



- Apache Tomcat

  - - JNDI（Java Naming and Directory Interface）

    - - 基本类型
      - 资源（Resource）
      - 环境（Environment）

  - - JDBC数据元（DataSource）

      ![image-20190508094955626](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190508094955626.png)

  - - 配置方式

    - - context.xml 配置
      - web.xml 配置









### Spring Boot 嵌入式Web容器

- - Servlet 容器

  - - Embedded Jetty
    - Embedded Tomcat

  - 非 Servlet 容器

  - - Undertow

- - Embedded Tomcat

  - - 静态资源处理
    - 欢迎页面（Welcome file list）
    - JSP处理
    - 类加载（Classloading）
    - 连接器（Connectors）

- - 两个关键类：
    - org.springframework.boot.context.embedded.EmbeddedServletContainerCustomizer
    - org.springframework.boot.context.embedded.ConfigurableEmbeddedServletContainer



### 问答互动

1. jndi在实际开发中有什么用？

   目前实际开发中用的比较少。

2. spring boot 跑https怎么搞

   Ssl

3. 内嵌的tomcat搭集群需要怎么配？

   通过loadbalance，nginx去分发请求，无状态。

   传统j2ee通过cluster，分发session

4. springcloud组件都要学吗？

   有些不需要

   

注意：spring boot 内嵌tomcat相当于spring的一个bean，是spring boot 的一部分。



## 数据库JDBC

### 数据源（DataSource）

- 数据源（DataSource）

​	数据源是数据库连接的来源，通过 DataSource 接口获取。

- 类型

- - 通用型数据源（javax.sql.DataSource）

  - - 主要使用场景：通用型数据库，本地事务，一般通过Socket方式连接。

  - 分布式数据源（javax.sql.XADataSource）

  - - 主要使用场景：通用型数据库，分布式事务，一般通过Socket方式连接。

  - 嵌入式数据源（org.springframework.jdbc.datasource.embedded.EmbeddedDatabase）

  - - 主要使用场景：本地文件系统数据库，如：HSQL、H2、Derby等。
    - 枚举：org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType

- 事务（Transaction）

​	事务用于提供数据完整性，并在并发访问下确保数据视图的一致性。

- 重要概念

- - 自动提交模式（Auto-commit mode）

    - 默认情况下，当独立SQL语句执行后，当前事务将会自动提交。

    - 触发时机

      - - DML 执行后
        - DDL  执行后
        - SELECT 查询后结果集关闭后
        - 存储过程执行后（如果执行返回结果集的话，待其关闭后）

  - 事务隔离级别（Transaction isolation levels）

    事务隔离级别决定事务中执行语句中的数据可见性。

    - 事务并发可能的影响

    - - 脏读（dirty reads）
      - 不可重现读（nonrepeatable reads)
      - 幻读（phantom reads）

  - 保护点（Savepoints）

    ​	保护点是在事务中创建，提供细粒度事务控制。

    -  使用场景

    - -  部分事务回滚
      -  选择性释放



dbcp：spring 1.5之后已经淘汰了。



### 事务（Transaction）

### JDBC 4.0（JSR-221）

- 核心接口

- - 驱动接口：java.sql.Drvier
  - 驱动管理：java.sql.DriverManager
  - 数据源：javax.sql.DataSource
  - 数据连接：java.sql.Connection
  - 执行语句：java.sql.Statement
  - 查询结果集：java.sql.ResultSet
  - 元数据接口：java.sql.DatabaseMetaData、java.sql.ResultSetMetaData





### Spring Boot 整合

### 问答互动

1. jsr规范的学习，jcp.org

   jdbc规范，jpa规范，servlet规范，jdk规范，虚拟机规范等等

2. Spring事务底层实现，非Catcher的RuntimeException都会回滚

3. 动态数据源，可以把datasource直接替换掉

4. C3p0,dbcp,druid



CGLIB：既可以面向接口，也可以面向类

动态代理：智能面向接口





## Mybatis

### MyBatis 简介

- 基本概念	

​	MyBatis is a first class persistence framework with support for custom SQL, stored procedures and advanced mappings. MyBatis eliminates almost all of the JDBC code and manual setting of parameters and retrieval of results. MyBatis can use simple XML or Annotations for configuration and map primitives, Map interfaces and Java POJOs (Plain Old Java Objects) to database records.

- 历史

iBatis 是 MyBatis的前生

### MyBatis 配置

- 全局XML配置文件

  - MyBatis 全局XML配置文件包含影响MyBatis行为的设置和属性。

    - - 属性（properties）
      - 设置（settings）
      - 类型别名（typeAliases）
      - 类型处理器（typeHanders）：类型转换
      - 对象工厂（objectFactory）：鸡肋
      - 插件（plugins）：重要，本质是拦截器链，通过拦截器实现
      - 环境（environments）：类似于maven和spring里面的profile。
      - 数据库标识提供商（databaseIdProvider）：mybatis基于sql语句，不同数据库的sql有所区别（不是sql标准）。
      - SQL映射文件（mappers）：通过相对路径、url、interface classes、指定package等方式。

  

​	

- SQL Mapper XML配置文件

​	SQL Mapper XML 配置用于映射SQL 模板语句与Java类型的配置。

- - XML定义

  - - 文档类型约束方式

    - - DTD：Document Type Definition
      - <http://mybatis.org/dtd/mybatis-3-config.dtd>

    - 子元素

    - - properties, settings, typeAliases, typeHandlers, objectFactory, objectWrapperFactory, reflectorFactory, plugins, environments, databaseIdProvider, mappers

  - API接口

  - - org.apache.ibatis.session.Configuration

- SQL Mapper Annotation

  SQL Mapper Annotation是Java Annotation的方式替代SQL Mapper XML配置文件。

### MyBatis 实例讲解

- MyBatis 核心API

- - org.apache.ibatis.session.SqlSessionFactoryBuilder

  - -  SqlSessionFactory 构建器，创建 SqlSessionFactory 实例。通过重载方法build，控制实例行为，其中方法参数如下：

    - - MyBatis全局配置流（java.io.InputStream、java.io.Reader）
      - Mybatis环境名称（environment）
      - Mybatis属性（java.util.Properties）

    - 相关API

    - - 配置构建器：org.apache.ibatis.builder.xml.XMLConfigBuilder
      - MyBatis配置：org.apache.ibatis.session.Configuration
      - MyBatis环境：org.apache.ibatis.mapping.Environment

- MyBatis 核心API

- - org.apache.ibatis.session.SqlSessionFactory

  - -  SqlSession 工厂类，创建 SqlSession 实例，通过重载方法openSession，控制实例特性，其中方法参数如下：

    - - 是否需要自动提交
      - JDBC 数据库连接（java.sql.Connection）
      - Mybatis SQL语句执行器类型（org.apache.ibatis.session.ExecutorType）
      - Mybatis 事务隔离级别（org.apache.ibatis.session.TransactionIsolationLevel）

- MyBatis 核心API

- - org.apache.ibatis.session.SqlSession

  - -  MyBatis SQL 会话对象，类似于JDBC Connection

    - - 职责

      - - 封装java.sql.Connection
        - 屏蔽java.sql.Statement（以及派生接口）的细节
        - 映射java.sql.ResultSet 到Java类型
        - 事务控制
        - 缓存
        - 代理映射（Mapper）

- MyBatis Generator

- - 资源地址：http://www.mybatis.org/generator/index.html

### MyBatis 自动化工具

### MyBatis Spring Boot 整合



### 问答互动

1. Domain 和 entity的区别：domain没有id，entity有id。



### 待解决问题

1. mybatis的连接池和引用第三方连接池有什么区别？有什么方法可以避免在application.properties中定义spring.datasource.url等属性？

   Spring boot使用mybatis后就不需要mybatis-config文件了，所有的属性都可以通过MybatisProperties类所显示的去配置。

2. 





## JPA

### Java Persistence API

- 介绍	

​	JPA 1.0  整合查询语言（Query）和对象关系映射（ORM）元数据定义。

​	JPA 2.0 在 1.0 的基础上，增加 Criteria 查询、元数据API以及校验支持。

- 历史

- - 2009 年 JPA 2.0 最终规范发布（JSR-317）

  - 2006 年 JPA 1.0 最终规范发布（JSR-220）

  - -  EJB 3.0 的子规范

- 实体（Entities）	

​	轻量级持久化域（Domain）对象。

- 实体类（Entity Class）

  实体类可能利用辅助类或者用于表示状态。

- - 约束

  - - 实体类必须使用@Entity标注或者XML描述
    - 实体类至少包含一个默认构造器，并且构造器必须是public 或者 protected
    - 实体类必须是顶级类，不能是枚举或者接口
    - 实体类禁止是final类
    - 实体支持继承、多态关联以及多态查询

- 体持久字段和属性	

​	实体持久状态由字段（Fields）或者属性（Properties），字段即实例的属性或变量，属性则是JavaBeans实例的setter或getter方法。

​	实例属性的访问性必须是private、protected或者包可见，属性的可见性必须是public或者protected。

​	字段和属性可能是单一类型值或集合类型值。

- 持久字段和属性类型

- - 原生类型
  - Java Serializable类型
  - 自定义类型（实现Serializable接口）
  - 枚举
  - 实体类型（包括集合实体类型）
  - 嵌入类型

- 字段和属性访问类型（Access Type）

- - 默认访问类型

  - - 非transient 或者@Transient字段:   瞬间，不需要持久化。
    - 非@Transient 属性

  - 显示访问类型

  - - 注解类型

    - - 实体类
      - 映射超类
      - 嵌套类

    - 注解

    - - @Access(AccessType.FIELD) 字段
      - @Access(AccessType.PROPERTY) 属性

- 实体主键（Primary Key）

​	每个实体必须存在主键，主键必须定义在实体类。

- - 简单主键

  - - @Id

  - 复合主键

  - - @EmbeddedId
    - @IdClass

- 实体关系

​	实体关系可能一对一、一对多、多对一或多对多，这些关系是多态性的，可以是单向或者双向。

- - 注解表述方式

  - - @OneToOne
    - @OneToMany
    - @ManyToOne
    - @ManyToMany

  - XML表述方式

  - - @EmbeddedId
    - @IdClass

- 实体双向关系

​	实体双向关系是指两实体之间不仅存在拥有方（owning），也存在倒转方（inverse）。主方决定了更新级联关系到数据库。

- - 规则

  - - 倒转必须通过@OneToOne、@OneToMany或者@ManyToMany中的mappedBy属性方法关联到拥有方的字段或者属性。
    - 一对多、多对一双向关系中的多方必须是主方，因此@ManyToOne 注解不能指定mappedBy属性方法。
    - 双向一对一关系中，主方相当于包含外键的一方。
    - 双向多对多关系中，任何一方可能是拥有方。



- 实体双向关系：一对一（OneToOne）

- - 假设：

  - - 实体A引用单个实体B的实例
    - 实体B引用单个实体A的实例
    - 实体A在关系中处于拥有方

  - 默认映射

  - - 实体A被映射到数据表A
    - 实体B被映射到数据表B
    - 表A包含一个外键关联B

  - 举例：客户（Customer）与信用卡（Credit Card）的关系

- 实体双向关系：多对一（ManyToOne）/一对多（OneToMany）

- - 假设：

  - - 实体A引用单个实体B的实例
    - 实体B引用多个实体A的实例
    - 实体A在关系中处于拥有方

  - 默认映射

  - - 实体A被映射到数据表A
    - 实体B被映射到数据表B
    - 表A包含一个外键关联B

  - 举例：店铺（Store）与客户（Customer）的关系

- 实体双向关系：多对多（ManyToMany）

- - 假设：
  - - 实体A引用多个实体B的实例
    - 实体B引用多个实体A的实例
    - 实体A在关系中处于拥有方
  - 默认映射
  - - 实体A被映射到数据表A
    - 实体B被映射到数据表B
    - 存在一个名为A_B的关联表（拥有方表名为前缀），其中包含两个外键列，一列关联表A的主键，另外一列关联表B的主键。
  - 举例：客户（Customer）与图书（图书）的关系

- 实体继承（Inheritance）

​	实体可继承其他实体。实体之间支持继承、多态关联、多态查询



- - 继承方式：

  - - 继承抽象实体类

    - - @Inheritance
      - 

    - 继承已映射父类型

    - - @MappedSuperclass
      - @AssociationOverride

    - 继承非实体类型

- 实体操作（Operations）

- - 实体管理器：

  - - EntityManager

  - ![image-20190518163754956](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190518163754956.png)



- 实体实例生命周期（Life Cycle）

- - 创建
  - 持久化
  - 移除
  - 同步到数据库
  - 刷新实例
  - 淘汰

- 持久化上下文使用期限（Persistence Context Lifetime）

- - 类型

  - - 事务类型（默认）:必须要十五

    - 扩展类型：集成容器的事务情况

      

  - 阶段

  - - 事务提交阶段

    - - 事务类型：实体状态->脱管
      - 扩展类型：实体状态->继续维持

    - 事务回滚阶段

    - - 实体状态->脱管

- 实体监听器和回调方法

- - 实体监听器

  - - @EntityListeners

  - 回调方法

  - - @PrePersist
    - @PostPersist
    - @PreRemove
    - @PostRemove
    - @PreUpdate
    - @PostUpdate
    - @PostLoad

- 缓存（Caching）
- 查询API（Query API）
- Criteria API
- 元模型API（Metamodel API）



- Spring Data Repository

  现代写代码很少用dao了，repository既有可能来自sql，也有可能nosql，有可能不是来自db。

- - 核心接口

  - - Repository
    - CrudRepository
    - JpaRepository
    - @NoRepositoryBean

  - 激活JPA Repository, 继承**SimpleJpaRepository**

  - - @EnableJpaRepositories

### Spring Data JPA





### JPA Spring Boot 整合







### 问答互动

1. jdk和cglib动态代理都要使用，改怎么办？

   自定义实现ProxyFactoryBean

2. 关系型数据库是处理比较复杂的关系性数据。目前国内用mybatis比较多，国外用jpa比较多。jpa是个规范，mybatis是一个框架。

3. domain和entity的区别？

   domain基本可以分为两种，一种是富domain，包含状态和操作。贫domain基本就是pojo，javaBeans

   entity=JavaBeans + Id 

   domain更关注于描述逻辑状态，entity更强调持久化。

4. Jpa、mongo、redis都被spring data给封装了。

5. jpa的情况下，sql怎么优化？

   如果jpa使用了太多的Native SQL，那么又有什么必要使用jpa呢，如果通过面相对象的方式处理所有议题，其实也是有问题的。比如懒加载，数据变化了怎么办？

   

6. 



## NoSQL

### NoSQL

- 名词解释	

​	NoSQL ，通常表示NoSQL数据库，提供一种存储和获取数据的机制。这些数据是模型化，而关系型数据库中的非扁平关系。

- 多种解读

- - NoSQL = non SQL
  - NoSQL = non relational
  - NoSQL = not only SQL



注：SQL（Structured Query Language），Since 1970s



- 使用目的	

- - 简化设计
  - 简单的水平伸缩
  - 细粒度控制

- 数据结构

- - 键值对（key-value） - Redis
  - 宽列（wide column） - Hbase
  - 图（graph） - Neo4j
  - 文档（document）- Mongodb

- 优势（相对于关系型数据库）	

- - 操作执行更快
  - 数据结构更具有弹性

- 不足

- - 一致性（consistency）

- 相关原理

- - CAP ：一致性（Consistency）、可靠性（Availability）、区块容错（Partition tolerance）
  - ACID：原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）



### Elasticsearch

- Elasticsearch

高度伸缩的开源全文本搜索和分析引擎，能够快速地存储、搜索、分析大数据，并且接近于实时（real time）。通常用于复杂的搜索功能和需求。

- 常见使用场景

- - 文本搜索
  - 日志收集
  - 数据分析

- 基本概念

- - 近乎实时（NRT）**正常情况下，构建索引（写）到结果可查询（读）在一秒间。**

- - 集群（Cluster）
  - 一个或多个节点，集群持有整体数据，提供跨节点的索引和搜索能力。集群的名称必须是唯一的。

- - 节点（Node）
  - 指单个实例，也是集群的组成单元，存储数据、参与集群的索引和搜索。其名称也是唯一的。

- - 文档（Document）
  - 数据存储基本单元。文档信息能被索引，并且采用JSON的格式传递。

- - 索引（Index）
  - 一些存在相似特征的文档集合。索引通过名称来区分（必须小写），并且该名称用于关联那些为文档构建索引、查询、更新、删除操作的索引。

- - 类型（Type）
  - 同一个索引可被定义多个类型。类型属于索引的逻辑分类。正如博文如文档，标签为类型。

- - Shards
  - 一个索引可能存储大量的数据，这些数据可能超出了单节点的限制。为了解决这个问题，需要分而治之，这种技术即是 Shards。

- - - 目的：

    - - 水平伸缩（Horizontally scale）
      - 并行操作，增加吞吐量

  - Replicas

  - 网络环境中，单点服务可能随时存在不可用，因此需要提供failover的机制，因此，Replicas的方式能够避免服务持续不可用。

- - - 目的：

    - - 高可用
      - 提高吞吐量

- - 安装

  - - 步骤

- 1. 1. 1. 依赖 Java版本 >= 7

           

        2. 下载 [elasticsearch-2.4.5.tar.gz](https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.4.5/elasticsearch-2.4.5.tar.gz)

- 

- 1. 1. 3. 解压 elasticsearch-2.4.5.tar.gz

     2. 

     3. 4. 跳转至执行目录 elasticsearch-2.4.5/bin

     4. 

     5. 5. 执行 elasticsearch 命令

- 集群（Cluster）

  Elasticsearch 提供了全面并且强大的REST API与集群交互。

- - 健康

  - - API

    - - 集群：/_cat/health?v
      - 节点：/_cat/nodes?v

    - 状态：

    - - green：一切OK
      - yellow：所有数据可用、replicas未分配
      - red：部分数据不可用

- 索引（Index）

- - 索引列表

  - - API：/_cat/indices?v

  - 创建索引

  - - 请求

    - - Method：PUT
      - URL：/segmentfault?pretty

    - 相应

    - - 内容：{"acknowledged":true}

- - 删除索引

  - - 请求

    - - Method：DELETE
      - URL：/segmentfault?pretty

    - 相应

    - - 内容：{"acknowledged":true}

- 

- 文档（Document）

- - 建立

  - - 请求

    - - Method：PUT
      - URL：/segmentfault/l/1500000009957330?pretty
      - Body：{"name":"NoSQL"}

    - 相应

    - - 内容：![image-20190513192234202](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190513192234202.png)

- - 查询

  - - 请求

    - - Method：GET
      - URL：/segmentfault/l/1500000009957330?pretty

    - 相应

    - - 内容：![image-20190513192321319](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190513192321319.png)

- - 更新

  - - 请求

    - - Method：POST
      - URL：/segmentfault/l/1500000009957330?pretty
      - Body：{“name”:“No SQL”}

    - 相应

    - - 内容：![image-20190513192339806](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190513192339806.png)

- - 删除

  - - 请求

    - - Method：DELETE
      - URL：/segmentfault/l/1500000009957330?pretty

    - 相应

    - - 内容：![image-20190513192400666](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190513192400666.png)



- - 批量操作

  - - 请求

    - - Method：POST
      - URL：/segmentfault/l/_bulk?pretty
      - Body：

  - {"index":{"_id":"1500000009957330"}}

  - {"name":"NoSQL"}

  - {"index":{"_id":"1500000009970812"}}

  - {"name":"Caching"}

  - {"index":{"_id":"1500000009971600"}}

  - {"name":"Messaging"}



### Elasticsearch Spring Boot 整合

- Spring Data 整合:spring data是通用的东西，它包括了很多东西。

- - 核心接口

  - - org.springframework.data.repository.Repository
    - org.springframework.data.repository.CrudRepository
    - org.springframework.data.repository.PagingAndSortingRepository
    - @org.springframework.data.repository.NoRepositoryBean

- Spring Data Elasticsearch整合

- - 操作接口

  - - org.springframework.data.elasticsearch.core.ElasticsearchOperations
    - org.springframework.data.elasticsearch.core.ElasticsearchTemplate

  - 功能激活

  - - @org.springframework.data.elasticsearch.repository.config.EnableElasticsearchRepositories

  - 仓储接口

  - - org.springframework.data.elasticsearch.repository.ElasticsearchRepository

1:37:00

### 问答互动





## 缓存

### Java Cache（JSR-107）

- 介绍	

​	缓存是一种久经考验并且显著地提升应用性能以及伸缩性的技术。缓存用作临时存储信息复本，该复本未来可能被再次使用，减少再次加载或创建的成本。



- Java Caching API	

​	为Java 程序提供一种通用方式去创建、读取、更新以及删除缓存中的元素。



- 1.0 规范

- - 发布时间：2013年12月16日



- 非规范目标

- - 资源和内存限制配置

​	尽管许多缓存实现提供了运行时缓存资源限制能力，不过规范并不会定义功能性的配置。

- - 缓存存储和拓扑结构

​	规范没有规定缓存的实现存储或者信息展示。

- - 管理

​	规范没有规定缓存如何管理，定义了程序化配置缓存的机制以及通过Java Management Extensions（JMX）探测缓存的统计信息。

- - 安全

​	规范没有规定缓存内容如何是否安全或者缓存操作如何控制。

- - 外部资源同步

规范没有规定应用或缓存实现如何保持缓存与外部之间的内容同步。



- 核心接口

- - CachingProvider

​	定义构建、配置、获取、管理和控制零个以上CacheManager的机制，应用程序在运行时也可能读取或使用零个以上的CachingProvider。

- - CacheManager

​	定义构建、配置、获取、管理和控制零个以上不重名的Cache的机制。CacheManager 归属单个CacheProvider。

- - Cache

​	一种类似于Map的数据结构，允许Key-Value临时存储。Cache归属单个CacheManager。

- - Entry

​	单个 存储在Cache中的键值对。存储在缓存中的每个Entry存在一个持久时间。

- - ExpiryPolicy
  - 定义Entry的过期策略。

- 存储方式

- - 值存储（Store-By-Value）

​	默认机制，在存储Key和Value前，需要实现创建一份复本数据，并且在读取缓存时，同样返回一份复制数据。一种简单键值复本的实现方式为Java序列化。规范推荐自定义Key和Value类均实现标准的Java 序列化，用户也可以自定义实现。

​	举例：分布式缓存 - Redis



- - 引用存储（Store-By-Reference）

​	可选机制，存储与获取Key和Value的实现通过Java引用。

​	举例：JVM本地缓存 - Guava



- Cache 与 Map 

- - 类似

  - - 缓存值均有关联键来存储
    - 每个值可能仅关联单个键
    - 特别注意key的可变性，可变的key可能会影响键的比较
    - 自定义Key类应该添加合适的Object.hashCode方法 

  - 区别

  - - 缓存的键和值禁止为null
    - 缓存项可能会过期
    - 缓存项可能被移除
    - 缓存支持Compare-And-Swap（CAS）操作
    - 缓存的键和值可能需要某种方式的序列化

- 一致性（Consistency） 

- - 非阻塞锁（lock-free）： volatile，适合作为开关

  - - 保障：无法保证

    - 类型：Happen-Before（HB）

      

  - 乐观锁（optimistic locking）：aomic包中的使用了cas。适合做非阻塞数据交换

  - - 一致性：保证
    - 类型：Compare-And-Swap（CAS）



- - 消极锁（pessimistic locking）：一些原子系列的操作

  - - 一致性：保证
    - 类型：lock、mutex





### Spring Cache

- 介绍

​	从Spring 3.1 开始，Spring Framework 对已有的应用程序增添缓存的支持。类似于事务的支持，在最小影响的代码，缓存抽象允许持续使用各种缓存解决方案。

​	从Spring 4.1开始，缓存抽象对JSR-107注解以及其他自定义操作上得到重要的提升。

- 核心接口

- - org.springframework.cache.CacheManager
  - org.springframework.cache.Cache:可以理解为有名称的map



- 核心注解

- - @Cacheable
  - @CacheEvict
  - @CachePut
  - @Caching
  - @CacheConfig

- 激活缓存

- - @EnableCaching



### Redis Spring Boot 整合





### 问答互动





## 消息

### Java Message Service（JSR-914）

- 介绍	

​	Java Message Service，简称JMS，为Java 程序提供一种通用的方式，来创建、发送、接收以及读取企业消息系统的消息。

- 规范版本

- - JMS 1.0.2b (2001年6月26日)
  - JMS 1.1 (2002年4月12日)
  - JMS 2.0 (2013年5月21，维护状态)



- 面向消息中间件	

​	面向消息中间件（Message Oriented Middleware）是一种支持在分布式系统中发送和接受消息**软件或者硬件**的基础设施。通过非对称平台，MOM让应用模块成为分布式，同时减少了开发跨操作系统和网络接口应用的复杂度。

可以和rpc对比。

- 优势

- - 异步
  - 路由
  - 解耦

- 不足

- - 性能
  - 可靠性
  - 复杂

- JMS 元素

- - JMS 提供方（Provider）：实现JMS 接口的MOM
  - JMS 客户端（Client）：生产或消费消息的应用或进程
  - JMS 生产者（Producer）：创建和发送消息的JMS客户端
  - JMS 消费者（Consumer）：接收消息的JMS客户端
  - JMS 消息    （Message）：JMS客户端之间的传输数据对象
  - JMS 队列    （Queue）：包含待读取消息的准备区域
  - JMS 主题    （Topic）：发布消息的分布机制

- JMS 消息

- - 消息头（Header）
  - 所有消息支持相同的头字段集合，头字段包含客户端和提供方识别和路由消息的数据。

- - 消息属性（Properties）
  - 除标准的头字段以外，提供一种内建机制来添加可选的消息头字段。

- - - 应用特殊属性
    - 标准属性
    - 提供方特殊属性

  - 消息主体（Body）：payload 负载

  - JMS定义了多种消息主题类型，覆盖了主要的消息风格



- JMS 消息头字段（Header Fields）

- - JMSDestination：消息发送目的地
  - JMSDeliveryMode：消息传递模式
  - JMSMessageID：消息ID
  - JMSTimestamp：消息发送时间戳
  - JMSCorrelationID：提供方特殊ID、应用特殊ID和提供方本地字节数组值
  - JMSReplyTo：消息回复地址，说明消息期待回复，可选值
  - JMSRedelivered：消息重投递标识
  - JMSType：消息客户端发送消息时的类型标识
  - JMSExpiration：消息过期
  - JMSPriority：消息优先级

- JMS 消息主体（Body）

​	JMS提供五种消息主体的形式，每种形式通过消息接口定义：

- - StreamMessage
  - 消息整体主体包含流式Java原生值，它是连续地被填充和读取的。

- - MapMessage
  - 消息整体主体包含键值对集合，其中键为字符串，值为Java原生类型。条目访问可被计算器连续地或者名称随机地访问，它的顺序并不一定。

- - TextMessage
  - 消息整体主体包含一个Java String 对象。

- - ObjectMessage
  - 消息整体主体包含一个Serializable 对象，如果需要使用集合对象，确保JDK 1.2或更高。

- - BytesMessage



- JMS 消息确认（Acknowledgment）

​	所有JMS消息支持acknowledge方法的使用，当客户端已规定JMS消息将明确地收到。如果客户端使用了消息自动确认的话，调用acknowledge方法将被忽略。

- JMS 消息模型

- - 点对点模型（Point-To-Point Model ）

- - 发布/订阅模型（Publish/subscribe Model）

### 高级消息队列协议（AMQP）

- 介绍

​	高级消息队列协议（AMQP），全称Advanced Message Queuing Protocol，是一种针对面向消息中间件的开放标准应用层协议，定义了面向消息、队列、路由、可靠性和安全等特性。

- 历史

- - 1.0 协议：2011年10月30日

- 实现

- - Apache ActiveMQ
  - Pivotal RabbitMQ





### Apache Kafka

- 介绍

​	Kafka 是一种分布式流式计算平台，用于构建实时的数据流水线以及流式计算应用，它是水平伸缩的、容错的、极其快速，并且运行在成千上万的公司的生产环境。

- 三大关键能力

- - 发使用**容错的方式**来存储流式记录
  - 发布和订阅流式记录，类似于消息队列或企业消息系统
  - 处理流式记录

- 优势

- - 构建实时的流式计算数据流水线
  - 构建实时的流式计算应用

- 基本概念

- - Kafka是集群式运行
  - Kafka集群分类存储流式记录，这种分类称为主题（Topic）
  - 每条记录包含键（Key）、值（Value）、以及时间戳（Timestamp）

- 四类核心API

- - 生产者 API
  - 能够让应用发布流式记录到一个或多个主题

- - 消费者 API
  - 能够让应用订阅流式记录到一个或多个主题，并且处理他们

- - 流式 API
  - 能够让应用充当流式处理器，消费一个或多个主题的输入流，生产一个或多个主题的输出流，并且高效地将输入流转化成输出流

- - 连接器 API
  - 构建生产者和消费者之间连接



### Kafka Spring Boot整合







### 问答互动





## 验证

### Apache commons-validator

apache commons实现了很多工具类，值得学习。

使用单例模式。

- 介绍	

​	A common issue when receiving data either electronically or from user input is verifying the integrity of the data. This work is repetitive and becomes even more complicated when different sets of validation rules need to be applied to the same set of data based on locale. Error messages may also vary by locale. This package addresses some of these issues to speed development and maintenance of validation rules.

- 最新版本

- - commons-validator 1.6

  - - 依赖 JDK 1.6

- 功能特性	

- - 可配置的校验引擎
  - 可重用的原生校验手段

- 第三方依赖

- - commons-beanutils:1.9.2
  - commons-digester:1.8.1
  - commons-logging:1.2
  - commons-collections:3.2.2

- 设计模式

- - 单例模式（GoF23）
  - 校验器模式

- 验证器类型	

- - Date 与 Time 校验器
  - 数值校验器
  - 正则表达式校验器
  - ISBN校验器
  - IP 地址校验器
  - 邮件地址校验器
  - URL 校验器
  - 域名校验器



- Date 与 Time 校验器

- - API

  - - org.apache.commons.validator.routines.DateValidator
    - org.apache.commons.validator.routines.CalendarValidator
    - org.apache.commons.validator.routines.TimeValidator

  - 使用场景

  - - 校验
    - 格式化
    - 时区
    - 比较

- 数值校验器（Numberic）

- - API

  - - org.apache.commons.validator.routines.ByteValidator
    - org.apache.commons.validator.routines.ShortValidator
    - org.apache.commons.validator.routines.IntegerValidator
    - org.apache.commons.validator.routines.LongValidator
    - org.apache.commons.validator.routines.FloatValidator
    - org.apache.commons.validator.routines.DoubleValidator
    - org.apache.commons.validator.routines.BigIntegerValidator
    - org.apache.commons.validator.routines.BigDecimalValidator
    - org.apache.commons.validator.routines.CurrencyValidator



- 其他校验器

- - API

  - - org.apache.commons.validator.routines.RegexValidator
    - org.apache.commons.validator.routines.CodeValidator
    - org.apache.commons.validator.routines.ISBNValidator
    - org.apache.commons.validator.routines.InetAddressValidator
    - org.apache.commons.validator.routines.EmailValidator
    - org.apache.commons.validator.routines.UrlValidator
    - org.apache.commons.validator.routines.DomainNameValidator





​	

### Spring Validator

用的比较少。

- 介绍

​	Spring Framework 提供了用于校验对象的Validator 接口，在校验过程中，与 Errors 对象配合。校验器可以通过Errors 对象报告校验失败的信息。

- 接口定义

  ![image-20190530161915418](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190530161915418.png)

- 实现模式

- - 实现 org.springframework.validation.Validator接口

  - 实现 supports 方法判断是否需要支持校验

  - - 当方法返回 false时，视作不予校验

  - 实现 validate 方法，判断 target 参数是否校验合法

  - - 当校验非法时，通过Errors 对象返回

  - 实现 MessageCodesResolver 或重用框架实现，完成错误信息编码化

​	

-  辅助

- - 使用工具类 ValidationUtils ， 辅助通用校验逻辑





### Bean Validation 1.0（JSR-303）

- 介绍

​	Java API for JavaBean validation in Java EE and Java SE. The technical objective of this work is to provide a class level constraint declaration and validation facility for the Java application developer, as well as a constraint metadata repository and query API.

​	

-  规范版本

- - 2009.10.12 Bean Validation 1.0（JSR-303）
  - 2013.04.10 Bean Validation 1.1（JSR-349）
  - 2017.06.21 Bean Validation 2.0.0.CR1（JSR-380）



- 常用注解

- - @Valid
  - @NotNull
  - @Null
  - @Size
  - @Min
  - @Max





### Validation Spring Boot 整合







### 问答互动

1. 国际化的类：LocaleContextHolder





## WebSocket

### WebSocket 协议

- 现实中的挑战	

- - 当今 Web 应用要求可靠、近乎零延迟的实时通讯

  - 不仅需要广播，还需要双向通讯

  - 现实中的案例

  - - 股票交易系统
    - 社交网络
    - 线上游戏

  - 如何通过Web实现企业级实时 CS应用呢？

- 桌面系统中的全双工 C/S 架构

![image-20190602165608387](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190602165608387.png)

- 为什么不用HTTP	

- - HTTP 起初被设计为文本传输协议
  - HTTP 是半双工，一次单向通讯
  - HTTP的限制，实现实时、双向Web通讯非常麻烦
  - HTTP 请求头和响应头信息造成不必要的通讯负载

- HTTP 曾付出的努力	

- - AJAX（Asynchronous JavaScript and XML）
  - Polling
  - Long Polling
  - Stream



- - AJAX（Asynchronous JavaScript and XML）

  - - 特征

    - - 页面修改无需整体刷新
      - 用户体验上，感觉低延迟

    - 不足

    - - 仍采用拉的模式
      - 无法避免HTTP头负载的问题

- - Polling

  - - 特征

    - - 接近于实时
      - 使用Ajax 应用模拟实施通讯
      - 浏览器发送规律的间隔的请求
      - ![image-20190602170745639](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190602170745639.png)

- - - 不足

    - - 在消息低频的情况，
      - 太多的连接没必要地被打开和关闭

- - Long Polling

  - - 特征

    - - 又称为异步 Polling
      - 浏览器发送请求到服务器，
      - 服务器保持请求开打一段时间

      - ![image-20190602170833269](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190602170833269.png)

- - - 不足

    - - HTTP 头通常造成大量的网络堵塞
      - 消息高频情况，导致连续的拉循环

- - Stream

  - - 特征

    - - 效率更高
      - ![image-20190602170909299](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190602170909299.png)

- - - 不足

    - - 时常问题频发
      - 代理和防火墙可能引起的复杂性
      - 响应必须周期性的构建和flush
      - 浏览器跨域连接限制



- - Comet

    tomcat8.5以后把这个移除了。

  - ​	一种长期持有的 HTTP 请求Web 应用模型，允许Web 服务器向浏览器推送数据。

  - ​	Comet 是一个整体的术语（Umbrella term），包括多种技术完成交互。包括 Ajax Push、Reverse Ajax、 Two-way-web、HTTP Streaming and HTTP server push





### Java WebSocket API（JSR-356）

- 介绍	

​	WebSocket 是一种通讯协议，通过单个TCP连接提供完全多工（full-duplex）通讯管道。WebScoket 协议于2011年被IETF标准化，作为 RFC 6455，同时 Web IDL中的 WebSocket API 正在被W3C 标准化。

​	WebSocket 被 Web浏览器与Web服务器实现，然而它能用于任何客户端或服务器应用。WebSocket 协议是基于TCP的独立协议。



- ​	历史	

​	WebSocket 首次引述于 HTML 5 规范中的 TCPConnection，在2008年6月，由Michael Carter 领导一系列的讨论，得出协议的第一个版本。

​	在WebSocket协议被多种浏览器默认激活并且投入使用后，于2011年12月，最终编入RFC。

注：

- IETF ：Internet Engineering Task Force，一个开放的组织，致力于开发和提升因特网标准。

- RFC：Request for Comments，一种IETF发行类型。



- WebSocket 实现浏览器

- - IE 10+
  - Firefox（PC、Android） 11+
  - Chrome（PC、Mobile） 16+
  - Safari（Mac、iOS） 6+
  - Opera （PC、Mobile）12.10+



- 协议握手

​	为建立一个WebSocket 连接，客户端发送WebSocket 握手请求，服务器返回握手的响应。

客户端请求：

```
GET /text HTTP/1.1Upgrade: WebSocketConnection: Upgrade

Host: www.example.com

Origin: http://example.com

WebSocket-Protocol: sample
```

服务端相应：

```
HTTP/1.1 101 WebSocket Protocol Handshake
Upgrade: WebSocket
Connection: Upgrade
WebSocket-Origin: http://example.com
WebSocket-Location: ws://example.com/demo
WebSocket-Protocol: sample
```



- 介绍

​	开发 WebSocket 的Java API 集合

- 版本：Java WebSocket API 1.1（JSR-356）

- 术语

- - 端点（Endpoint）
  - 连接（Connection）
  - 对点（Peer）
  - 会话（Session）
  - 客户端端点、服务器端点

- 端点生命周期（Endpoint Lifecycle）

- - 打开连接

  - - Endpoint#onOpen(Session,EndpointConfig)
    - @OnOpen

  - 关闭连接

  - - Endpoint#onClose(Session,CloseReason)
    - @OnClose

  - 错误

  - - Endpoint#onError(Session,Throwable)
    - @OnError

- 会话（Sessions）

- - API：javax.websocket.Session

  - 接受消息：javax.websocket.MessageHandler

  - - 部分
    - 整体

  - 发送消息：javax.websocket.RemoteEndpoint.Basic



- 配置（Configuration）

- - 服务端配置（javax.websocket.ServerEndpointConfig）

  - - URI 映射
    - 子协议协商
    - 扩展点修改
    - Origin检测
    - 握手修改
    - 自定义端点创建

  - 客户端配置（javax.websocket.ClientEndpointConfig）

  - - 子协议
    - 扩展点
    - 客户端配置修改

- 部署（Deployment）

- - 应用部署到Web 容器

  - - WEB-INF/classes
    - WEB-INF/lib

  - 应用部署到独立WebSocket 服务器

  - - javax.websocket.server.ServerApplicationConfig

  - 编程方式

  - - javax.websocket.server.ServerContainer



### Spring WebSocket 抽象



### WebSocket Spring Boot 整合



### 问答互动

1. websocket是长连接

2. netty和websocket比较

   基本上比较类似，无非是关注的事件。netty提供的更细致。

3. websocket案例：聊天室、股票交易、在线游戏。

4. websocket的高可用依赖于底层的容器（NIO的实现），以及处理逻辑。





## WebService

### Web Services

- Web Services	

​	又称之为 Web Service，是一种设计通过网络来支持相互协作的机器间交互的软件系统。它拥有被机器可处理的格式所描述的接口（如：WSDL），规定使用 SOAP消息的方式与其他系统交互，典型地以HTTP传输、XML序列化以及联合其他Web 相关标准。

— W3C, Web Services Glossary



Spring Web Services (Spring-WS) is a product of the Spring community focused on creating document-driven Web services.



- 标准整合方式

- - XML
  - SOAP
  - WSDL
  - UDDI



- SOAP	

​	Simple Object Access Protocol，一种在计算机网络中实现 Web Services的交换结构化信息的协议规范，它的目的在于促使可扩展性、中立性以及独立性。SOAP使用XML 信息集合作为它的消息格式，并且依赖于应用层协议，最常见的HTTP或者SMTP，用于消息协商和传输。



- 主要特性

- - 可扩张性：安全和Web Services寻址
  - 中立性：可操作在任何应用层通讯协议上，如：HTTP、SMTP、TCP等
  - 独立性：允许使用任何编程模型



- SOAP 数据封装	

- - SOAP 消息：在SOAP节点之间表达交换的信息

- - SOAP 信封：鉴定SOAP 消息的XML封装元素
  - SOAP 头块：SOAP 头的基本单元
  - SOAP 头：一个或多个SOAP 头块集合
  - SOAP 主体：交给接收端包含消息的主体
  - SOAP 故障：SOAP 节点故障时处理消息

- ![image-20190603123609373](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190603123609373.png)



- SOAP 请求示例	

  ![image-20190603123916084](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190603123916084.png)

- SOAP 响应示例

  ![image-20190603124127094](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190603124127094.png)

  

  

  - WSDL

  ​	WSDL 指网络服务描述语言 (Web Services Description Language)。WSDL 是一种使用 XML 编写的文档。这种文档可描述某个 Web service。它可规定服务的位置，以及此服务提供的操作（或方法）。	





### JAX-RS（JSR-224）



### Web Serices Metadata（JSR-181）



### WebServices Spring Boot 整合

- 问答互动

1. RPC和REST的区别

   rest也是一种rpc，不过它是一种偏轻量级的rpc，微服务里面会经常用到rest，soa里面用到WebService。WebService目前已经逐步被Rest替代了。WebService和Rest多用在异构系统。如Java-》PHP。Binary RPC多用于同构系统，比如Thirf。

2. RPC和WebService的区别？

   WebService的前身是XML-RPC，也是RPC的一种，不过是透明的，文本传输方式。






## 安全

### 客户端安全

- CSRF

​	跨站请求伪造（英语：Cross-site request forgery），也被称为one-click attack或者session riding，通常缩写为CSRF 或者XSRF， 是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。

- 术语解释

- - CSRF Token

​	服务端为客户端生成令牌，该令牌将用于请求合法性校验，一般通过请求头或请求参数传递到服务端

- - CSRF Token 仓库

​	服务端组件，用于从请求加载或生成 CSRF Token。

​	Spring Security 提供了**Cookie** 和 **HttpSession** 两种实现。

- - CSRF 请求校验匹配器

服务端组件，用于判断请求是否需要CSRF校验

- 防攻逻辑

1. 1. 利用 CSRF Token 仓库 将 HTTP 请求获取 CSRF Token（该过程 可以理解为 Web 服务端针对当前请求获取 CSRF Token）。
   2. 通过 CSRF Token 校验请求匹配器 来判断当前请求是否需要 CSRF Token 校验。如果需要的话，执行第3步，否则，跳过校验。
   3. 先从请求头中获取 CSRF Token 值，如果不存在的话，再向请求参数中 获取。（该过程可以理解为 获取 Web 客户端请求中的 CSRF Token）。

- - - 如果均未获取的话，将会转向错误页面，并且相应头状态码为：403。
    - 如果 CSRF Token 值 获取到的话，执行第4步。

  - ​	将第1步CSRF Token 仓库 获取的 CSRF Token 与 客户端请求中的 CSRF Token 进行比较。

- - - 如果两值相同的话，说明 CSRF Token 校验通过
    - 否则，CSRF Token 检验失败，将会转向错误页面，并且相应头状态码为：403。

- CSRF Token 仓库

- - 框架接口：org.springframework.security.web.csrf.CsrfTokenRepository

  - Cookie 类型（默认）

  - - 实现类：org.springframework.security.web.csrf.CookieCsrfTokenRepository
    - CSRF Token 存储：客户端，Web浏览器 Cookie
    - 有效时间：Web浏览器 会话期间
    - 特别注意：Cookie 方式安全系数相对较低

  - HttpSession 类型

  - - 实现类：org.springframework.security.web.csrf.HttpSessionCsrfTokenRepository
    - CSRF Token 存储：服务端，HttpSession（Servlet 容器）
    - 有效时间：HttpSession 最大不活动时间间隔（#setMaxInactiveInterval(int) ）
    - 特别注意：Servlet 容器需要支持HttpSession复制（分布式HttpSession）

- XSS

  ​	跨站脚本（英语：Cross-site scripting，通常简称为：XSS）是一种网站应用程序的安全 漏洞攻击，是代码注入的一种。它允许恶意用户将代码注入到网页上，其他用户在观看网页时就 会受到影响。这类攻击通常包含了HTML以及用户端脚本语言。

  ​	XSS攻击通常指的是通过利用网页开发时留下的漏洞，通过巧妙的方法注入恶意指令代码到网页， 使用户加载并执行攻击者恶意制造的网页程序。这些恶意网页程序通常是JavaScript， 但实际上也可以包括Java，VBScript，ActiveX，Flash或者甚至是普通的HTML。攻击成功后， 攻击者可能得到更高的权限（如执行一些操作）、私密网页内容、会话和cookie等各种内容。

01:19



### 服务端安全



### Spring Boot Security整合



### 问答互动



### 下期预告





## 日志

### 日志框架

#### Log4j

- 介绍

​	Log4j 是目前最为流行的Java 日志框架之一，虽然已经停滞发展，并逐步被Logback和Log4j 2 等日志框架所替代，可是无法掩饰光辉历程，以及优良的设计理念。

- 背景

​	Almost every large application includes its own logging or tracing API. In compliance with this rule, the E.U. SEMPER project decided to write its own tracing API. This was in early 1996. After countless enhancements, several incarnations and much work that API evolved to become log4j。

\- The Complete log4j Manual

- 结束

​	On August 5, 2015 the Logging Services Project Management Committee announced that Log4j 1.x had reached end of life. 

\- http://logging.apache.org/log4j/1.2/



- 整体架构

  ![image-20190604162213561](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604162213561.png)



- Log4j API

- - 日志对象（org.apache.log4j.Logger）
  - 日志级别（org.apache.log4j.Level）
  - 日志管理器（org.apache.log4j.LogManager）
  - 日志仓储（org.apache.log4j.spi.LoggerRepository）
  - 日志附加器（org.apache.log4j.Appender）
  - 日志过滤器（org.apache.log4j.spi.Filter）
  - 日志格式布局（org.apache.log4j.Layout）
  - 日志事件（org.apache.log4j.LoggingEvent）
  - 日志配置器（org.apache.log4j.spi.Configurator）
  - 日志诊断上下文（org.apache.log4j.NDC、org.apache.log4j.MDC）

​	

- 日志对象（org.apache.log4j.Logger）

​	日志对象（org.apache.log4j.Logger）是最核心的API。

- - 举例说明
  - Logger logger = Logger.getLogger("ROOT");

  - logger.info("message");

- - API 层次
  - ​	-org.apache.log4j.Category

  - ​			-org.apache.log4j.Logger

- - 日志对象（org.apache.log4j.Logger）

    ![image-20190604162526418](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604162526418.png)

- - 日志级别（org.apache.log4j.Level）

- 1. 1. OFF
     2. FATAL
     3. ERROR
     4. INFO
     5. DEBUG
     6. TRACE
     7. ALL

- - - API 层次

- ​	-org.apache.log4j.Priority

- ​			-org.apache.log4j.Level

- - 日志管理器（org.apache.log4j.LogManager）

  - - 主要职责

    - - 初始化默认log4j 配置
      - 维护日志仓储（org.apache.log4j.spi.LoggerRepository）
      - 获取日志对象（org.apache.log4j.Logger）

- - 日志仓储（org.apache.log4j.spi.LoggerRepository）

  - - 主要职责

    - - 管理日志级别阈值（org.apache.log4j.Level）
      - 管理日志对象（org.apache.log4j.Logger）

- - 日志附加器（org.apache.log4j.Appender）

- ​	日志附加器是日志事件（org.apache.log4j.LoggingEvent）具体输出的介质，如：控制台、文件系统、网络套接字等。

- ​	日志附加器（org.apache.log4j.Appender）关联零个或多个日志过滤器（org.apache.log4j.Filter），这些过滤器形成过滤链。

- - - 主要职责

    - - 附加日志事件（org.apache.log4j.LoggingEvent）
      - 关联日志布局（org.apache.log4j.Layout）
      - 关联日志过滤器（org.apache.log4j.Filter）
      - 关联错误处理器（org.apache.log4j.spi.ErrorHandler）

- ![image-20190604163045665](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604163045665.png)

- - 日志附加器（org.apache.log4j.Appender）

  - - 控制台实现：org.apache.log4j.ConsoleAppender

    - 文件实现

    - - 普通方式：org.apache.log4j.FileAppender
      - 滚动方式：org.apache.log4j.RollingFileAppender
      - 每日规定方式：org.apache.log4j.DailyRollingFileAppender

    - 网络实现

    - - Socket方式：org.apache.log4j.net.SocketAppender
      - JMS方式：org.apache.log4j.net.JMSAppender
      - SMTP方式：org.apache.log4j.net.SMTPAppender

    - 异步实现：org.apache.log4j.AsyncAppender

  - ![image-20190604163533029](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604163533029.png)

- - 日志过滤器（org.apache.log4j.spi.Filter）

- ​	日志过滤器用于决策当前日志事件（org.apache.log4j.spi.LoggingEvent）是否需要在执行所关联的日志附加器（org.apache.log4j.Appender）中执行。

- ​	决策结果有三种：

- - - DENY：日志事件跳过日志附加器的执行
    - ACCEPT：日志附加器立即执行日志事件
    - NEUTRAL：跳过当前过滤器，让下一个过滤器决策

  - ![image-20190604163704530](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604163704530.png)

- - 日志格式布局（org.apache.log4j.Layout）

- ​	日志格式布局用于格式化日志事件（org.apache.log4j.spi.LoggingEvent）为可读性的文本内容。

- - - 内建实现

    - - 简单格式：org.apache.log4j.SimpleLayout

      - 模式格式：org.apache.log4j.PatternLayout

      - 提升模式格式：org.apache.log4j.EnhancedPatternLayout

      - HTML格式：org.apache.log4j.HTMLLayout

      - XML格式：org.apache.log4j.xml.XMLLayout

      - TTCC格式：org.apache.log4j.TTCCLayout

        TTCC – **T**ime、**T**hread、**C**ategory、nested diagnostic **C**ontext information

  - **日志事件**（org.apache.log4j.LoggingEvent）

- ​	日志事件是用于承载日志信息的对象，其中包括：

- - - 日志名称
    - 日志内容
    - 日志级别
    - 异常信息（可选）
    - 当前线程名称
    - 时间戳
    - 嵌套诊断上下文（NDC）
    - 映射诊断上下文（MDC）

- - 日志配置器（org.apache.log4j.spi.Configurator）

    日志配置器提供外部配置文件配置log4j行为的API，log4j 内建了两种实现：

  - - Properties 文件方式（org.apache.log4j.PropertyConfigurator）

- - ![image-20190604164442620](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604164442620.png)

  - - XML 文件方式（org.apache.log4j.xml.DOMConfigurator）

      ![image-20190604164452084](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604164452084.png)

- - 日志诊断上下文

- ​	日志诊断上下文作为日志内容的一部分，为其提供辅助性信息，如当前 HTTP 请求 URL。Neil Harrison described this method in the book “Patterns for Logging Diagnostic Messages,” in Pattern Languages of Program Design 3。

- ​	log4j 有两种类型的日志诊断上下文，分别是映射诊断上下文和嵌套诊断上下文：

- - - **映射诊断上下文**（org.apache.log4j.MDC）
    - 嵌套诊断上下文（org.apache.log4j.NDC）

- - 日志诊断上下文

  - - 映射诊断上下文（org.apache.log4j.MDC）
    - 该诊断上下文是以Key-Value的形式存储诊断信息，如：

    - ![image-20190604164643401](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604164643401.png)

    - ![image-20190604164657106](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190604164657106.png)



#### Java logging

- 介绍

​	Java Logging 是Java 标准的日志框架，也称为 Java Logging API，即 JSR 47。从 Java 1.4 版本开始，Java Logging 成为 Java SE的功能模块，其实现类存放在“java.util.logging”包下。

​	很少人使用。

- 特色

- -  Java 天然性
  -  Java Security 整合
  -  Java 国际化/本地化 整合

- 整体架构

  ![image-20190608101346821](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190608101346821.png)

  - Java Logging API

  - - 日志对象（java.util.logging.Logger）
    - 日志级别（java.util.logging.Level）
    - 日志管理器（java.util.logging.LogManager）
    - 日志处理器（java.util.logging.Handler	） : 类似于appender
    - 日志过滤器（java.util.logging.Filter）
    - 日志格式器（java.util.logging.Formatter）
    - 日志记录（java.util.logging.LogRecord）
    - 日志权限（java.util.logging.LoggingPermission）
    - 日志JMX接口（java.util.logging.LoggingMXBean）

#### 日志框架 – Logback

- 介绍

​	Logback 是 Log4j 的替代者，在架构和特征上有着相当提升。

- 重要提升

- - 执行速度更快，内存占用更小
  - Slf4 无缝整合
  - 自动重载配置文件
  - 自动移除老的归档日志:显著特性
  - 自动压缩归档日志文件：显著特性
  - 条件化配置文件

- 解读：https://logback.qos.ch/reasonsToSwitch.html

#### 日志框架 – Log4j2

- 介绍

​	Log4j2 同样也是 Log4j 的替代者，在性能方面提升非常显著。

- 重要提升

- - 执行速度更快，内存占用更小
  - 避免锁
  - 自动重载配置文件
  - 高级过滤
  - 插件架构

- 解读：https://logging.apache.org/log4j/2.x/



### 统一日志 API

- 背景

​	日志框架无论 Log4j 还是 Logback，虽然它们功能完备，但是各自API 相互独立，并且各自为政。当应用系统在团队协作开发时，由于工程人员可能有所偏好，因此，可能导致一套系统可能同时出现多套日志框架。

​	其次，最流行的日志框架基本上基于实现类编程，而非接口编程。因此，暴露一些无关紧要的细节给用户，这种耦合性是没有必要的。

​	诸如此类的原因，开源社区提供统一日志的API框架，最为流行的是：

- - Apache commons-logging

  - - 适配 log4j 和 Java Logging

  - slf4j

  - - 适配 Log4j、Java Logging 和 Logback







### 日志设计模式







### 日志 Spring Boot 整合



### 问答互动

1. 排除依赖方法

   ```
   mvn dependency:tree -Dincludes=groupid:artifiId
   <excludes>
    <exclude>
    </exclude>
   </excludes>
   ```

   

2. 日志记录

   Debug级别记录上下文参数，及输出结果。

   Info级别记录一些操作成功或者失败，类似于黑河结果

   Warn记录CPU，一些阈值达到了，或者不主要的错误输入输出

   Error级别记录一些相关的错误

3. MDC用于 什么场景：

   MDC主要用于上下文传递信息，比如当前操作失败后，当前执行的请求uri或者用户id

4. I/O过于频繁，肯定会影响整体性能，所以合理的Buffer大小比较有必要，或者异步输出。





## 监管

### JMX

- 介绍

​	JMX 全称 Java Management Extensions，技术提供构建分布式、Web、模块化的工具，以及管理和监控设备和应用的动态解决方案。从 Java 5 开始，JMX API 作为 Java 平台的一部分。

- 规范

- - JSR 3：JMX 1.0、JMX 1.1和 1.2（作为Java 5的一部分）
  - JMX 1.4：2006.11.09（作为Java 6的一部分）
  - JSR 255：JMX 2.0
  - JSR 160：JMX Remote API 1.0
  - JSR 262：JMX Remote API for Web Services

- 优势

- - 激活应用管理无需大量投资：仍然需要投资，比如修改代码等。
  - 提供伸缩性管理架构
  - 整合现有的管理解决方案
  - 对现有的Java技术起到杠杆作用
  - 能够扛起未来管理概念
  - 定义面向接口管理

- 架构概况

- - 设备级别（Instrumentation Level）：management resource，被管理的设备或资源
  - 代理级别（Agent Level）
  - 分布式服务级别（Distributed Services Level）
  - 可添加管理协议API

- 架构概况

  ![image-20190608153804839](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190608153804839.png)



- 设备级别

- - 管理Bean（MBeans）

  - - 标准 MBeans
    - 动态 MBeans
    - 开放 MBeans
    - 模型 MBeans

- - 通知模型（Notification Model）
  - MBean 元数据类（MetaData Class）

- 管理Bean（MBeans）

- - 标准 MBeans
  - 设计和实现最为简单，Bean的管理 通过接口方法来描述。MXBean 是一种特殊标准MBean，它使用开放MBean的概念，允许通用管理，同时简化编码。

- - 动态 MBeans
  - 必须实现指定的接口，不过它在运行时能让管理接口发挥最大弹性

- - 开放 MBeans
  - 动态 MBean，提供通用管理所依赖的基本数据类型以及用户友好的自描述信息

- - 模型 MBeans
  - 同样也是动态MBean，在运行时能够完全可配置和自描述，为动态的设备资源提供带有默认行为的MBean泛型类

- 通知模型（Notification Model）

   通知模型允许MBean 广播管理事件，这种操作称之为通知。管理应用和其他对象注册成监听器。

- MBean 元数据类（MetaData Class）

  元信息类包含描述所有MBean 管理接口的组件接口，其中包括：

- - 属性（Attribute）
  - 操作（Operation）
  - 通知（Notification）
  - 构造器（Constructor）

- 代理级别（Agent Level）

- - MBean 服务器
  - MBean 服务器是一个在代理上的MBean的注册器。它仅用作暴露MBean 的管理接口，而非其引用对象。

- - 代理服务
  - 代理服务是在MBean服务器上能够执行已注册MBean的管理操作，其中包括一下代理服务：

- - - 动态类加载
    - 监控
    - 定时器
    - 服务关系





### JMX 核心 API

- 标准 MBeans

- - MBean

  - - 接口的类名称必须以“MBean”为后缀，如MBean 定义为 “XXXMBean”,那么它的实现类名必须是“XXX”

  - MXBean

  - - 接口的类名称必须以“MXBean”为后缀

    - - 举例：java.lang.management.MemoryManagerMXBean

    - 或者接口标记@javax.management.MXBean注解



- 动态 MBeans

- - 管理资源实现 javax.management.DynamicMBean接口

  - - 简化API：javax.management.StandardMBean

- MBean 元信息类（Meta Data Class）
- - - 属性：javax.management.MBeanAttributeInfo
    - 操作：javax.management.MBeanOperationInfo
    - 构造器：javax.management.MBeanConstructorInfo
    - 参数：javax.management.MBeanParameterInfo
    - 通知：javax.management.MBeanNotificationInfo
    - Bean：javax.management.MBeanInfo

- 开放 MBeans

- - 基本数据类型

    ![image-20190608154053098](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190608154053098.png)



- 开放MBean 元信息类（Meta Data Class）

- - 属性：javax.management.openmbean.OpenMBeanAttributeInfo
  - 操作：javax.management.openmbean.OpenMBeanOperationInfo
  - 构造器：javax.management.openmbean.OpenMBeanConstructorInfo
  - 参数：javax.management.openmbean.OpenMBeanParameterInfo
  - 通知：javax.management.openmbean.OpenMBeanNotificationInfo
  - Bean：javax.management.openmbean.OpenMBeanInfo



- 模型 MBeans

- - 参考 JMX 规范

- 代理相关（Agent）

- - MBean 服务器：javax.management.MBeanServer
  - 管理工厂：java.lang.management.ManagementFactory









### JMX 客户端

- JConsole

  

- VisualVM

  

- JMX Remote API（JSR-160）







### JMX Spring Boot 整合

- 核心组件

- - 管理资源

  - - 组件：ManagedResource
    - 注解：@ManagedResource

  - Spring JMX组件：MBeanExportor

  - Spring Boot自动装配：JMXAutoConfiguration







### 问答互动







## 配置

### 外部配置

- 定义

​	Spring Boot 应用的外部配置资源，这些配置资源能够与代码相互配合，避免硬编码 方式，提供应用数据或行为变化的灵活性。

- 类型

- - Properties 文件
  - YAML 文件
  - 环境变量：-D
  - Java 系统属性
  - 命令行



- 加载顺序

- - 热加载
  - 测试
  - 命令行：比如线上想改端口
  - Servlet 参数（ServletConfig、ServletContext）
  - JNDI
  - 先系统属性，再环境变量
  - application-{profile}.properties（先外后内）
  - application.properties（先外后内）
  - @PropertySource
  - SpringApplication.setDefaultProperties



- 配置引用

- - XML 文件
  - Annotation
  - Java Code,  eg.EnvironmentAware



### Profile应用

- 定义

​	Spring Profiles 提供一种区隔应用配置的方式，让应用配置仅在某种特定的“环境”可用。

- - 使用场景



### 核心 API

- 核心API

- - 环境相关

    Eg. Environment

  - 配置相关

  - Profile 相关



### 装配原理



### 问答互动







## 测试

### 单元测试

- 单元测试

  不依赖于远程。本地测试。TDD：老的概念，不一定要测试优先。

  单元测试有一些局限性，无法做到平台统一。

- - 模拟对象

  - - Environment
    - JNDI
    - Servlet API
    - Portlet API

  - 支持类

  - - 通用工具类

    - - 反射： ReflectionTestUtils
      - AOP：AopTestUtils 

    - Spring WebMVC





### Spring 测试

- 集成测试

- - Spring TestContext Framework

  - - 上下文管理
    - 依赖注入
    - 事务管理
    - JDBC 测试支持

  - Spring WebMVC Test Framework

  - - 服务端测试
    - HtmlUnit 集成
    - 客户端测试

- 集成测试

- - 注解（Annotation）

  - - 上下文管理

    - - @ContextConfiguration
      - @ContextHierarchy
      - @WebAppConfiguration
      - @DirtiesContext

    - 事务管理

    - - @BeforeTransaction
      - @AfterTransaction
      - @Commit
      - @Rollback

- 集成测试

- - 注解（Annotation）

  - -  @SpringBootTest

    - - 配置属性
      - Spring Bean配置
      - Web 环境

  - 自动装配测试

  - - JSON
    - Spring WebMVC
    - Data JPA
    - JDBC
    - RestClient





### Spring Boot 测试



### 高端测试框架

- AssertJ
- Mockito

### 问答互动



33 min





## Spring boot 自定义启动器

### Spring Boot Starter

- 说明

​	Spring Boot Starter 又称作为Spring Boot 启动器，是 Spring Boot 框架中最核心的组件，其中可能包含以下组件：

- - 自动装配模块（Autoconfigure Module）

- - 启动器模块（Starter Module）

- 自动装配模块（Autoconfigure Module）

​	自动装配模块包含类库中的每种必要启动单元，它可能也包含配置键的定义，也可能包含任意用于未来自定义已初始化组件的回调接口。

- - 举例说明

  - - 配置键的定义

    - - @ConfigurationProperties

    - 自定义已初始化组件的回调接口

    - - EmbeddedServletContainerCustomizer

- - 自动装配类型

  - - 自动装配 Bean（Auto-Configuration Beans）

    - - Spring 配置（@Configuration）
      - Spring Boot管理上下文配置（@ManagementContextConfiguration）

- - - Spring Boot 组件

    - - FailureAnalysisReporter：错误分析器，自动状态失败等情况时会告知
      - SpringApplicationRunListener：spring boot应用启动监听器
      - AutoConfigurationImportListener

- - **理解自动装配 Bean**（Auto-Configuration Beans）
  - ​	自动装配是由标准Spring @Configuration 实现，结合 Spring 4 的新特性条件判断注解 @Conditional 以及其Spring Boot 派生注解，如：@ConditionalOnClass等。

- - **放置自动装配 Bean**（META-INF/spring.factories）
  - ​	将标记 @Configuration 的Spring Configuration Class 放置在相对于class-path下的 META-INF/spring.factories 文件中，如下所示：

  -  ```
    org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    
    org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
     org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
    ```

- 

- - 前置条件 （@Conditional）

​	Bean 装配前的前置条件，基于Spring 4 @Conditional，判断当前Bean 是否适合或者需要装配。在 Spring Boot 场景下，时常使用其派生注解。

- - Spring Boot 派生前置条件注解

  - - 类条件
    - Bean 条件
    - 配置属性条件
    - 资源条件
    - Web应用条件
    - Spring 表达式条件

- - Spring Boot 派生前置条件注解

  - - 类条件

  - 用于判断指定的Class对象或者其全名称存在与否

- - - - 存在判断：@ConditionalOnClass
      - 缺失判断：@ConditonalOnMissingClass

- - - Bean 条件

  - 用于判断指定的Spring Bean 是否在指定的**Spring 应用上下文**中存在与否

- - - - 存在判断：@ConditionalOnBean
      - 缺失判断：@ConditonalOnMissingBean

- - - 配置属性条件

      用于判断指定的配置属性存在与否、默认是否匹配等

- - - - @ConditionalOnProperty

- - - 资源条件

      用于判断指定的资源是否存在

- - - - @ConditionalOnResource
      - 参考Spring 章节：Resources

- - Spring Boot 派生前置条件注解

  - - Web应用条件

      用于判断当前Spring Boot 应用是否为Web应用

- - - - @ConditionalOnWebApplication

- - - Spring 表达式条件

      用于Spring 表达式评估结果是否成立

- - - - @ConditionalOnExpression
      - 参考Spring 章节：Spring Expression Language (SpEL)

- - 自动装配顺序

  - - 在特定自动装配Class之前

    - - @AutoConfigureBefore

    - 在特定自动装配Class之后

    - - @AutoConfigureAfter ： 如DispatcherServletAutoConfiguration.java

    - 指定顺序

    - - @AutoConfigureOrder

- 启动器模块（Starter Module）

  ​       启动器模块是一个空 JAR 文件，仅提供辅助性依赖管理，这些依赖可能用于自动装配或者其他类库

- - - - 特别注意

    - - 如果自动装配的类库需要其他启动器（starters），管理依赖时，需要将它们一并引入，最好不用使用单一启动器来间接依赖。提供一种合适依赖管理集合可能非常困难，如果其中类库存在非必须依赖时

- - - - 举例说明

      - - Spring Boot 官方启动模块：spring-boot-starter:${version}

- 命名

  Spring Boot Starter 名称也称之为 Spring Boot 命名空间，通常用于Maven artifactId，并且能够传达starter 模块的功能职责。Starter 命名空间可简单地分类为：“官方命名空间”和“自定义命名空间”。

  框架层面，Spring Boot Starter 的命名并未做强制的约束，而是采用推荐规约的形式。

- 命名规约

- - 官方命名空间

  - - 前缀：“spring-boot-starter-” 
    - 模式：spring-boot-starter-{module.name}
    - 举例：spring-boot-starter-web、spring-boot-starter-actuator、spring-boot-starter-jdbc

  - 自定义命名空间（非官方）

  - - 后缀：“-spring-boot-starter” 
    - 模式：{module.name}-spring-boot-starter
    - 举例：acme-spring-boot-starter



### 开发经验

- 命名经验

- - 启动器名称一般由组件名 + “-spring-boot-starter”后缀

  - - 组件名使用名词

    - - 比如，payment

  - 启动器名称尽可能言简意赅

  - - 正例：payment-spring-boot-starter

  - 启动器名称尽可能避免歧义或者模糊

  - - 反例：shopping-spring-boot-starter

  - 启动器名称尽可能避免与官方冲突

  - - 反例：spring-boot-starter-web 或 web-spring-boot-starter

- 自动装配实现经验

- - 自动装配实现类名以“AutoConfiguration”为后缀

  - - 正例：PaymentAutoConfiguration
    - 反例：PaymentConfig

  - 组合前置条件尽可能地严谨（多条件）

  - - 例如 Spring Boot Web MVC应用的判断

    - - 正例：

      - - @ConditionalOnWebApplication
        - @ConditionalOnClass(Servlet.class）
        - @ConditionalOnClass(DispatcherServlet.class）

      - 反例：

      - - @ConditionalOnWebApplication

- - 自动装配实现经验

  - - 组合前置条件判断成本由低到高（多条件）

    - - 假设当@ConditionalOnClass 和 @ConditionalOnBean 同时存在时，@ConditionalOnClass 的判断成本较低，因此放置的位置优先。

      - 例如：

        ```
        @ConditionalOnClass(Car.class)
        
        @ConditionalOnBean(JpaRepository.class)
        
        public class VehicleAutoConfiguration {}
        ```

- - - 自动装配Class 组件依赖与顺序尽可能明确

    - @ConfiguraionProperties Class 应生成元信息

    - - spring-boot-configuration-processor

- 类库依赖经验

- - 明确知晓第三方类库依赖范围
  - 类库依赖应做到最小化原则
  - 通用工具类尽可能使用 Spring 内部，而非 Apache Commons
  - Spring Boot 依赖管理尽量保持<optional> = true，编译时会依赖进来，打包的时候不会依赖。





### Spring Boot 系列总结

![image-20190709162642363](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190709162642363.png)



![image-20190709162711065](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20190709162711065.png)





### Spring Cloud 系列预告





### 问答互动

1. 对技术广度和深度的理解？

   技术的深度决定对技术本质的了解

   技术的广度时帮助架构选型

2. 看spring源码，该从哪入手呢？或者说有什么侧重点？

   - 通过问题入手，问题能够帮助你定位源码
   - 模块化思考，比如spring web mvc和jdbc，他们背后原理分别是Servlet规范和jdbc规范
   - 同位话思考，比如spring框架实现一个功能，首先通过文档学习，分析它的命名套路，可以揣测作者的设计意图。
   - 多参考业界实现，比如apache 的实现
   - 平时多使用spring 内部的类，比如Environment

   









### 



