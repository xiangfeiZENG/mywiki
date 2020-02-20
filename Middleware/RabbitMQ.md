# RabbitMQ

[TOC]

## 消息中间件概述

- ActiveMQ: 

  Apache，老牌消息中间件，性能一般。丰富的的API。目前用的少

  ![image-20191021222604574](./images/image-20191021222604574.png)

  ![image-20191021222641347](./images/image-20191021222641347.png)

  

- Kafka

  ![image-20191021221207520](./images/image-20191021221207520.png)

  ![image-20191021221343051](./images/image-20191021221343051.png)

- RocketMQ

  ![image-20191021221451637](./images/image-20191021221451637.png)

  ![image-20191021221813897](./images/image-20191021221813897.png)

- RabbitMQ

  ![image-20191021222114379](./images/image-20191021222114379.png)

  ![image-20191021222244926](./images/image-20191021222244926.png)





## RabbitMQ核心概念及AMQP协议

![image-20191021223252510](./images/image-20191021223252510.png)



**互联网大厂为什么选择RabbitMQ？**

- 滴滴、美团、头条、去哪儿、艺龙等
- 开源、性能优秀、稳定性保障
- 提供可靠性消息投递模式（confirm）、返回模式（return）
- 与SpringAMQP完美整合、API丰富。
- 集群模式丰富、表达式配置、HA模式、镜像队列模型
- 保证数据不丢失的前提做到高可靠性、可用性



**RabbitMQ的高性能之道是如何做到的？**

- Erlang语言最初在于交换机领域的架构模式，这样使得RabbitMQ在Broker之间进行数据交换的性能是非常优秀的。
- Erlang的优点：**Erlang有着和原生Socket一样的延迟**



**什么是AMQP高级协议？**

- AMQP全称：Advanced Message Queuing Protocol 高级消息队列协议

  ![image-20191021224130529](./images/image-20191021224130529.png)

- AMQP协议模型

  ![image-20191021224202427](./images/image-20191021224202427.png)





**AMQP核心概念是什么？**

- Server：又称Broker，接收客户端的连接，实现AMQP实体服务
- Connection：连接，应用程序与Broker的网络连接
- Channel：网络通道，几乎所有的操作都在Channel中进行，Channel是进行消息读写的通道。客户端可建立多个Channel，每个Channel代表一个会话任务。（可以类比Session）
- Message：消息，服务器和应用程序之间传送的数据，由Properties和Body组成。Properties可以对消息进行修饰，比如消息的优先级、延迟等高级特性；Body则就是消息体内容。
- Virtual host：虚拟地址，用于进行逻辑隔离，最上层的消息路由。一个Virtual Host里面可以有若干个Exchage和Queue，同一个Virtual Host里面不能有相同名称的Exchange或Queue。
- Exchange：交换机，接收消息，根据路由键转发消息到绑定的队列。
- Binding：Exchange和Queue之间的虚拟连接。binding中可以包含routing key
- Routing key：一个路由规则，虚拟机可用它来确定如何路由一个特定消息。
- Queue：也称为Message Queue，消息队列，**保存消息**并将他们转发给消费者



**RabbitMQ整体架构模型是什么样子的？**

![image-20191021230859885](./images/image-20191021230859885.png)





**RabbitMQ消息是如何流转的？**

![image-20191021231032817](./images/image-20191021231032817.png)



- publish注意要指定Exchange和Routing key



**RabbitMQ安装与使用**

建议大家使用3.6.x



3.7.10



```
准备：
sudo yum install build-essential openssl openssl-devel unixODBC unixODBC-devel make gcc gcc-c++ kernel-devel m4 ncurses-devel tk tc xz

下载：
wget www.rabbitmq.com/releases/erlang/erlang-18.3-1.el7.centos.x86_64.rpm
wget http://repo.iotti.biz/CentOS/7/x86_64/socat-1.7.3.2-5.el7.lux.x86_64.rpm
wget www.rabbitmq.com/releases/rabbitmq-server/v3.6.5/rabbitmq-server-3.6.5-1.noarch.rpm

配置文件：
vim /usr/lib/rabbitmq/lib/rabbitmq_server-3.6.5/ebin/rabbit.app
比如修改密码、配置等等，例如：loopback_users 中的 <<"guest">>,只保留guest
服务启动和停止：
启动 rabbitmq-server start &
停止 rabbitmqctl app_stop

管理插件：rabbitmq-plugins enable rabbitmq_management
访问地址：http://192.168.11.76:15672/
```



