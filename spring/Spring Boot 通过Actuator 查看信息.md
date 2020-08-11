# Spring Boot 通过Actuator 查看信息

开发过程中，有时需要可视化的方式查看Beans和其他程序方式。

以赖：

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

application.properties 开发所有端口：

```
# 开发环境暴露actuator所有端点  注意这里的*不能有双引号，这个和yaml不同
management.endpoints.web.exposure.include=*
```



可查看端口：

The following technology-agnostic endpoints are available:

| ID               | Description                                                  | Enabled by default |
| ---------------- | ------------------------------------------------------------ | ------------------ |
| auditevents      | Exposes audit events information for the current application. | Yes                |
| beans            | Displays a complete list of all the Spring beans in your application. | Yes                |
| caches           | Exposes available caches.                                    | Yes                |
| conditions       | Shows the conditions that were evaluated on configuration and auto-configuration classes and the reasons why they did or did not match. | Yes                |
| configprops      | Displays a collated list of all @ConfigurationProperties.    | Yes                |
| env              | Exposes properties from Spring’s ConfigurableEnvironment.    | Yes                |
| flyway           | Shows any Flyway database migrations that have been applied. | Yes                |
| health           | Shows application health information.                        | Yes                |
| httptrace        | Displays HTTP trace information (by default, the last 100 HTTP request-response exchanges). | Yes                |
| info             | Displays arbitrary application info.                         | Yes                |
| integrationgraph | Shows the Spring Integration graph.                          | Yes                |
| loggers          | Shows and modifies the configuration of loggers in the application. | Yes                |
| liquibase        | Shows any Liquibase database migrations that have been applied. | Yes                |
| metrics          | Shows ‘metrics’ information for the current application.     | Yes                |
| mappings         | Displays a collated list of all @RequestMapping paths.       | Yes                |

| ID             | Description                                                  | Enabled by default |
| -------------- | ------------------------------------------------------------ | ------------------ |
| scheduledtasks | Displays the scheduled tasks in your application.            | Yes                |
| sessions       | Allows retrieval and deletion of user sessions from a Spring Session-backed session store. Not available when using Spring Session’s support for reactive web applications. | Yes                |
| shutdown       | Lets the application be gracefully shutdown.                 | No                 |
| threaddump     | Performs a thread dump.                                      | Yes                |