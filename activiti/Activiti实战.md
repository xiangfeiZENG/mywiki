# Activiti实战

[TOC]

## 认识Activiti

工作流应用广泛，由任务驱动的各种系统中都能看到它的影子，例如，CRM、ERP、BCM、BI、OA等。工作流总是以任务（TASK）的形式驱动人处理业务或者驱动业务系统自动完成作业，有了工作流引擎后，我们不必一直等待其他人的工作进度，直白的说，只需要关心系统首页的代办任务数即可，有系统提醒当前有多少代办任务要处理。

工作流中每一个动作都可以称为活动（Activity），业务流程中最小的组成部分，多个活动，Activities。最后以复数简化的方式标志活动的集合，以此来诠释Activiti与工作流的目的与设计。

BPM（Business Process Management），业务流程管理。

BPMN ( Business Process Modeling Notation)，业务流程建模标注。



activiti实现了bpmn2.0



中台系统中就包含了工作流引擎。



为什么学习工作流引擎

- 工作流引擎在互联网公司快速盛行
- Activiti6.0 技术是Java中高级工程师进阶利器
- 掌握工作流引擎技术可以提升技术架构和业务建模能力



两个框架

- jBPM
- Activiti



## 工作流入门

![image-20190718224523556](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718224523556.png)

![image-20190718224726608](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718224726608.png)



![image-20190718225038378](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718225038378.png)



**工作流**是对工作流程及其各操作步骤之间业务规则的抽象、概括描述。



工作流建模即将工作流程中的工作如何前后组织在一起的逻辑和规则，在计算机中以恰当的模型表达并对其实施计算。



要解决的问题是为了实现某个业务目标，利用计算机在多个参与者之间按某种预定规则自动传递文档，信息或者任务。



工作流管理系统WfMS

- 是处理工作流的电脑软件系统
- 通过计算机技术支持去定义、执行、管理工作流，协调工作流执行过程中工作之间以及群体成员之间的信息交互。
- 工作流是需要依靠工作流管理系统来完成的



计算机支持协同工作CSCW

- 是普遍地研究一个群体如何在计算机的帮助下实现协同工作的
- 工作流属于计算机支持的协同工作（CSCW）的一部分



工作流管理联盟WFMC

- 工作流技术标准化的工业组织，1993年8月成立
- 工作流管理联盟发布了用于管理工作流管理系统之间互操作的工作流参考模型，并相继制定了一系列工业标准



为什么需要工作流：

![image-20190718230010487](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718230010487.png)



![image-20190718230046372](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718230046372.png)



![image-20190718230140996](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718230140996.png)







### 工作流技术选型

Activiti vs Jbpm

![image-20190719222711522](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190719222711522.png)



### Activiti6.0快速体验

activity-app.war

Activity-admin.war



登录界面：http://localhost:8080/activiti-app

默认用户名密码：admin/test

管理界面：http://localhost:8080/activiti-admin

默认用户名密码：admin/admin





## Activiti6.0源码初探

![image-20190720094636522](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190720094636522.png)

### 源码概述

源码获取过程

```
xfei-mac:framework zengxiangfei$ git clone https://github.com/xiangfeiZENG/Activiti.git

xfei-mac:Activiti zengxiangfei$ git checkout -b study activiti-6.0.0

xfei-mac:Activiti zengxiangfei$ mvn clean test-compile
```



核心模块

- module/activiti-engine 核心引擎
- module/activiti-spring spring集成模块
- module/activiti-spring boot springboot集成模块
- module/activiti-rest 对外提供rest api模块
- module/activiti-form-engine 表单引擎模块
- module/activiti-ldap 集成ldap用户模块



![image-20190720101953105](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190720101953105.png)





### 基于源码运行activiti-app

![image-20190720111126853](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190720111126853.png)

过程：

```
xfei-mac:Activiti zengxiangfei$ cd modules/activiti-ui/activiti-app/
xfei-mac:activiti-app zengxiangfei$ mvn clean tomcat7:run

```

![image-20190720112231071](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190720112231071.png)



### hello world之activiti6.0

例子：

![image-20190720113502787](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190720113502787.png)







## Activiti6.0引擎配置

![image-20190718222533261](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718222533261.png)



ProcessEngineConfiguration

