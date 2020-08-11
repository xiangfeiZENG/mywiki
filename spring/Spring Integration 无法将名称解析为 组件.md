# Spring Integration 无法将名称解析为 组件

使用spring integration集成程序时，遇到xml文件解析错误问题：

配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:int="http://www.springframework.org/schema/integration"
       xmlns:int-jdbc="http://www.springframework.org/schema/integration/jdbc"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc"
       xsi:schemaLocation="http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
		http://www.springframework.org/schema/integration https://www.springframework.org/schema/integration/spring-integration.xsd
		http://www.springframework.org/schema/integration/jdbc https://www.springframework.org/schema/integration/jdbc/spring-integration-jdbc.xsd
		http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd">

    <int-jdbc:inbound-channel-adapter channel="jdbcInboundChannel"
                                      data-source="dataSource"
                                      max-rows-per-poll="50"
                                      query="select * from gprs_cdr gprs where status=1"
                                      update="update gprs_cdr set status=2 where id in(:id)">
        <int:poller fixed-rate="10000">
            <int:transactional propagation="REQUIRED" transaction-manager="transactionManager"/>
        </int:poller>
    </int-jdbc:inbound-channel-adapter>

    <int:channel id="jdbcInboundChannel"/>

    <int:service-activator input-channel="jdbcInboundChannel"
                           ref="jdbcHandler"
                           method="handleCdr"
                           output-channel="nullChannel"
                           />

    <bean id="jdbcHandler" class="com.cmit.mvne.billing.settle.handler.JdbcHandler"/>

</beans>
```

报错信息：

```
org.springframework.beans.factory.xml.XmlBeanDefinitionStoreException: Line 150 in XML document from class path resource [META-INF/spring/integration/jdbcInboundChannelAdapter.xml] is invalid; nested exception is org.xml.sax.SAXParseException; systemId: https://www.springframework.org/schema/integration/jdbc/spring-integration-jdbc.xsd; lineNumber: 150; columnNumber: 70; src-resolve: 无法将名称 'integration:channelAdapterAttributes' 解析为 'attribute group' 组件。
```



debug 跟进代码，发现原因是无法找到xsd导致。

查看spring.schemas

```
http\://www.springframework.org/schema/integration/spring-integration-5.0.xsd=org/springframework/integration/config/spring-integration-5.0.xsd
http\://www.springframework.org/schema/integration/spring-integration.xsd=org/springframework/integration/config/spring-integration-5.0.xsd
```

原因：复制的是高版本的spring integration xml 头文件，导致到不到xsd。