- 服务的启动：`rabbitmq-server start &`
- 服务的停止：`rabbitmqctl stop_app`
- 管理插件：`rabbitmq-plugins enable rabbitmq_management`
- 访问地址：http://192.168.11.76:15672/







**RabbitMQ管控台与命令行**

- rabbitmqctl stop_app 关闭应用
- rabbitmqctl start_app 启动应用
- rabbitmqctl status 节点状态
- rabbitmqctl add_user username password 添加用户
- rabbitmqctl list_users 列出所有用户
- rabbitmqctl delete_user username 删除用户
- rabbitmqctl clear_permissions -p vhostpath username 清除用户权限
- rabbitmqctl list_user_permissions username 列出用户权限
- rabbitmqctl change_password username newpassword 修改密码
- rabbitmqctl set_permissions -p vhostpath username `".*"".*"".*"`设置用户权限
- rabbitmqctl list_queues 查看所有队列
- rabbitmqctl -p vhostpath purge_queue blue 清除队列里的消息
- rabbitmqctl reset 移除所有数据，要在rabbitmqctl stop_app之后使用
- rabbitmqctl join_cluster <clusternode> [—ram] : 组成集群命令
- rabbitmqctl cluster_status 查看集群状态
- rabbitmqctl change_cluster_node_type disc | ram 修改集群节点的存储形式
- rabbitmqctl forget_cluster_node [—offline] 忘记节点（摘除节点）
- rabbitmqctl rename_cluster_node oldnode1 newnode1 修改节点名称





**RabbitMQ消息生产与消费**

- ConnectionFactory:获取连接工厂
- Connection：一个连接
- Channel：数据通信信道，可发送和接收消息
- Queue：具体的消息存储队列
- Producer&Consumer 生产和消费



创建队列

```java
   /**
     * Declare a queue
     * @see com.rabbitmq.client.AMQP.Queue.Declare
     * @see com.rabbitmq.client.AMQP.Queue.DeclareOk
     * @param queue the name of the queue
     * @param durable true if we are declaring a durable queue (the queue will survive a server restart)
     * @param exclusive true if we are declaring an exclusive queue (restricted to this connection)
     * @param autoDelete true if we are declaring an autodelete queue (server will delete it when no longer in use)
     * @param arguments other properties (construction arguments) for the queue
     * @return a declaration-confirm method to indicate the queue was successfully declared
     * @throws java.io.IOException if an error is encountered
     */
    Queue.DeclareOk queueDeclare(String queue, boolean durable, boolean exclusive, boolean autoDelete,
                                 Map<String, Object> arguments) throws IOException;
                             
```







**RabbitMQ交换机详解**

- Exchange : 接收消息，并根据路由键转发消息到所绑定的队列

  ![image-20191027112807444](./images/image-20191027112807444.png)
  - Name：交换机名称

  - Type：交换机类型direct、topic、fanaut、headers

    - Direct Exchange：

      - 所有发送到Direct Exchange的消息被转发到RouteKey中指定的Queue

        注意：Direct模式可以使用RabbitMQ自带的Exchange：default Exchange，所以不需要将Exchange进行任何绑定（bingding）操作，消息传递时，RouteKey必须完全匹配才会被队列接收，否则消息会被抛弃。

        ![image-20191027114037845](./images/image-20191027114037845.png)

    - Topic Exchange

      - 所有发送到Topic Exchange的消息被转发到所有关心RouteKey中指定Topic的Queue上

      - Exchange将RouteKey和某Topic进行模糊匹配，此时队列需要绑定一个Topic

        注意：可以使用通配符进行模糊匹配

        ![image-20191027120230039](./images/image-20191027120230039.png)

        ![image-20191027120307556](./images/image-20191027120307556.png)

    - Fanout Exchange

      - 不处理路由键，只需要简单的将队列绑定到交换机上

      - 发送到交换机上的消息都会被转发到与该交换机绑定的所有队列上

      - Fanout交换机转发消息是最快的

        ![image-20191028172955073](./images/image-20191028172955073.png)

  - Durability：是否需要持久化，true为持久化

  - Auto Delete：当最后一个绑定到Exchange上的队列删除后，自动删除该Exchange

  - Internal：当前Exchange是否用于RabbitMQ内部使用，默认为False