- 查找并解析xml配置文件activiti.cfg.xml
- 提供多个静态方法创建配置对象
- 实现几个基于不同场景的子类，配置方式非常灵活



![image-20190721090900286](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190721090900286.png)



maven脚手架的制作

`man clean install`安装脚手架

安装完成后，idea中可以手动添加。



### 数据库配置：

- 缺省配置默认，受用H2内存数据库
- 配置JDBC属性，使用mybatis提供的链接池
- 配置DataSource，可选择第三方实现



配置第三方实现的DataSource

- Druid 为监控而生的数据库连接池，来自阿里.stat,slf4j可以对所有的数据库操作进行日志记录

  ![image-20190721212336989](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190721212336989.png)

- Dbcp 老牌数据源连接池，稳定可靠，tomcat自带

- HikariCP 来自日本的极速数据源连接池，Spring 默选



数据库更新策略

![image-20190721213403553](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190721213403553.png)

值为false：生产，true：开发，create-drop 单元测试。





### 日志记录配置

![image-20190726105314619](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190726105314619.png)



日志门面：程序中直接使用的，spring用的是commons-logging

改变依赖：是为了对历史软件的依赖改变。比如spring用的是commons-logging，通过jcl-over-slf4j改变。



日志记录与MDC

- 配置开启MDC（Mapped Diagnostic Contexts）：记录线程号，当前登录用户，操作订单。把一些上下文数据存储在此线程中，ThreadLocal
  - 默认没有开启，需要手动设置LogMDC.setMDCEnable(true)
  - 配置logback.xml 日志模版 %X{mdcProcessInstanceID} 打印大钱流程id
  - 流程只有在执行过程中出现异常时才会记录MDC信息。



- - 



- 





### 历史记录配置

配置流程历史记录级别

- 配置HistoryLevel
  - none：不记录历史流程，性能高，流程结束后不可读取
  - activiti：归档流程示例和活动实力，流程变量不同步
  - audit：默认值，在activiti基础上同步变量值，保持表单属性
  - full：性能较差，记录所有历史和变量细节变化





### 事件处理及监听器配置-eventlog

配置Event Logging

- **实验性**的事件记录机制，性能影响较大
- 开启默认记录所有数据的变化过程，表记录快速增长
- 日志内容json格式，建议存入mongoDB、Elastic Search



### 事件及监听器配置

- 事件及监听器原理
- 监听器的配置方式
- Activiti的事件监听

![image-20190728114135257](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190728114135257.png)



- 配置Listener
  - eventListeners：监听所有事件派发的通知
  - typedEventListeners：监听指定事件类型的通知
  - activiti:eventListner：流程定义文件监听（非重点）只监听特定流程定义的事件

  还可以通过代码实现：

  ```
  // 增加事件监听
  activitiRule.getRuntimeService().addEventListener(new CustomEventListener());
  
  // 发布监听事件
  activitiRule.getRuntimeService().dispatchEvent(new ActivitiEventImpl(ActivitiEventType.CUSTOM));
  
  ```

  



![image-20190728114619947](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190728114619947.png)



- 相关API
  - ActivitiEnvent：事件对象
  - ActivitiEventListener：监听器
  - ActivitiEventType：事件类型





### 命令拦截器的配置

- 命令模式与责任链模式

  ![image-20190804215050121](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190804215050121.png)

  ![image-20190804215241643](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190804215241643.png)

  ![image-20190804215727025](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190804215727025.png)

- 拦截器的配置方式

  - customPreCommandInterceptors：配置在默认拦截器之前
  - customPostCommandInterceptors：配置在默认拦截器之后
  - commandInvoker：配置最后的执行器

- Activiti的拦截器

  ![image-20190804220056915](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190804220056915.png)

  



### 作业执行器配置

- 作为执行器的配置

  - 定时开启事件（Timer Start Event）
    - timeDate：指定启动时间
    - timeDuration：指定持续时间间隔后执行
    - timeCycle：R5/P1DT1H指定事件段后周期执行

- 配置自定义线程池

- 流程定义定时启动流程

- 相关配置

  - asyncExecutorActivate：激活作业执行器，默认为false
  - asyncExecutorXXX：异步执行器的属性配置
  - asyncExecutor：异步执行器bean

