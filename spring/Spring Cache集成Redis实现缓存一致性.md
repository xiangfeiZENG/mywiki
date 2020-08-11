# Spring Cache集成Redis实现缓存一致性

> 参考文档：
>
> https://www.jianshu.com/p/d44b1c0f9df0
>
> https://www.jianshu.com/p/0624d15bfb0d
>
> [SpringBoot2.X整合Redis缓存](https://www.jianshu.com/p/0d4aea41a70c)
>
> [https://www.cnblogs.com/zhangjianbin/p/6439206.html](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fzhangjianbin%2Fp%2F6439206.html)
>
> [https://www.cnblogs.com/wzdnwyyu/p/11180461.html](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fwzdnwyyu%2Fp%2F11180461.html)



一般使用事务分为编程式和声明式。

编程式：事务操作与业务代码耦合，一般我们不会使用这种方式；
声明式：AOP的运用，通过注解使得事务代码与业务代码解耦，目前项目中一般都是使用事务注解。

而我们平时使用缓存，正是**编程式**，即对缓存的操作与业务代码耦合。那么是否存在一种类似于事务的技术，完成**声明式**的缓存操作呢？

而SpringCahe便可以提供**透明化的缓存操作**，即用户可以使用注解的方式。灵活的操纵缓存。



## Spring Cache 集成Redis

### 相关包

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```



### 配置

可以在yml中配置，推荐在@Configuration中进行配置。原因是更加灵活，在配置CacheManager的Bean时，可以初始化Cache对象，在项目启动的时候注册到CacheManager中。

```java
package com.cmit.mvne.billing.preparation.config;

import com.fasterxml.jackson.annotation.JsonInclude;
import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.PropertyNamingStrategy;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateTimeDeserializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateTimeSerializer;
import org.springframework.aop.framework.AopProxyUtils;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.interceptor.KeyGenerator;
import org.springframework.cache.interceptor.SimpleKeyGenerator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializationContext;

import java.lang.reflect.Method;
import java.text.SimpleDateFormat;
import java.time.Duration;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.HashMap;
import java.util.Map;

/**
 * @author <a href="mailto:zengxiangfei@chinamobile.com">zengxf</a>
 * @since 2020/4/16
 */
@Configuration
@EnableCaching
public class CachingConfig {

    /**
     * 自定义KeyGenerator
     * @return
     */
    @Bean
    public KeyGenerator keyGenerator() {
        return new KeyGenerator() {
            @Override
            public Object generate(Object target, Method method, Object... params) {
                //获取代理对象的最终目标对象
                Class<?> targetClass = AopProxyUtils.ultimateTargetClass(target);
                StringBuilder sb = new StringBuilder();
                sb.append(targetClass.getSimpleName()).append(":");
                sb.append(method.getName()).append(":");
                //调用SimpleKey的逻辑
                Object key = SimpleKeyGenerator.generateKey(params);
                return sb.append(key);
            }
        };
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        //设置特有的Redis配置
        Map<String, RedisCacheConfiguration> cacheConfigurations = new HashMap<>();
        //定制化的Cache为300s
        cacheConfigurations.put("as",customRedisCacheConfiguration(Duration.ofSeconds(300)));
        cacheConfigurations.put("books",customRedisCacheConfiguration(Duration.ofSeconds(300)));
        cacheConfigurations.put("cs",customRedisCacheConfiguration(Duration.ofSeconds(300)));
        //默认超时时间60s
        return RedisCacheManager.builder(connectionFactory).
                transactionAware().   //Cache的事务支持
                cacheDefaults(customRedisCacheConfiguration(Duration.ofSeconds(60))).
                withInitialCacheConfigurations(cacheConfigurations).   //设置个性化的Cache配置
                build();
    }

    /**
     * 设置RedisConfiguration配置
     *
     * @param ttl
     * @return
     */
    public RedisCacheConfiguration customRedisCacheConfiguration(Duration ttl) {
        //设置序列化格式
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer
                = new Jackson2JsonRedisSerializer<>(Object.class);
        jackson2JsonRedisSerializer.setObjectMapper(RedisObjectMapper.redisConfigurationObjectMapper());
        return RedisCacheConfiguration.
                defaultCacheConfig().serializeValuesWith(
                RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer)).
                computePrefixWith(cacheName -> cacheName + ":").   //设置Cache的前缀，默认::
                disableCachingNullValues().   //若返回值为null，则不允许存储到Cache中
                entryTtl(ttl);  //设置缓存缺省超时时间
    }

    /**
     * JSON转换，避免和容器中ObjectMapper冲突
     */
    public static class RedisObjectMapper {
        public static ObjectMapper redisConfigurationObjectMapper() {
            ObjectMapper objectMapper = new ObjectMapper();
            //JDK1.8新版时间格式化Model
            JavaTimeModule javaTimeModule = new JavaTimeModule();
            javaTimeModule.addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
            javaTimeModule.addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
            objectMapper.registerModule(javaTimeModule);
            //Date类型禁止转换为时间戳
            objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
            //序列化时格式化时间戳
            objectMapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
            //字段名字开启驼峰命名法
            objectMapper.setPropertyNamingStrategy(PropertyNamingStrategy.SNAKE_CASE);
            //序列化无public的属性或方法时，不会抛出异常
            objectMapper.disable(SerializationFeature.FAIL_ON_EMPTY_BEANS);
            //序列化时保存对象类型,以便反序列化时直接得到具体POJO
            objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
            //非空数据才进行格式化
            objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
            //针对BigDecimal，序列化时，不采取科学计数法
            objectMapper.enable(JsonGenerator.Feature.WRITE_BIGDECIMAL_AS_PLAIN);
            //反序列化时，POJO中不含有JSON串的属性，不解析该字段，并且不会抛出异常
            objectMapper.enable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
            //反序列化{}时，不抛出异常，而是得到null值
            objectMapper.enable(DeserializationFeature.ACCEPT_EMPTY_STRING_AS_NULL_OBJECT);
            return objectMapper;
        }
    }
}
```



**注意不要将ObjectMapper加入到Spring容器中。**因为Spring容器中存在一个ObjectMapper，以用于`@RequestBody`、`ResponseBody`、`RestTemplate`等地的序列化和反序列化。

为什么不采用Spring容器的ObjectMapper对象，而要自己设置是因为Redis配置了`objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);`属性，在序列化时记录类/属性的类型，以便在反序列化时得到POJO对象。此属性详见——[Jackson的ObjectMapper.DefaultTyping.NON_FINAL属性。](https://www.jianshu.com/p/c5fcd2a1ab49)

[此处可以使用protostuff替换Jackson进行序列化和反序列化，详细内容请点击...](https://www.jianshu.com/p/eb55c425645a)



KEY的设置

需要注意的是，SpringCache作为应用层的声明式缓存。其数据结构为`Key-Value`。那么设计一个安全优雅的Key，是一个重要的任务。

> 1. 在[SpringCache官网中](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.spring.io%2Fspring%2Fdocs%2F5.1.5.RELEASE%2Fspring-framework-reference%2Fintegration.html%23cache)，这样描述SpringCache默认的KeyGenerator的：

- 若没有参数值被得到，返回SimpleKey.EMPTY（空数组）。
- 若只有一个参数值被得到，返回该参数值的实例。
- 若多个参数值被得到，返回一个包含所有参数值SimpleKey对象。

> 2. 默认的KeyGenerator如何获取参数值？

- 若注解上只是指定`cacheName`属性，SimpleKeyGenerator将获取所有的参数值。组成SimpleKey对象。
- 指定`cacheName`和`key`属性，并且`key`的属性支持`SpEL`表达式：

1. 基本形式

```csharp
@Cacheable(value="cacheName", key="#id")
public ResultDTO method(int id);
```

2. 组合形式

```ruby
@Cacheable(value="cacheName", key="T(String).valueOf(#name).concat('-').concat(#password))
public ResultDTO method(int name, String password);
```

3. 对象形式

```ruby
@Cacheable(value="cacheName", key="#user.id)
public ResultDTO method(User user);
```

> 3. 默认的SimpleKeyGenerator的缺陷

SimpleGenerator只会将参数值封装为SimpleKey对象。然后作为Key，可能会导致**不同方法Key冲突**。
 我们虽然可以使用`SpEL`表达式获取类名、方法名，在进行拼接。但是需要为每一个注解指定，太过于繁杂。

> 1. 自定义KeyGenerator

注解上`keyGenerator`属性与`key`属性是不共存的，即我们若通过`keyGenerator`来自定义我们的Key生成器，那么就需要将所有的参数值均进行处理，而不能指定特定的参数值
 处理。

```java
@Bean
public KeyGenerator keyGenerator() {
    return (target, method, params) -> {
        //获取代理对象的最终目标对象
        Class<?> targetClass = AopProxyUtils.ultimateTargetClass(target);
        StringBuilder sb = new StringBuilder();
        sb.append(targetClass.getSimpleName()).append(":");
        sb.append(method.getName()).append(":");
        //调用SimpleKey的逻辑
        Object key = SimpleKeyGenerator.generateKey(params);
        return sb.append(key);
    };
}
```

使用：



```java
@Cacheable(value = "book2",keyGenerator = "keyGenerator")
public Account getAccInfo(String customerId, String accType) {
    //业务逻辑
}
```

使用

springCache和事务类型，均采用AOP原理。故它们的注意事项也是相同。

1. 若一个service中，注解方法被调用，则注解不会生效；
2. 只有访问修饰符为public的方法，注解才会生效；



## Spring Cache 使用

> 参考文档：
>
> [Spring官网对Cache的描述](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.spring.io%2Fspring%2Fdocs%2F5.1.5.RELEASE%2Fspring-framework-reference%2Fintegration.html%23cache)
>
> [SpringBoot官网对Cache的描述](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.spring.io%2Fspring-boot%2Fdocs%2F2.1.3.RELEASE%2Freference%2Fhtmlsingle%2F%23boot-features-caching)
>
> [spring-boot的spring-cache中的扩展redis缓存的ttl和key名](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fweixin_34343689%2Farticle%2Fdetails%2F92046678)

在Spring3.1版本后，Spring框架提供了对**缓存透明化应用**的支持。缓存抽象允许使用各种缓存解决方案，而对代码的影响最小。

从Spring4.1开始，通过支持JSR-107注释和更多自定义选项，来改善缓存抽象。



### 基于声明式注释的缓存

SpringCache是Service层的声明式缓存。即无需与业务代码耦合，通过注解完成缓存。

#### @Cacheable注解

@Cacheable的注解的处理流程如下图：

![img](https:////upload-images.jianshu.io/upload_images/16013479-54ef23cdc3847ee3.png?imageMogr2/auto-orient/strip|imageView2/2/w/417/format/webp)

可以使用`@Cacheable`用来划分可缓存的方法，即将结果存储在缓存中的方法，以便在后续调用（使用相同的参数）时，返回缓存中的值而无需实际执行该方法。

- 注释声明以最简单的形式：注解属性为CacheName。

```java
@Cacheable("books")
public Book findBook(ISBN isbn) {...}
```

- 支持多个CacheName。

```java
@Cacheable({"books", "isbns"})
public Book findBook(ISBN isbn) {...}
```



#### @Cacheable注解属性

调用者在调用方法时，会通过注解属性自动的去缓存中进行查询。那么我们需要指定`cacheManager(CacheResolver)`、`cacheName`、`key(keyGenerator)`，来确定去那个缓存管理器（Redis，ConcurrentHashMap等）进行查询。而cacheName以及key会组装成对应的键。

```css
@Cacheable(cacheNames="books", cacheManager="anotherCacheManager") 
public Book findBook(ISBN isbn) {...}
```

1. @CacheResolver：也可指定使用哪个缓存管理器。需要通过实现`org.springframework.cache.interceptor.CacheResolver`接口来解析

```css
@Cacheable(cacheResolver="runtimeCacheResolver") 
public Book findBook(ISBN isbn) {...}
```

> cacheManager和cacheResolver参数是互斥的，同时指定这两个参数会导致异常。因为实现CacheManager会忽略自定义的CacheResolver。



#### cachename

CacheName属性也是value属性，定义@Cacheable注解时，必须使用该属性。即指定缓存的名字。使用默认CacheManager属性，以及使用默认的key属性(SimpleKey对象包含所有的参数值)。

#### key和keyGenerator

> 1. keyGenrator属性

SpringCache默认使用SimpleKeyGenerator，默认情况下将**参数值**作为键，但是可能会导致key重复出现。

[我们在整合SpringCache中自定义CacheGenerator，将`类名:方法名`作为key的一部分。](https://www.jianshu.com/p/d44b1c0f9df0)

而后@Cacheable注解中，指定自定义的KeyGenerator。



```java
@Cacheable(value = "book2",keyGenerator = "keyGenerator")
public Account getAccInfo(String customerId, String accType) {
    //业务逻辑
}
```

**注意key和keyGenerator依旧是互斥的。**

> 1. key属性

当然若是使用key属性，也是可以指定类名和方法名等参数作为key。

SpringCache提供了与缓存相关的专用元数据，例如参数名称。下表描述了可用于上下文的项目，以便于**key的生成和条件计算**。

| 名称        | 位置       | 描述                                | 例子                 |
| :---------- | :--------- | :---------------------------------- | :------------------- |
| methodMame  | root       | 被调用方法的名称                    | #root.methodName     |
| method      | root       | 被调用的方法                        | #root.method.name    |
| target      | root       | 被调用的目标对象                    | #root.target         |
| targetClass | root       | 被调用目标的类                      | #root.targetClass    |
| args        | root       | 用于被调用目标的参数值（数组）      | #root.args[0]        |
| caches      | root       | 执行当前方法缓存的集合              | #root.caches[0].name |
| 参数名称    | 调用的方法 | 方法的任何参数名称                  | #iban或#a0           |
| result      | 调用的方法 | 仅用在unless,方法调用的结果(缓存值) | #result              |

1. cacheName无法使用SpEL表达式，#root.args是参数值。

```java
@Cacheable(cacheNames = "#root.methodName",key = "#root.args")
    public User getUser(int id) {
    User user = new User().setUserName("tom").setId(id);
    log.info("【调用getUser】方法");
    return user;
}
```

![img](https:////upload-images.jianshu.io/upload_images/16013479-da5f88604c57924b.png?imageMogr2/auto-orient/strip|imageView2/2/w/510/format/webp)

图4-   @Cacheable(cacheNames = "#root.methodName",key = "#root.args").png

1. 两个SpEL表达式拼接，创建更具体的key值



```java
@Cacheable(value = "book2",  
           key = "#root.targetClass.getSimpleName().concat(':').concat(#root.methodName).concat(':').concat(#customerId)")  
    public User getUser(int id) {
    User user = new User().setUserName("tom").setId(id);
    log.info("【调用getUser】方法");
    return user;
}
```

![img](https:////upload-images.jianshu.io/upload_images/16013479-6178dcc1e7b7c905.png?imageMogr2/auto-orient/strip|imageView2/2/w/693/format/webp)

图5-两个SpEL表达式拼接.png



#### 同步缓存

在多线程环境下，某些操作可能会为一个参数并发调用。默认情况下，SpringCache不会锁定任何内容，并且可能多次计算相同的值，从而破坏了缓存的目的。

对于那些特殊情况，可以使用**`sync`**属性来锁定。即只有一个线程正在忙于计算该值，而其他线程则被阻塞，直到缓存中更新该条目为止。

```java
@Cacheable(cacheNames="foos", sync=true) 
public Foo executeExpensiveOperation(String id) {...}
```



#### 条件缓存

1. condition：方法可能不总适合缓存（例如：他可能取决于给你定的参数）。缓存注释通过condition支持这种功能，该参数采用SpEL表达式，该表达式的值等于true或false。如果为true，则缓存该方法。否则的话，每次调用该方法。例如：仅当参数name的长度小于32时才缓存以下方法：

```java
@Cacheable(cacheNames="book", condition="#name.length() < 32") 
public Book findBook(String name)
```

1. unless（如果不）：可以使用unless参数来决定是否将值添加到缓存中，该参数也采用SpEl表达式，该表达式输出结果boolean类型。与condition不同的是，unless表达式是在调用方法后求值的，并且当SpEL返回false时，加入到缓存中（unless：如果不小于1000，则存储。）。

```java
@Cacheable(cacheNames="books", key="#isbn.rawNumber",unless ="#result.id < 1000" )
public Book findBook(ISBN isbn, boolean checkWarehouse, boolean includeUsed) {
    log.info("执行方法！");
    Book book = Book.builder().id(1101).bookName("java").build();
    return book;
}
```

[若出现org.springframework.expression.spel.SpelEvaluationException: EL1008E异常原因](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fbrokencolor%2Fp%2F10857759.html)

SpringCache支持`java.util.Optional`，仅在支持时才将其内容作为缓存。#result始终引用业务实体，而不引用受支持的包装器。因此可以重写为下面代码：



```java
@Cacheable(cacheNames="books", key="#isbn.rawNumber",unless ="#result?.id >1000" )
public Optional<Book> findBook(ISBN isbn, boolean checkWarehouse, boolean includeUsed) {
    log.info("执行方法！");
    Book book = Book.builder().id(111).bookName("java").build();
    Optional<Book> optionalBook = Optional.of(book);
    return optionalBook;
}
```

> 请注意：result仍然指的是Book而不是Optional。



#### @CachePut注解

在不影响方法执行的情况下更新缓存，可以使用@CachePut注解。也就是说，该方法始终执行，将其结果放入缓存（根据@CachePut选项）。他支持与@Cacheable缓存相同的属性。但是它应用于缓存填充而不是方法优化。



```kotlin
@CachePut(cacheNames="book", key="#isbn")
public Book updateBook(ISBN isbn, BookDescriptor descriptor)
```

![img](https:////upload-images.jianshu.io/upload_images/16013479-b3278f8aba506e91.png?imageMogr2/auto-orient/strip|imageView2/2/w/508/format/webp)



#### @CacheEvict

Spring Cache不仅允许缓存的填充，还允许删除缓存。此过程对于从缓存中删除陈旧或未使用的数据很有用，相对于@Cacheable，@CacheEvict是从缓存中删除数据的注解。@CacheEvict需要指定一个或多个受操作影响的缓存，允许自定义缓存和Key或者条件。

1. **`allEntries`**，该参数指示是否需要在整个缓存范围内逐出而不仅仅是基于Key的逐条逐出。
    代码1：逐出books缓存中的所有条目。



```java
@CacheEvict(cacheNames="books", allEntries=true) 
public void loadBooks(InputStream batch)
```

1. **`beforeInvocation`**，该参数指定逐出缓存是在方法执行前还是方法执行后（默认方法执行后）。在默认情况下，如果方法未执行（可能已经被缓存）或者引发异常，缓存是不会被移除的。而`beforeInvocation=true`逐出缓存则是在方法调用前发生。适用于移除操作和方法结果没有必要联系的情况。

代码2：方法执行前移除缓存



```cpp
@CacheEvict(cacheNames = "books", key = "#isbn",beforeInvocation = true)
    public void loadBooks(ISBN isbn) {
    log.info("清除缓存！");
    //出现异常，默认不会清除缓存
    throw new RuntimeException("aa");
}
```

> 注：void方法可以与@CacheEvict一起使用，因为方法充当触发器，返回值将被忽略（因为他们不与缓存交互）。



#### Caching注解

指定多个相同类型的注解时（例如@CacheEvict或@CachePut）。因为Key或Key的表达式在不同的缓存间是不同的。@Caching允许嵌套多个@Cacheable，@CachePut和@CacheEvict注解来使用。

代码3：使用两个@CacheEvict注解

```java
@Caching(evict = { @CacheEvict("primary"), @CacheEvict(cacheNames="secondary", key="#p0") })
public Book importBooks(String deposit, Date date)
```



#### CacheConfig注解

@CacheConfig是一个类级别的注解，他允许共享cacheNames，custom KeyGenerator，custom CacheManager和custom CacheResolver。将此注解注释在类上不会打开任何缓存操作。

> 注：方法级别的注解会覆盖类注解





## CacheManager源码

> CacheManager是用来存储Cache对象。而Cache对象中存储的是K-V缓存数据，在宏观意义上将`CacheNames::Key`才是存储结构中实际的Key。Cache对象可以看做一组(配置)K-V数据。



CacheManager既然存储所有的Cache对象。那么开篇需要考虑两个问题：

1. Cache什么时候被CacheManager存储；
2. 存储Cache时，是否可以增强Cache功能；



### CacheManager在Spring Cache中的作用

**1. Spring Cache结构**

缓存抽象不提供实际的存储，而是依赖于`org.springframework.cache.Cache`和`org.springframework.cache.CacheManager`接口实现抽象，SpringCache的缓存架构如图一所示。

![img](https:////upload-images.jianshu.io/upload_images/16013479-21ca623b62a3a3cf.png?imageMogr2/auto-orient/strip|imageView2/2/w/405/format/webp)

图1-SpringCache的缓存架构.png

**2. Spring Cache流程**

1. 项目启动加载bean时，解析缓存注解，创建代理对象。
2. 调用方法时，直接调用代理对象，在代理方法中获取到指定的CacheManager对象。
3. 根据注解的cacheNames属性，在CacheManager中获取到Cache对象，然后调用`org.springframework.cache.Cache`中的方法对缓存数据进行操作。调用流程如图2所示。

![img](https:////upload-images.jianshu.io/upload_images/16013479-626dceba30fe4c10.png?imageMogr2/auto-orient/strip|imageView2/2/w/1017/format/webp)

图2-Spring Cache调用流程.png

- CacheManager：主要作用是存储某一数据结构（例如Redis）中所有Cache对象。
- Cache：作用是操纵Cache对象中的K-V数据。

### 如何定义实现CacheManager

自定义实现CacheManager对象，可以参考`org.springframework.data.redis.cache.RedisCacheManager`类，类继承结构如图3所示。

![img](https:////upload-images.jianshu.io/upload_images/16013479-a97aa81c53c9a813.png?imageMogr2/auto-orient/strip|imageView2/2/w/435/format/webp)

图3-RedisCacheManager继承结构图.png

#### AbstractCacheManager—定义算法骨架

`AbstractCacheManager`是`CacheManager`的子类。**定义了管理Cache的骨架算法**，自定义的CacheManager继承`org.springframework.cache.support.AbstractCacheManager`类后，只需要实现其中的钩子方法。AbstractCacheManager方法如图4所示。

![img](https:////upload-images.jianshu.io/upload_images/16013479-69a53dacdfd43cd8.png?imageMogr2/auto-orient/strip|imageView2/2/w/412/format/webp)

图4-CacheManager对象.png

| 属性       | 作用                                                  |
| :--------- | :---------------------------------------------------- |
| cacheMap   | ConcurrentMap<String, Cache>类型，用于缓存Cache对象。 |
| cacheNames | Set<String>类型，用于缓存CacheNames                   |

| 方法               | 作用                                                         |
| :----------------- | :----------------------------------------------------------- |
| getCache           | 模板方法(已定义算法逻辑)，子类需实现`getMissingCache`和`decorateCache`钩子方法。目的：将Cache放入cacheMap中，获取Cache对象。 |
| getCacheNames      | 获取`CacheNames`的集合。                                     |
| afterPropertiesSet | bean对象初始化方法，实际调用`initializeCaches()`方法。       |
| decorateCache      | 钩子方法(需要子类实现)，装饰`Cache`对象。                    |
| getMissingCache    | 钩子方法(需要子类实现)，若`CacheManager`不存在`Cache`对象时的处理策略。 |
| initializeCaches   | 模板方法，包含`loadCaches`方法，将Caches集合读取到缓存中。   |
| loadCaches         | 抽象钩子方法(需要子类实现)，加载配置中参数，创建Caches集合。 |
| lookupCache        | 根据key获取Cache对象。                                       |
| updateCacheNames   | 私有方法，更新CacheNames集合。                               |



#### AbstractTransactionSupportingCacheManager—实现事务支持

`AbstractTransactionSupportingCacheManager`类重写了`AbstractCacheManager#decorateCache`的方法，对Cache对象的功能进行加强，使其支持事务。

![img](https:////upload-images.jianshu.io/upload_images/16013479-793fabf74dcbdcdf.png?imageMogr2/auto-orient/strip|imageView2/2/w/415/format/webp)

图5-AbstractTransactionSupportingCacheManager类方法.png

将Cache对象保存到CacheManager中时，调用子类实现的decorateCache方法对Cache对象功能进行加强。

例如：`org.springframework.cache.transaction.AbstractTransactionSupportingCacheManager#decorateCache`若`transactionAware==true`，返回支持事务的Cache对象，否则返回普通的Cache对象。

![img](https:////upload-images.jianshu.io/upload_images/16013479-1aba51a3c17258bf.png?imageMogr2/auto-orient/strip|imageView2/2/w/794/format/webp)

image.png

`org.springframework.cache.transaction.TransactionAwareCacheDecorator`装饰Cache对象，重写put、evict和clear方法。只有事务真正提交，才会保存到缓存中。

![img](https:////upload-images.jianshu.io/upload_images/16013479-7008339932307e3b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1085/format/webp)

image.png



#### RedisCacheManager—自定义的CacheManager

RedisCacheManager虽继承自`CacheManager`接口，但在`AbstractCacheManager`中重写了`getCache(String)`等方法逻辑。而子类只需要实现图4红圈中的方法。

![img](https:////upload-images.jianshu.io/upload_images/16013479-354694af89e421d6.png?imageMogr2/auto-orient/strip|imageView2/2/w/424/format/webp)

图4-RedisCacheManager实现的方法.png



##### 钩子方法

**1. 抽象钩子方法(强制实现)—loadCaches(加载配置中Cache对象集合)**
 在项目启动时，读取配置文件中的参数，来初始化自定义的CacheManager对象。

`AbstractCacheManager#afterPropertiesSet`方式是在构造方法之后，init方法之前执行，完成类的初始化操作。

![img](https:////upload-images.jianshu.io/upload_images/16013479-9f4af99b3d442452.png?imageMogr2/auto-orient/strip|imageView2/2/w/634/format/webp)

image.png

**子类必须重写`AbstractCacheManager#loadCaches`方法**，读取配置文件的参数，构建对应的`XxxCache`对象集合，在`AbstractCacheManager#initializeCaches`方法中将Cache集合保存到cacheMap中。

子类的loadCaches方法可以返回空集合但是不能返回null。

在RedisCacheManager中，我们可以在配置文件中加载（不同配置的）Cache对象(例如：Cache1设置60s的超时时间，Cache2设置30s超时时间)，**从而解决Spring Cache无法设置过期时间的缺陷！**

![img](https:////upload-images.jianshu.io/upload_images/16013479-cbfdd03094bf1bf5.png?imageMogr2/auto-orient/strip|imageView2/2/w/919/format/webp)

image.png

**2. 具体钩子方法(选择性实现)—decorateCache(装饰Cache对象)**
 `AbstractCacheManager#decorateCache`作用就是装饰Cache对象，加强功能。实际上Cache对象中定义了对缓存的增改删查等操作。

**3. 具体钩子方法(必须实现)—getMissingCache(处理Miss缓存)**
 在CacheManager增加缓存的方法实际上有两种，一种是读取配置文件中的Cache配置列表；另一种是在获取Cache对象时，若不存在便创建Cache对象，并将其放入到`cacheMap`中。

![img](https:////upload-images.jianshu.io/upload_images/16013479-e02dc797193c219c.png?imageMogr2/auto-orient/strip|imageView2/2/w/488/format/webp)

image.png



`AbstractCacheManager#getMissingCache`方法是一个具体的钩子方法，默认返回null。子类`RedisCacheManager#getMissingCache`中重写了该方法。根据`allowInFlightCacheCreation`属性来决定是否构建Cache对象。

![img](https:////upload-images.jianshu.io/upload_images/16013479-7fb1da598a74c955.png?imageMogr2/auto-orient/strip|imageView2/2/w/829/format/webp)

image.png

如果`getMissingCache`方法返回null，即CacheManager中不存在对应的Cache对象，SpringCache会抛出异常。



#####  创建自定义Cache

实际上RedisCacheManager中存储的是RedisCache对象。根据我们的分析。缓存Cache对象的方法主要有两种，一种是在初始化中读取配置文件配置；一种是发现不存在时创建。但是都是调用了`RedisCacheManager#createRedisCache`方法。

![img](https:////upload-images.jianshu.io/upload_images/16013479-16c9771e37ceacfd.png?imageMogr2/auto-orient/strip|imageView2/2/w/930/format/webp)

image.png



##### RedisCache的配置

可以在配置文件中对`org.springframework.data.redis.cache.RedisCacheConfiguration`属性进行配置，也就是对Redis进行配置。

![img](https://upload-images.jianshu.io/upload_images/16013479-9b3c904fa724e814.png?imageMogr2/auto-orient/strip|imageView2/2/w/661/format/webp)

imag





## Cache Aside 模式下解决一致性问题

`@Cacheable`一般用于优化方法，而`@CachePut`一般用于填充缓存。

`@CachePut`一般用于更新/插入方法。理论上是在此处实现数据库与缓存的一致性。

翻看源码：**实际上SpringCache采用的是先更新数据库，再更新缓存的策略**。这会导致：

![img](https:////upload-images.jianshu.io/upload_images/16013479-08118e33998a5d98.png?imageMogr2/auto-orient/strip|imageView2/2/w/516/format/webp)

先更新数据库，在更新缓存导致数据不一致

线程A先更新DB，准备更新Redis时，被阻塞。线程B更新DB，并更新Redis后，线程A拿着**旧值**填充Redis，造成Redis与DB数据不一致。



```java
//源码：org.springframework.cache.interceptor.CacheAspectSupport#execute(org.springframework.cache.interceptor.CacheOperationInvoker, java.lang.reflect.Method, org.springframework.cache.interceptor.CacheAspectSupport.CacheOperationContexts)
// 将@CachePut和@Cacheable miss的值保存到缓存中。
for (CachePutRequest cachePutRequest : cachePutRequests) {
    cachePutRequest.apply(cacheValue);
}

//会执行Cache的put操作，将数据放入到缓存中。
public void apply(@Nullable Object result) {  
    if (this.context.canPutToCache(result)) {  
        for (Cache cache : this.context.getCaches()) {  
            doPut(cache, this.key, result);  
        }  
    }  
}  

//doPut操作最终会自执行org.springframework.data.redis.cache.RedisCache#put方法，若是缓存中已经存在记录，
//会将该记录更新。
@Override  
public void put(Object key, @Nullable Object value) {  

    Object cacheValue = preProcessCacheValue(value);  

    if (!isAllowNullValues() && cacheValue == null) {  

        throw new IllegalArgumentException(String.format(  
            "Cache '%s' does not allow 'null' values. Avoid storing null via '@Cacheable(unless=\"#result == null\")' or configure RedisCache to allow 'null' via RedisCacheConfiguration.",  
            name));  
    }  

    cacheWriter.put(name, createAndConvertCacheKey(key), serializeCacheValue(cacheValue), cacheConfig.getTtl());  
}  
```

> 选择什么策略去保证数据一致性问题呢？

[分布式之数据库和缓存双写一致性方案解析](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzU5NjY2MzEyMA%3D%3D%26mid%3D2247483656%26idx%3D1%26sn%3D5668a4d9dd1fe0e028e4c1d4bcf5d427%26chksm%3Dfe5e0f37c92986218653450cd60fc88a65f29acda51c13b84602fc421786bb549f858e56aee0%26mpshare%3D1%26scene%3D1%26srcid%3D1202kyWVTYnveqpQ5Io4C75s%26sharer_sharetime%3D1575248731198%26sharer_shareid%3D63fed49079c3a76703649fd65f9e220e%26key%3D259fe527a3dbfab37e85d3ef4f9f803dd722610a705578658f43e82bf5ce3c75260d0f7a35bf4bf701e04fb4d70667d4847b6adaab8466db6cf3ed000c5e3ff7059cf9adf7e08c6e89ed3cca69f26a76%26ascene%3D1%26uin%3DMTIyMzM4MjIyNA%3D%3D%26devicetype%3DWindows%2B7%26version%3D62070158%26lang%3Dzh_CN%26pass_ticket%3DCiasNt1biARzYZDFNUfzvG3WgXsXoWZO4LBZi9v6lS4i0WjB3n8g4n1S%2FKfruBwT)

采用的是**先更新数据库，在删除缓存的策略。**一般情况下因为DB`写操作`比`读操作`耗时。即更新操作（2）一般会在查询操作（3）之后。

![img](https:////upload-images.jianshu.io/upload_images/16013479-b13aeb3a3c49cca6.png?imageMogr2/auto-orient/strip|imageView2/2/w/526/format/webp)

更新DB，在删缓存导致数据不一致的极端情况.png

遇到上述问题，我们可以在开启一个定时线程，1s（根据业务方法执行时间来大概推算）后再次删除Redis的缓存。

> 如何对源码二次开发

SpringCache中，Cache对象是真正的对缓存进行增改删除的对象，所以我们的目的就是装饰RedisCache对象。

Cache对象是由CacheManager对象生产的。Spring使用模板方法模式实现CacheMananger接口，留给我们可扩展的方法大概是：

1. 项目启动时，读取Cache的配置，创建Cache对象；
2. 查询Cache得不到时，根据默认配置创建Cache对象；
3. 装饰Cache对象；

咋一看3中可以满足我们的需求，但是源码中已经使用`事务`装饰了Cache对象。我们不能再次重写该方法。

只能在生成Cache时去装饰。无论是`项目启动时`还是`查询Cache得不到`，都需要调用**`RedisCache#createRedisCache`**去生成Cache对象，于是我们可以去重写该方法，装饰Cache对象。



```java
public class LocalRedisCacheManager extends RedisCacheManager {
    
    public LocalRedisCacheManager(RedisCacheWriter cacheWriter, RedisCacheConfiguration defaultCacheConfiguration, Map<String, RedisCacheConfiguration> initialCacheConfigurations) {
        super(cacheWriter, defaultCacheConfiguration, initialCacheConfigurations);
    }

    //子类重写该方法，装饰生成的Cache对象。
    @Override
    protected RedisCache createRedisCache(String name, RedisCacheConfiguration cacheConfig) {
        return  new LocalRedisCache(super.createRedisCache(name, cacheConfig));
    }
}
```



```java
@Slf4j
public class LocalRedisCache extends RedisCache {

    private RedisCache cache;
    private RedisCacheWriter cacheWriter;
    private String name;


    /**
     * 调用父类的方法
     *
     * @param cache
     */
    public LocalRedisCache(RedisCache cache) {
        super(cache.getName(), cache.getNativeCache(), cache.getCacheConfiguration());
        this.cache = cache;
        this.cacheWriter = cache.getNativeCache();
        this.name = cache.getName();
    }

    @Override
    public void put(Object key, Object value) {

        //插入之前，若是缓存中已经存在值
        byte[] oldValue = cacheWriter.get(name, createAndConvertCacheKey(key));
        //先更新数据库，其次删除缓存
        if (oldValue != null) {
            log.warn("该数据存在，推测为@CachePut操作。进行移除缓存，确保缓存一致性！");
            cache.evict(key);
          //可以增加线程，实现延迟双删
            return;
        }

        log.warn("数据不存在，填入缓存");
        //若不存在，则插入缓存
        cache.put(key, value);
    }

    private byte[] createAndConvertCacheKey(Object key) {
        return serializeCacheKey(createCacheKey(key));
    }
}
```



```dart
//配置文件
RedisCacheWriter redisCacheWriter = RedisCacheWriter.lockingRedisCacheWriter(connectionFactory);  
LocalRedisCacheManager localRedisCache = new LocalRedisCacheManager(redisCacheWriter,  
        customProtoStuffRedisCacheConfiguration(Duration.ofSeconds(6000)),  
        cacheConfigurations);  
```



