## Activiti基础

#### 认识Activiti

工作流应用广泛，由任务驱动的各种系统中都能看到它的影子，例如，CRM、ERP、BCM、BI、OA等。工作流总是以任务（TASK）的形式驱动人处理业务或者驱动业务系统自动完成作业，有了工作流引擎后，我们不必一直等待其他人的工作进度，直白的说，只需要关心系统首页的代办任务数即可，有系统提醒当前有多少代办任务要处理。

工作流中每一个动作都可以称为活动（Activity），业务流程中最小的组成部分，多个活动，Activities。最后以复数简化的方式标志活动的集合，以此来诠释Activiti与工作流的目的与设计。

BPM（Business Process Management），业务流程管理。

BPMN ( Business Process Modeling Notation)，业务流程建模标注。



##### activiti配置

activiti配置用于构造processEngine引擎。

activiti.cfg.xml例子如下：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans   
       http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="driverClassName">
			<value>com.mysql.jdbc.Driver</value>
		</property>
		<property name="url">
			<value>jdbc:mysql://127.0.0.1:3306/activiti1?useUnicode=true&amp;characterEncoding=UTF-8
			</value>
		</property>
		<property name="username">
			<value>root</value>
		</property>
		<property name="password" value="root-123" />
		<!-- -->
	</bean>
	<!-- -->
	<bean id="processEngineConfiguration"
		class="org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration">
		<property name="dataSource" ref="dataSource" />
		<property name="jobExecutorActivate" value="true" />
		<property name="databaseSchemaUpdate" value="true" />
	</bean>
</beans>
```

相关配置说明如下：

![1534303053384](E:\Wiki\mywiki\images\1534303053384.png)