**RabbitMQ队列、绑定、虚拟主机、消息**

- Binding-绑定

  - Exchange和Exchange、Queue之间的连接关系
  - Binding中可以包含RoutingKey或者参数

- Queue-消息队列

  - 实际存储数据
  - Durability：是否持久化
  - Auto delete：当最后一个监听被移除，Queue将自动删除

- Message-消息

  - 服务器和英语程序之间传送的数据
  - 本质上就是一段数据，由Properties和Payload组成
  - 常用属性：delivery mode、headers（自定义属性）
  - content_type\content_encoding\priority（顺序消息怎么设计？）
  - Correlation_id\reply_to\expiration\message_id
  - timestamp\type\user_id\app_id\cluster_id

- Virtual host-虚拟主机

  - 虚拟地址，用于进行逻辑隔离，最上层的消息路由
  - 一个Virtual Host里面可以有若干个Exchange和Queue
  - 同一个Virtual Host里面不可以有同名的Exchange和Queue

  



## RabbitMQ高级特性

### 消息如何保障100%的投递成功

#### 生产端-可靠性投递

什么是生产端的可靠投递？

- 保障消息的成功发出
- 保障MQ节点的成功接收
- 发送端收到MQ节点（Broker）确认应答
- 完善的消息进行补偿机制



BAT/TMD互联网大厂的解决方案：

- 消息落库，对消息状态进行打标(**高并发不适用**)

  消息信息落库，对消息状态进行打标

  ![image-20191028190246052](./images/image-20191028190246052.png)

  （step1 如果失败，可以快速失败）

- 消息的延迟投递，做二次确认，回调检查（3-3节，注意）

  ![image-20191028191811110](./images/image-20191028191811110.png)

  注意：

  - 一定要先对业务数据入库后再上发消息



### 幂等性概念详解

![image-20191029172002066](./images/image-20191029172002066.png)

#### 消费端-幂等性保障

在海量订单产生的业务高峰期，如何避免消息的重复消费问题？

- 消费端实现幂等性，就意味着，我们的消息永远不会消费多次，即使我们收到多条一样的消息

业界主流的幂等性操作：

- 唯一ID + 指纹码机制，利用数据库主键去重

  ![image-20191029172659307](./images/image-20191029172659307.png)

  先查询，后插入（如果没有就插入）

- 利用Redis的原子性去实现

  ![image-20191029173342120](./images/image-20191029173342120.png)





### 在海量订单产生的业务高峰期，如何避免消息的重复消费问题



### Confirm确认消息、Return返回消息

#### Confirm确认消息

![image-20191029174014862](./images/image-20191029174014862.png)

确认机制流程图：

![image-20191029174115080](./images/image-20191029174115080.png)

如何实现Confirm确认消息：

![image-20191029174214447](./images/image-20191029174214447.png)

#### Return消息机制

![image-20191029184316189](./images/image-20191029184316189.png)

![image-20191029184357597](./images/image-20191029184357597.png)

Return消息机制流程：

![image-20191029184516015](./images/image-20191029184516015.png)



### 自定义消费者

消费端自定义监听

![image-20191029185520184](./images/image-20191029185520184.png)

Envelope包含信息：

- deliveryTag
- redeliver
- exchange
- routingKey





### 消息的限流

![image-20191029190638752](./images/image-20191029190638752.png)

![image-20191029191055067](./images/image-20191029191055067.png)

![image-20191029191226544](./images/image-20191029191226544.png)

global一般为false，不应用于Channel，用于Customer级别。