- 自定义线程池ExecutorService

  - corePoolSize：核心线程数，当程序运行时有几个核心运行数量

  - queueCapacity：堵塞队列大小

  - maxPoolSize：最大线程数，当队列都排满，核心线程数都在工作，会创建新的线程，直到最大线程数。

    ![image-20190804230426512](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190804230426512.png)

  



### Activiti与Spring集成

- 集成Spring配置
- 相关配置
  - 添加pom依赖activiti-spring
  - 基于Spring的默认配置activiti-context.xml
  - activiti核心服务注入Spring容器
- 基于Spring对Activiti管理
  - 功能特性
    - 基于Spring事务管理器
    - 定义文件表达式中使用Spring Bean
    - 自动部署资源文件
- 单元测试
  - 添加pom依赖spring-test
  - 辅助测试Rule：ActivitiRule
  - 辅助测试TestCase：SpringActivitiTestCase



- activiti spring关于事务管理
- acrtiviti spring关于表达式



## Activiti6.0核心API

![image-20190718222759996](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718222759996.png)



### RepositoryService

- RepositoryService：主要操作一些静态文件，如配置文件、图片等

  - 管理流程定义文件xml及静态资源的服务

  - 对特定流程的暂停和激活

  - 流程定义启动权限管理

    涉及api

  - 部署构造器DeploymentBuilder

  - 部署文件查询器DeploymentQuery

  - 流程定义文件查询对象ProcessDefinitionQuery

  - 流程部署文件对象Deployment

  - 流程定义对象ProcessDefinition

  - 流程定义的Java格式BpmnModel

  演示相关功能



HistoryService：提供对运行结束的一些流程的查询功能。

ManagementService：对流程引擎的一些基础管理，使用较少。提供了对定时任务的管理。

DynamicBpmService：入侵性比较高的功能，可以动态对流程进行修改，不推荐使用。





### RunTimeService

RuntimeService：主要是对流程进行控制的api，可以用来启动一个流程实例，针对指定的流程实例进行操作，查询。

- 启动流程对流程数据的控制
- 流程实例（ProcessInstance）与执行流（Execution）查询
- 出发流程操作，接收消息和信号



RuntimeService启动流程及变量管理

- 启动流程的常见方式（id，key，message）
- 启动流程可选参数（businessKey，variables，tenantId）
- 变量（variables）的设置和获取



流程实例与执行流（ProcessInstance， Excution）

- 流程实例（ProcessInstance）表示一次工作流业务的数据实体
- 执行流（Execution）表示流程实例中具体的执行路径。
- 流程实例接口继承了执行流



流程触发

- 使用trigger出发ReceiveTask节点
- 触发信号捕获事件signalEventReceived
- 触发消息捕获事件messageEventReceived



流程触发signalEventReceived ，信号跟具体的流程实例没有关系。

- signalCacheEvent



流程触发messageEventReceived

- MessageCatchingEvent



### TaskService 任务管理服务

TaskService：主要管理运行过程中的一些人工任务

- 对用户任务（UserTask）管理和流程的控制
  - Task对象的创建，删除
  - 查询Task，并驱动Task节点完成执行
  - Task相关参数（variable）设置
- 设置用户任务（UserTask）的权限信息（拥有者，候选人，办理人）
  - 候选用户（candidateUser）和候选组（candidateGroup）
  - 指定拥有人（Owner）和办理人（Assignee）
  - 通过claim设置办理人
- 针对用户任务添加任务附件、任务评论和事件记录
  - 任务附件（Attachment）创建于查询
  - 任务评论（Comment）创建于查询



### IdentityService

IdentityService：主要是对用户和用户组的管理

- 管理用户（User）
- 管理用户组（Group）
- 用户与用户组的关系（Membership）

![image-20190806230056641](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190806230056641.png)



### FormService

FormService：解析流程定义中的数据表单，并对表单进行处理。

- 解析流程定义中表单项的配置
- 提交表单的方式驱动用户节点流转,subbmit  提交表单后也会提交流程。
- 获取自定义外部表单key





### HistoryService

- 管理流程实例结束后的历史数据
- 构建历史数据的查询对象
- 根据流程实例Id删除流程历史数据

HistroyService历史数据实体

![image-20190808125105048](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190808125105048.png)

