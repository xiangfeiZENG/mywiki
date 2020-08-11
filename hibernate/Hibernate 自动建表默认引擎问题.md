# Hibernate 自动建表默认引擎问题

Hibernate建表默认引擎为myisam，myisam不支持事务。

修改方式

Spring Boot 程序

application.properties修改配置

```
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL55Dialect
```

application.yml

```
spring:
  jpa:
    database: mysql
    show-sql: true
    hibernate:
      ddl-auto: update
      naming:
        physical-strategy: org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy
    database-platform: org.hibernate.dialect.MySQL5InnoDBDialect  #不加这句则默认为myisam引擎
```

