# Spring Cache基本使用

转自：https://www.baeldung.com/spring-cache-tutorial#conditional-caching

Spring提供的核心缓存抽象位于 *[spring-context](https://search.maven.org/search?q=g:org.springframework a:spring-context)* 模块中。因此，在使用Maven时，我们的 *pom.xml* 应包含以下依赖项：

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.8.RELEASE</version>
</dependency>
```

有趣的是，还有一个名为*[spring-context-support的](https://search.maven.org/search?q=g:org.springframework a:spring-context-support)*模块 *，*它位于*spring-context* 模块的顶部，并提供了由[EhCache](https://www.baeldung.com/spring-boot-ehcache)或[Caffeine等](https://www.baeldung.com/java-caching-caffeine)支持的更多 *CacheManager* 。如果要使用这些作为缓存存储，请改为使用 *spring-context-support* 模块：

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>5.1.8.RELEASE</version>
</dependency>
```

由于 *spring-context-support*模块可传递地依赖于*spring-context* 模块，因此不需要单独的*spring-context*依赖声明 *。*



如果您是Spring Boot用户，请使用 *[spring-boot-starter-cache](https://search.maven.org/search?q=g:org.springframework.boot a:spring-boot-starter-cache)* starter软件包轻松添加缓存依赖项：

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

起动器在包含了 *spring-context-support* 模块。



## 启用缓存

为了启用缓存，Spring充分利用了注释，就像在框架中启用任何其他配置级别的功能一样。

可以通过将*@EnableCaching*批注添加到任何配置类中来声明性地启用缓存功能：

```
@Configuration
@EnableCaching
public class CachingConfig {
 
    @Bean
    public CacheManager cacheManager() {
        return new ConcurrentMapCacheManager("addresses");
    }
}
```

当然，您也可以**通过XML**配置**启用缓存管理**：

```
<beans>
    <cache:annotation-driven />
 
    <bean id="cacheManager" class="org.springframework.cache.support.SimpleCacheManager">
        <property name="caches">
            <set>
                <bean
                  class="org.springframework.cache.concurrent.ConcurrentMapCacheFactoryBean"
                  name="addresses"/>
            </set>
        </property>
    </bean>
</beans>
```

**注意：**在启用缓存之后（为了进行最小的设置），我们**必须**注册一个*cacheManager*。

**使用XML确实提供了更多灵活的选项**来配置缓存-您可以指定自己的Cache-Manager，Cache-Resolver，Error-Handler，并且通常使用更高级的自定义选项（有关更多详细信息，[请参阅Javadoc](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/cache/annotation/CachingConfigurer.html)）。



在spring boot 下：

使用Spring Boot时，仅在classpath上包含Starter包以及 *EnableCaching* 批注会注册相同的 *ConcurrentMapCacheManager。* 因此，不需要单独的bean声明。

另外，我们可以使用一个或多个 *CacheManagerCustomizer * bean 自定义[自动配置的](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/cache/CacheAutoConfiguration.java) *CacheManager* ：

```
@Component
public class SimpleCacheCustomizer 
  implements CacheManagerCustomizer<ConcurrentMapCacheManager> {
 
    @Override
    public void customize(ConcurrentMapCacheManager cacheManager) {
        cacheManager.setCacheNames(asList("users", "transactions"));
    }
}
```

该 *[CacheAutoConfiguration](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/cache/CacheAutoConfiguration.java)* 自动配置拿起这些定制，并将其应用到当前 *的CacheManager* 其完整的初始化之前。



## 使用带注释缓存

启用缓存后，下一步就是使用声明性注释将缓存行为绑定到方法。

### @Cacheable

启用方法的缓存行为的最简单方法是使用*@Cacheable对其*进行划分，并使用存储结果的缓存名称对其进行参数化：

```
@Cacheable("addresses")
public String getAddress(Customer customer) {...}
```

在实际调用方法之前，*getAddress（）*调用将首先检查缓存*地址*，然后将结果缓存。

虽然在大多数情况下，一个缓存就足够了，但是Spring框架还支持将多个缓存作为参数传递：

```
@Cacheable({"addresses", "directory"})
public String getAddress(Customer customer) {...}
```

在这种情况下，如果任何缓存包含所需的结果，则将返回结果，并且不会调用该方法。



### @CacheEvict

现在，使所有方法*@Cacheable*成为什么问题？

问题是: **我们不想用不需要的值填充缓存**。高速缓存可能会变得非常大，非常快，并且我们可能会保留大量过时或未使用的数据。

该*@CacheEvict*注解用于指示去除一个或多个/所有的价值观-让新鲜值可以重新加载到缓存中：

```
@CacheEvict(value="addresses", allEntries=true)
public String getAddress(Customer customer) {...}
```

在这里，我们将附加参数*allEntries*与要清空的缓存一起使用-清除缓存*地址*中的所有条目，并为新数据做准备。



### @CachePut

尽管*@CacheEvict*通过删除陈旧和未使用的条目*来*减少在大型缓存中查找条目的开销，但理想情况下，您还是要**避免从缓存中删除过多的数据**。

取而代之的是，您希望在更改条目时有选择地，智能地更新条目。

使用*@CachePut*批注，可以在不干扰方法执行的情况下更新缓存的内容。也就是说，该方法将始终执行并缓存结果。

```
@CachePut``(value=``"addresses"``)``public` `String getAddress(Customer customer) {...}
```

之间的区别*@Cacheable*和*@CachePut*是*@Cacheable*将**跳过运行的方法**，而*@CachePut*将**实际运行的方法**，然后把它的结果在缓存中。



### @Caching

如果要使用相同类型的多个注释来缓存方法，该怎么办。看下面的错误示例：

```
@CacheEvict``(``"addresses"``)``@CacheEvict``(value=``"directory"``, key=customer.name)``public` `String getAddress(Customer customer) {...}
```

上面的代码将无法编译，因为Java不允许为给定方法声明相同类型的多个注释。

上述问题的解决方法是：

```
@Caching``(evict = { `` ``@CacheEvict``(``"addresses"``), `` ``@CacheEvict``(value=``"directory"``, key=``"#customer.name"``) })``public` `String getAddress(Customer customer) {...}
```

如上面的代码片段所示，您可以使用*@Caching将***多个缓存注释***分组*，并使用它来实现自己的自定义缓存逻辑。



### @CacheConfig

使用*@CacheConfig*批注，您可以**在类级别将某些缓存配置简化到一个位置，**从而不必多次声明：

```
@CacheConfig(cacheNames={"addresses"})
public class CustomerDataService {
 
    @Cacheable
    public String getAddress(Customer customer) {...}
```



### **Conditional Caching**

有时，缓存可能不适用于所有情况下的方法。

例如–重用*@CachePut*批注中的示例–这将同时执行方法并每次都缓存结果：

```
@CachePut(value="addresses")
public String getAddress(Customer customer) {...}
```



### **Condition Parameter**

现在–如果我们想更好地控制注释的启用时间– 可以使用带有SpEL表达式的条件参数对*@CachePut*进行参数化，以确保基于对该表达式的求值来缓存结果：

```
@CachePut(value="addresses", condition="#customer.name=='Tom'")
public String getAddress(Customer customer) {...}
```



### **Unless Parameter**

我们还可以**根据方法的输出而不是输入**来控制缓存–通过*exclude*参数：

```
@CachePut(value="addresses", unless="#result.length()<64")
public String getAddress(Customer customer) {...}
```

上面的注释将缓存地址，除非它们少于64个字符。

重要的是要知道*条件*和*除非*参数可以与所有缓存注释一起使用。

这种条件缓存对于管理大型结果和基于输入参数自定义行为（而不是对所有操作强制执行一般行为）非常有用。



## **基于声明式XML的缓存**

如果您无权访问应用程序的源代码或想要从外部注入缓存行为，则还可以使用基于声明式XML的缓存。

这是我们的XML配置：

```
<!-- the service that you wish to make cacheable -->
<bean id="customerDataService"
  class="com.your.app.namespace.service.CustomerDataService"/>
 
<bean id="cacheManager"
  class="org.springframework.cache.support.SimpleCacheManager"> 
    <property name="caches"> 
        <set> 
            <bean
              class="org.springframework.cache.concurrent.ConcurrentMapCacheFactoryBean"
              name="directory"/> 
            <bean
              class="org.springframework.cache.concurrent.ConcurrentMapCacheFactoryBean"
              name="addresses"/> 
        </set> 
    </property> 
</bean>
<!-- define caching behavior -->
<cache:advice id="cachingBehavior" cache-manager="cacheManager">
    <cache:caching cache="addresses">
        <cache:cacheable method="getAddress" key="#customer.name"/>
    </cache:caching>
</cache:advice>
 
<!-- apply the behavior to all the implementations of CustomerDataService interface->
<aop:config>
    <aop:advisor advice-ref="cachingBehavior"
      pointcut="execution(* com.your.app.namespace.service.CustomerDataService.*(..))"/>
</aop:config>
```



等效基于Java配置：

这是等效的Java配置：

```
@Configuration
@EnableCaching
public class CachingConfig {
 
    @Bean
    public CacheManager cacheManager() {
        SimpleCacheManager cacheManager = new SimpleCacheManager();
        cacheManager.setCaches(Arrays.asList(
          new ConcurrentMapCache("directory"), 
          new ConcurrentMapCache("addresses")));
        return cacheManager;
    }
}
```

这是我们的*CustomerDataService*：

```
@Component
public class CustomerDataService {
  
    @Cacheable(value = "addresses", key = "#customer.name")
    public String getAddress(Customer customer) {
        return customer.getAddress();
    }
}
```