- create【历史数据实体】query
- createNative【历史数据实体】query
- createProcessInstanceHistoryLogQuery：每次只能查出一条记录，一条流程体的所有数据。
- deleteHistoricProcessInstance：输出历史流程实例
- deleteHistoricTaskInstance：删除Task实例



### 其他管理服务ManagementService

- 管理服务ManagementService
- 动态流程定义服务DynamicBpmnService 很少用



- ManagementService

  - Job任务管理

    ![image-20190810180038204](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190810180038204.png)

  - 数据库相关通用操作

  - 执行流程引擎命令（Command）

- 数据库相关操作

  - 查询表结构元数据（TableMetaData）
  - 通用表查询（TablePageQuery）
  - 执行自定义Sql查询（executeCustomSql）

### 异常策略

- ActivitiException

![image-20190810182927193](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190810182927193.png)







## 数据设计与模型映射

![image-20190718222824055](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190718222824055.png)

- MYsql建表语句

  - 核心引擎activiti.mysql.create.engine.sql
  - 历史数据activity.msyql.create.history.sql
  - 身份信息activiti.mysql.create.identity.sql

  

### 通用数据库

![image-20190810184517233](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190810184517233.png)

### 流程定义存储表

![image-20190811154904250](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811154904250.png)

![image-20190811154938748](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811154938748.png)

![image-20190811155017938](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811155017938.png)



### 身份数据表

![image-20190811172824179](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811172824179.png)

![image-20190811172925396](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811172925396.png)

![image-20190811172949131](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811172949131.png)

![image-20190811173041273](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811173041273.png)

![image-20190811173105856](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811173105856.png)



### 运行时流程数据表

![image-20190811173739336](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811173739336.png)

![image-20190811173920321](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811173920321.png)

![image-20190811174021062](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811174021062.png)

![image-20190811174108354](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811174108354.png)

![image-20190811174154002](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811174154002.png)

![image-20190811174215514](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811174215514.png)

参与者信息表：

![image-20190811174350231](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811174350231.png)

事件订阅信息表：

![image-20190811174417531](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811174417531.png)

作业信息表

![image-20190811174447875](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811174447875.png)

![image-20190811174614181](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811174614181.png)





### 历史流程数据表

![image-20190811214929770](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811214929770.png)



![image-20190811215305263](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811215305263.png)

![image-20190811215347649](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811215347649.png)

![image-20190811215445781](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811215445781.png)

![image-20190811215528687](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811215528687.png)









## BPMN2.0规范

定义：Business Process Model and Notation

- 是一套业务流程模型与符号建模标准
- 精准的执行语义来描述元素的操作
- 以XML为载体，以符号可视化业务

![image-20190811221508374](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811221508374.png)



![image-20190811221605054](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811221605054.png)

![image-20190811221702960](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811221702960.png)

![image-20190811222207601](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811222207601.png)

### 流对象（Flow Object）

- 活动（Activities）：User Task，Service Task ….

- 事件（Events）：Start Event,End Event ….

  - 位置分类

    - 开始事件
    - 中间事件/边界事件
    - 结束事件

  - 特性分类

    - 捕获事件（Catching）
    - 抛出事件（Throwing）

  - 事件定义分类

    - 定时事件

      - 指定事件（timeDate）

      - 指定持续时间（timeDuration）

      - 周期执行（timeCycle）

      - 以上timeEventDifinition指定

        ![image-20190811224057403](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190811224057403.png)

    - 错误事件

    - 信号事件

    - 消息事件

-  网关（Gateways）：Exclusive Gateway ….







## 集成Spring Boot2.0

- 初始Spring Boot 独立运行、花繁为简、约定大于配置
- Spring Boot核心功能
- Spring Boot优点

![image-20190816145009378](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190816145009378.png)





Spring boot运维监控：

- actuator，需要management.endpoints.web.exposure.include=* 把所有内容暴露出来。
- 基于http的形式展现信息，需要添加hateoas



spring boot特性：

- 提供丰富的Starter简化Pom配置（简化配置、功能聚合、版本一致、最小依赖）
- 提供上下文智能装配（Autoconfigure）
- 提供可视化运行时信息
- 创建Stand-alone Spring 应用
- 嵌入集成Tomcat等Servlet容器，无需WAR文件
- 将成为微服务标准



启动入口@SpringBootApplication

- SpringBootConfiguration支持Java配置类