![image-20191029191413510](./images/image-20191029191413510.png)



### 消息的ACK与重回队列

![image-20191029192529629](./images/image-20191029192529629.png)

![image-20191029192649862](./images/image-20191029192649862.png)



### TTL消息

![image-20191030145553250](./images/image-20191030145553250.png)

通过设置队列设置过期时间。



另外发消息时也可以指定一个过期时间。





### 死信队列

死信队列：DLX，Dead-Letter-Exchange

![image-20191030151245448](./images/image-20191030151245448.png)

![image-20191030151349330](./images/image-20191030151349330.png)



死信队列 概念描述：

![image-20191030151525296](./images/image-20191030151525296.png)



死信队列设置：

![image-20191030151630596](./images/image-20191030151630596.png)

![image-20191030151748403](./images/image-20191030151748403.png)







## RabbitMQ高级整合应用

### RabbitMQ整合 Spring AMQP实战

#### RabbitAdmin

![image-20191030155458907](./images/image-20191030155458907.png)

![image-20191030155619936](./images/image-20191030155619936.png)

![image-20191030155734999](./images/image-20191030155734999.png)





#### SpringAMQP声明

- 在Rabbit基础API里面声明一个Exchange、声明一个绑定、一个队列

![image-20191030174118138](./images/image-20191030174118138.png)



#### RabbitTemplate

即消息模版

![image-20191030182957049](./images/image-20191030182957049.png)

```java
	@Test
	public void testSendMessage() throws Exception {
		//1 创建消息
		MessageProperties messageProperties = new MessageProperties();
		messageProperties.getHeaders().put("desc", "信息描述..");
		messageProperties.getHeaders().put("type", "自定义消息类型..");
		Message message = new Message("Hello RabbitMQ".getBytes(), messageProperties);
		
		rabbitTemplate.convertAndSend("topic001", "spring.amqp", message, new MessagePostProcessor() {
			@Override
			public Message postProcessMessage(Message message) throws AmqpException {
				System.err.println("------添加额外的设置---------");
				message.getMessageProperties().getHeaders().put("desc", "额外修改的信息描述");
				message.getMessageProperties().getHeaders().put("attr", "额外新加的属性");
				return message;
			}
		});
	}
	
	@Test
	public void testSendMessage2() throws Exception {
		//1 创建消息
		MessageProperties messageProperties = new MessageProperties();
		messageProperties.setContentType("text/plain");
		Message message = new Message("mq 消息1234".getBytes(), messageProperties);
		
		rabbitTemplate.send("topic001", "spring.abc", message);
		
		rabbitTemplate.convertAndSend("topic001", "spring.amqp", "hello object message send!");
		rabbitTemplate.convertAndSend("topic002", "rabbit.abc", "hello object message send!");
	}
```



#### SimpleMessageListenerContainer

简单消息监听容器

![image-20191030185046502](./images/image-20191030185046502.png)

事务用的少，了解即可

![image-20191030185137114](./images/image-20191030185137114.png)

![image-20191030185335164](./images/image-20191030185335164.png)



注意：

![image-20191030185451606](./images/image-20191030185451606.png)



思考：

- SimpleMessageListenerContainer为什么可以动态感知配置变更？





#### MessageListenerAdapter

消息监听适配器

可以添加Converter。



1. 适配器方式，默认有自己的方法名字的：handleMessage





2. 适配器方式：我们的队列名称 和 方法名称 也可以进行一一的匹配



![image-20191102094514992](./images/image-20191102094514992.png)



![image-20191102094546154](./images/image-20191102094546154.png)





#### MessageConverter

MessageConverter消息转换器

![image-20191102094655467](./images/image-20191102094655467.png)

实现方法：

![image-20191102094726797](./images/image-20191102094726797.png)



常用的MessageConverter消息转换器

![image-20191102094811580](./images/image-20191102094811580.png)







### RabbitMQ整合Spring Boot实战

生产端：

![image-20191102104730427](./images/image-20191102104730427.png)

![image-20191102104517857](./images/image-20191102104517857.png)

![image-20191102104611459](./images/image-20191102104611459.png)

