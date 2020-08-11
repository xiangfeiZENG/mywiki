---
title: Java笔记-集成日志
date: 2018-04-27 12:10:01
categories: java
tags: java
---

## Spring 集成logback日志

本文引用参考：

https://blog.csdn.net/luhy516/article/details/76686984

logback替代log4j作为Spring MVC项目的日志处理组件。这两者虽然作者相同，但log4j早已被托管给Apache基金会维护，并且自从2012年5月之后就没有更新了。而logback从出生开始就是其作者奔着取代log4j的目的开发的，因此一方面logback继承了log4j大量的用法，使得学习和迁移的成本不高，另一方面logback在性能上要明显优于log4j，尤其是在大量并发的环境下，并且新增了一些log4j所没有的功能（如将日志文件压缩成zip包等）。

Spring MVC集成slf4j-log4j的过程，如下：

1. 在pom.xml文件中添加slf4j-log4j的依赖，完成后的classpath中将新增三个jar包，分别是：slf4j-api.jar、log4j.jar及slf4j-log4j.jar。
2. 在当前classpath中添加log4j.properties配置文件，按照log4j的参数语法编写该文件。
3. 以上两步完成后，普通的Java项目就能使用slf4j-log4j进行日志处理了；对于Java Web项目，还需要在web.xml文件中配置Log4jConfigLocation和Log4jConfigListener。

### 依赖包

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.1.3</version>
</dependency>
<dependency>
    <groupId>org.logback-extensions</groupId>
    <artifactId>logback-ext-spring</artifactId>
    <version>0.1.2</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jcl-over-slf4j</artifactId>
    <version>1.7.12</version>
</dependency>
```

注意：

1. logback-classic包含了logback本身所需的slf4j-api.jar、logback-core.jar及logback-classsic.jar
2. logback-ext-spring是由官方提供的对Spring的支持，它的作用就相当于log4j中的Log4jConfigListener；这个listener，网上大多都是用的自己实现的，原因在于这个插件似乎并没有出现在官方文档的显要位置导致大多数人并不知道它的存在
3. jcl-over-slf4j是用来把Spring源代码中大量使用到的commons-logging替换成slf4j，只有在添加了这个依赖之后才能看到Spring框架本身打印的日志，否则只能看到开发者自己打印的日志

### logback配置文件

在classpath上配置logback.xml

```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true" scan="true" scanPeriod="30 minutes">

    <!-- 日志文件存放的基础路径(路径最后不需要加/或\) -->
    <property name="LOG_DIR" value="D:\\app"/>
    <property name="FILE_HOME" value="${LOG_DIR}/logs/" />
    <property name="LOG_NAME" value="springaction"/>

    <!-- 控制台输出 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!-- encoder 默认配置为PatternLayoutEncoder -->
        <encoder>
            <pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%level] [%logger{36}:%line] [%thread] [%X{userInfo}] %msg%n</pattern>
        </encoder>
    </appender>

    <!-- 文件输出 -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
<!--
        <file>${FILE_HOME}/debug.log</file>