- EnableAutoConfiguration开启自动状态功能

  通过spring.factories找到对应类，加载这些类并执行。

- ComponentScan扫描Bean组件



![image-20190816160312949](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190816160312949.png)



![image-20190816160450629](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190816160450629.png)





![image-20190816161538820](/Users/zengxiangfei/Documents/mywiki/activiti/images/image-20190816161538820.png)



### Spring Boot2与Activiti6集成

activiti6发布是还没有spring boot 2

- 升级Activiti6.0依赖Spring boot 版本为2.0.0

  - 升级Spring boot依赖并解决编译错误

  - 更新activiti-spring-boot-starter-basic版本并安装

  - 集成使用Activiti的AutoConfiguration功能

    



## 搭建工作流平台

### 需求分析

- 基于Spring Boot2 与Activiti6.0搭建通用的工作流引擎平台
- 支持流程文件在线设计及部署和维护
- 支持自定义表单与流程定义的集成
- 满足流程运行的用户及权限管理功能
- 管理角度监控流程运行过程和历史数据
- 平台角度方便与第三方系统数据集成



### 技术方案

- 基于Activiti6.0源代码中activiti-ui工程升级开发
- 基于activiti-engine从零开始搭建



### 基于Activiti-ui工程升级搭建平台

- 升级activiti-spring-boot模块依赖版本

- 改造activiti-ui为Spring boot工程

- 基于改造后的activiti-ui创建workflow工程

  

### 平台搭建

开发步骤

- 基于源码Activiti6.0.0版本checkout出新的base分支

  ```
  git checkout -b boot activiti-6.0.0
  修改activiti-spring-boot版本号
  
  ```

  

- 定义activiti-spring-boot、activiti-ui及子模块版本6.0.0-boot2

  ```
  1. 修改版本号
  cd modules/activiti-spring-boot/
  mvn versions:set -DnewVersion=6.0.0-boot2
  问题：
  Project version is inherited from parent. 
  手动指定activiti-spring-boot版本号
      <version>6.0.0</version>
  再执行：修改版本号命令：执行命令的好处是会自动把子模块的依赖也改了。
  
  2. 进行安装
   mvn clean install source:jar -Dmaven.test.skip=true
   遇到版本问题修改即可,可以在root下指定一个activiti.version，将原来依赖project.version的全部改为依赖activiti.version
   
   同样的操作对activiti-ui
  ```

  

- 基于activiti-ui依赖版本6.0.0-boot2运行activiti-app模块

  ```
  mvn clean tomcat7:run
  ```

  

- 改造acctiviti-app为spring boot工程

  1. 引入spring-boot-starter包
  2. 改造activiti-app为spring-boot web项目
  3. 逐步编译，排错，安装

- 升级activiti-ui使用activiti-spring-boot-starter*6.0.0-boot2

  

- 启动运行基于spring boot工程activiti-app

- 升级activiti-spring-boot依赖boot2.0版本：编译，排错，安装

  修改spring boot 版本号，根据编译错误进行修改。

- 重新安装activiti-ui：编译，排错，安装

  

- 启动运行基于spring boot2工程activiti-app

- 创建新的独立工程workflow

- 添加依赖（6.0.0)及配置文件

- 集成web相关资源文件





### 电商业务建模















## 遇到问题



1. 在idea中报错"no deployed process definition found with id 'LeaveProcess'"

   参考文章：<https://community.alfresco.com/thread/218705-no-processes-deployed-with-key>解决。

   具体原因：

   Hi,

   The file extension is changed to ".bpmn" in Activiti Designer 5.9,

   but not in Activiti engine 5.9.

   You should give the classpath with the extnesion of ".bpmn20.xml", like addClasspathResource("diagrams/AlertingProcess.bpmn20.xml").

   You can also open this ".bpmn20.xml" file by clicking that file, and "Open With", and select Activit Designer.

   Below post will help you.

   [http://forums.activiti.org/en/viewtopic.php?f=8&t=3745](https://community.alfresco.com/external-link.jspa?url=http%3A%2F%2Fforums.activiti.org%2Fen%2Fviewtopic.php%3Ff%3D8%26t%3D3745)

   Good luck to you.

   将bpmn文件名后缀改为bpmn20.xml解决该问题。

2. 