消费端：

核心配置：

![image-20191102110819521](./images/image-20191102110819521.png)



![image-20191102110937021](./images/image-20191102110937021.png)



@RabbitListener注解使用

![image-20191102111023087](./images/image-20191102111023087.png)



![image-20191102111117591](./images/image-20191102111117591.png)



PS：由于类配置写在代码里非常不友好，所以强烈建议大家使用配置文件配置。



### RabbitMQ整合Spring Cloud实战

![image-20191102114638752](./images/image-20191102114638752.png)

![image-20191102114700089](./images/image-20191102114700089.png)



![image-20191102114754315](./images/image-20191102114754315.png)



![image-20191102114903542](./images/image-20191102114903542.png)

![image-20191102115025688](./images/image-20191102115025688.png)

![image-20191102115107734](./images/image-20191102115107734.png)



![image-20191102115128191](./images/image-20191102115128191.png)

![image-20191102115321765](./images/image-20191102115321765.png)









## RabbitMQ集群架构

### RabbitMQ集群架构模式

#### 主备模式

![image-20191103090838161](./images/image-20191103090838161.png)

![image-20191103090906558](./images/image-20191103090906558.png)

![image-20191103090942883](./images/image-20191103090942883.png)



![image-20191103091043045](./images/image-20191103091043045.png)



#### 远程模式（用的不多，较早期的模式）

![image-20191103091332535](./images/image-20191103091332535.png)

![image-20191103091356934](./images/image-20191103091356934.png)

![image-20191103091453215](./images/image-20191103091453215.png)

![image-20191103091643104](./images/image-20191103091643104.png)

![image-20191103091808030](./images/image-20191103091808030.png)

![image-20191103091827815](./images/image-20191103091827815.png)

![image-20191103091853055](./images/image-20191103091853055.png)



#### 镜像模式（常用）

![image-20191103092048145](./images/image-20191103092048145.png)

![image-20191103092107845](./images/image-20191103092107845.png)



![image-20191103092214077](./images/image-20191103092214077.png)



#### 多活模式

![image-20191103092539481](./images/image-20191103092539481.png)

有多个rabbitmq集群。

![image-20191103092748783](./images/image-20191103092748783.png)



![image-20191103092834545](./images/image-20191103092834545.png)

![image-20191103092950790](./images/image-20191103092950790.png)

![image-20191103093139154](./images/image-20191103093139154.png)











### 构建高可靠的RabbitMQ集群

#### 镜像模式

![image-20191103093449548](./images/image-20191103093449548.png)



#### HAProxy

![image-20191106083817305](./images/image-20191106083817305.png)

![image-20191106083917615](./images/image-20191106083917615.png)

![image-20191106083946683](./images/image-20191106083946683.png)



#### KeepAlived

![image-20191107115010440](./images/image-20191107115010440.png)

![image-20191107115146283](./images/image-20191107115146283.png)

![image-20191107115225499](./images/image-20191107115225499.png)





### 集群的配置文件与集群运维故障、失败转移讲解

#### 关键配置参数

![image-20191107144203222](./images/image-20191107144203222.png)

![image-20191107144328687](./images/image-20191107144328687.png)



#### 集群恢复与故障转移

![image-20191107160706792](./images/image-20191107160706792.png)

![image-20191107161126694](./images/image-20191107161126694.png)

![image-20191107161155298](./images/image-20191107161155298.png)

![image-20191107161336092](./images/image-20191107161336092.png)

![image-20191107161541475](./images/image-20191107161541475.png)

![image-20191107161715897](./images/image-20191107161715897.png)





### 高级插件的使用

#### 延迟插件

延迟插件的作用：

![image-20191107162112283](./images/image-20191107162112283.png)



延迟插件的安装

![image-20191107162252778](./images/image-20191107162252778.png)



## 互联网大厂SET化架构

SET：单元化

### SET化架构的进衍

![image-20191107172337619](./images/image-20191107172337619.png)

![image-20191107173052032](./images/image-20191107173052032.png)

![image-20191107173113675](./images/image-20191107173113675.png)

