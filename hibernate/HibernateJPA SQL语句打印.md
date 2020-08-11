# Hibernate/JPA SQL语句打印



Spring [JDBC](https://www.baeldung.com/spring-jdbc-jdbctemplate)和[JPA](https://www.baeldung.com/the-persistence-layer-with-spring-data-jpa)通过本机JDBC API提供了抽象，默认日志不显示SQL查询。通常我们出于调试目的需要查看那些自动生成的SQL语句以及而执行它们的顺序。







## JPA 日志查询

### **到标准输出**

将查询转储到标准输出中最简单的方法是将以下内容添加到*application.properties中*：

```
spring.jpa.show-sql=true
```

为了美化或漂亮地打印SQL，我们可以添加：

```
spring.jpa.properties.hibernate.format_sql=true
```

尽管这非常简单，**但不建议这样**做，因为它无需进行日志记录框架的任何优化即可直接将所有内容卸载到标准输出中。

而且，**它不会记录准备好的语句的参数。**

### 通过logger

现在，让我们看看如何通过在属性文件中配置记录器来记录SQL语句：

```
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

第一行记录SQL查询，第二行记录准备好的语句参数。

pretty print属性也将在此配置中工作。

通过设置这些属性，**日志将发送到已配置的appender**。默认情况下，Spring Boot使用带有标准out附加程序的*logback*。



## JdbcTemplate日志查询

要在使用*JdbcTemplate*时配置语句日志记录，我们需要以下属性：

```
logging.level.org.springframework.jdbc.core.JdbcTemplate=DEBUG
logging.level.org.springframework.jdbc.core.StatementCreatorUtils=TRACE
```

与JPA日志记录配置类似，第一行用于记录语句，第二行用于记录准备好的语句的参数。



## 结论

**Spring / Hibernate类（**生成SQL语句并设置参数）**已经包含用于记录它们的代码**。

但是，这些日志语句的级别分别设置为*DEBUG*和*TRACE*，低于Spring Boot – *INFO中*的默认级别。通过添加这些属性，我们只是将那些记录器设置为所需级别。