# Spring Boot Rabbit MQ

```

# MQ配置
spring.rabbitmq.host=192.168.127.41
spring.rabbitmq.port=8085
spring.rabbitmq.username=zengxf
spring.rabbitmq.password=zengxf
spring.rabbitmq.virtual-host=/
spring.rabbitmq.connection-timeout=PT15S
# 开启消息确认模式
spring.rabbitmq.publisher-confirms=true

# 采用连接池模式 采用连接池模式无法自动创建交换器、队列和绑定
spring.rabbitmq.cache.connection.mode=connection
spring.rabbitmq.cache.connection.size=3
spring.rabbitmq.cache.channel.size=10

# 消息发送到交换器的确认



#定义虚拟主机
#spring.rabbitmq.virtual-host=mvne
#开启手动确认
spring.rabbitmq.listener.acknowledge-mode=manual
#消息发送到交换机的确认
#spring.rabbitmq.publisher-confirms = true
#消息发送到队列的确认
#spring.rabbitmq.publisher-returns = true
#spring.rabbitmq.template.mandatory=true


```