![image-20191108092508711](./images/image-20191108092508711.png)

![image-20191108092746214](./images/image-20191108092746214.png)



解决方案：

#### 同城"双活"架构介绍

![image-20191108093022739](./images/image-20191108093022739.png)

![image-20191108093103899](./images/image-20191108093103899.png)

#### 两地三中心架构

![image-20191108093221984](./images/image-20191108093221984.png)

两地三中心架构

![image-20191108093322989](./images/image-20191108093322989.png)



#### SET化方案

目标：

![image-20191108093721551](./images/image-20191108093721551.png)

SET化架构策略

![image-20191108093845833](./images/image-20191108093845833.png)



![image-20191108094352744](./images/image-20191108094352744.png)

·![image-20191108094525228](./images/image-20191108094525228.png)

![image-20191108094637713](./images/image-20191108094637713.png)

![image-20191108094955858](./images/image-20191108094955858.png)

![image-20191108095626204](./images/image-20191108095626204.png)



优势：

![image-20191108095721010](./images/image-20191108095721010.png)

![image-20191108095800939](./images/image-20191108095800939.png)

![image-20191108095921579](./images/image-20191108095921579.png)

![image-20191108100002694](./images/image-20191108100002694.png)



![image-20191108100215868](./images/image-20191108100215868.png)





SET化架构原则

![image-20191108101026301](./images/image-20191108101026301.png)

![image-20191108101054373](./images/image-20191108101054373.png)

![image-20191108101232189](./images/image-20191108101232189.png)







### RabbitMQ SET化架构搭建

![image-20191108101619712](./images/image-20191108101619712.png)



Federation

![image-20191108102051459](./images/image-20191108102051459.png)

![image-20191108102301272](./images/image-20191108102301272.png)



![image-20191108104353270](./images/image-20191108104353270.png)

![image-20191108104446186](./images/image-20191108104446186.png)

![image-20191108104519987](./images/image-20191108104519987.png)



## RabbitMQ在一线大厂中的基础组件架构设计思路



### 一线大厂中MQ组件实现思路和架构设计方案

![image-20191108105127175](./images/image-20191108105127175.png)



基本需求：

![image-20191108105812909](./images/image-20191108105812909.png)

扩展封装：

![image-20191108105937421](./images/image-20191108105937421.png)

![image-20191108110037880](./images/image-20191108110037880.png)



#### 基础组件封装设计 - 迅速消息发送

![image-20191108110248006](./images/image-20191108110248006.png)

![image-20191108110314854](./images/image-20191108110314854.png)



### 基础组件封装设计 - 确认消息发送

![image-20191108110358087](./images/image-20191108110358087.png)



![image-20191108111218628](./images/image-20191108111218628.png)





### 基础组件封装设计 - 批量消息发送

![image-20191108110650334](./images/image-20191108110650334.png)



![image-20191108111002858](./images/image-20191108111002858.png)



### 基础组件封装设计 - 延迟消息发送

![image-20191108111145367](./images/image-20191108111145367.png)



### 

### 基础组件封装设计 -顺序消息发送

![image-20191108113836564](./images/image-20191108113836564.png)

![image-20191108114121431](./images/image-20191108114121431.png)

![image-20191108114244311](./images/image-20191108114244311.png)





### 基础组件封装设计 -事务消息发送

![image-20191108114657332](./images/image-20191108114657332.png)

![image-20191108114930137](./images/image-20191108114930137.png)

![image-20191108115208815](./images/image-20191108115208815.png)

![image-20191108115426716](./images/image-20191108115426716.png)

![image-20191108115537871](./images/image-20191108115537871.png)



### 消息的幂等性保证-消息路由规则架构设计

![image-20191108124135892](./images/image-20191108124135892.png)

![image-20191108124209598](./images/image-20191108124209598.png)



## 课程总结

![image-20191108130054398](./images/image-20191108130054398.png)









## 其他问题

问题一：启动时报错：`ERROR: epmd error for host "yourhostname": timeout`

解决：原因是主机名和ip不匹配，修改hostname或者/etc/hosts文件

```
192.168.252.128 xfei-centos
```