-->
        <append>true</append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${FILE_HOME}/${LOG_NAME}_%i.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>90</maxHistory>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <encoder>
            <pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%level] [%logger{36}:%line] [%thread] [%X{userInfo}] %msg%n</pattern>
        </encoder>
    </appender>


    <!-- http://www.cnblogs.com/tankaixiong/archive/2013/03/08/2949857.html -->
    <logger name="org.springframework">
        <level value="INFO" />
    </logger>

    <logger name="com.alibaba.druid.pool" >
        <level value="INFO" />
    </logger>

    <logger name="org.directwebremoting" >
        <level value="INFO" />
    </logger>
    <logger name="org.directwebremoting.log.accessLog" >
        <level value="INFO" />
    </logger>

    <logger name="org.mybatis" >
        <level value="INFO" />
    </logger>
    <logger name="mybatis.mapper" >
        <level value="WARN" />
    </logger>

    <logger name="org.quartz" >
        <level value="WARN" />
    </logger>


    <logger name="java.sql.Connection" >
        <level value="WARN" />
    </logger>
    <logger name="java.sql.Statement" >
        <level value="DEBUG" />
    </logger>
    <logger name="java.sql.PreparedStatement" >
        <level value="INFO" />
    </logger>
    <logger name="java.sql.ResultSet" >
        <level value="INFO" />
    </logger>

    <logger name="log4jdbc.debug">
        <level value="INFO" />
    </logger>
    <logger name="jdbc.audit">
        <level value="OFF" />
    </logger>
    <logger name="jdbc.sqlonly">
        <level value="OFF" />
    </logger>
    <logger name="jdbc.sqltiming">
        <level value="INFO" />
    </logger>
    <logger name="jdbc.resultset">
        <level value="WARN" />
    </logger>
    <logger name="jdbc.connection">
        <level value="OFF" />
    </logger>
    <logger name="jdbc.transcation">
        <level value="INFO" />
    </logger>

    <logger name="org.apache.commons.httpclient">
        <level value="INFO" />
    </logger>
    <logger name="httpclient.wire">
        <level value="INFO" />
    </logger>

    <logger name="org.directwebremoting" >
        <level value="INFO" />
    </logger>
    <logger name="org.directwebremoting.log.accessLog" >
        <level value="INFO" />
    </logger>

    <root>
        <priority value="DEBUG" />
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE" />
    </root>

</configuration>
```



### 配置web.xml

与log4j类似，logback集成到Spring MVC项目中，也需要在web.xml中进行配置，同样也是配置一个config location和一个config listener，如下所示：

```xml

<context-param>
		<param-name>logbackConfigLocation</param-name>
		<param-value>classpath:logback.xml</param-value>
	</context-param>
	<listener>
		<listener-class>ch.qos.logback.ext.spring.web.LogbackConfigListener</listener-class>
	</listener>
```





## 日志部署

### 原因

SpringBoot先于LogBack加载，application-dev.yml中的logging path 后生效

在logback-spring.xml 增加
<springProperty name="log.path" source="logging.path" defaultValue="${user.dir}/log"/>

 即可



三个配置文件：

logback-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<configuration scan="true" scanPeriod="60 seconds" debug="false">
    <contextName>mvne-crm-eureka</contextName>

    <springProperty name="log.path" source="logging.path" defaultValue="${user.dir}/log"/>

    <springProfile name="default">
        <include resource="logback/logback-dev.xml" />
    </springProfile>
    <springProfile name="dev">
        <include resource="logback/logback-dev.xml" />
    </springProfile>

</configuration>
```



```xml
<!--<?xml version="1.0" encoding="UTF-8"?>-->

<!--<configuration scan="true" scanPeriod="60 seconds" debug="false">-->
<included>
    <contextName>mvne-crm-eureka</contextName>

    <!--    <property name="log.path" value="log" />-->
    <property name="log.maxHistory" value="30" />
    <property name="log.totalSizeCap" value="1GB"/>
    <property name="log.colorPattern" value="%magenta(%d{yyyy-MM-dd HH:mm:ss}) %highlight(%-5level) %yellow(%thread) %green(%logger) %msg%n"/>
    <property name="log.pattern" value="%d{yyyy-MM-dd HH:mm:ss} %-5level %thread %logger %msg%n"/>

    <!--输出到控制台 -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${log.colorPattern}</pattern>
        </encoder>
    </appender>

    <!--按天生成日志 -->
    <appender name="file_info"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <FileNamePattern>${log.path}/info/mvne-crm-eureka.%d{yyyy-MM-dd}.log</FileNamePattern>
            <maxHistory>${log.maxHistory}</maxHistory>
            <totalSizeCap>${log.totalSizeCap}</totalSizeCap>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <appender name="file_error"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <FileNamePattern>${log.path}/error/mvne-crm-eureka-error.%d{yyyy-MM-dd}.log</FileNamePattern>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <root level="trace">
    </root>

    <root level="info">
        <appender-ref ref="console" />
        <appender-ref ref="file_info" />
        <appender-ref ref="file_error" />
    </root>

    <logger name="org.springframework.cloud.netflix.eureka" level="debug" additivity="false"/>

</included>
```



