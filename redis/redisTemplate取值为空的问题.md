# redisTemplate取值为空

在开发过程中发现redis数据库中存在数据，但是redisTemplate取数失败。

在：https://stackoverflow.com/questions/35783004/spring-data-redis-ping-works-key-is-there-no-data-returned

找到问题原因。

> `RedisOperations` uses [serializers](http://docs.spring.io/spring-data/redis/docs/current/reference/html/#redis:serializer) to [translate Java objects](http://docs.spring.io/spring-data/redis/docs/current/reference/html/#redis:template) into Redis data structure values. The serializer defaults to `JdkSerializationRedisSerializer`. The JDK serializer translates your `String` object into a Java-serialized representation that is not compatible with ASCII or UTF-8. Check out the docs, you might be interested in `StringRedisSerializer`. The serializer needs to be set in `RedisTemplate`:

```
@Bean
RedisTemplate<String, String> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
    RedisTemplate<String, String> redisTemplate = new RedisTemplate<>();

    redisTemplate.setDefaultSerializer(new StringRedisSerializer());

    redisTemplate.setConnectionFactory(redisConnectionFactory);
    redisTemplate.afterPropertiesSet();
    return redisTemplate;
}
```

发现问题处在redis配置上，错误配置：

```
    @Bean
    public RedisTemplate<String, Object> redisCacheTemplate(LettuceConnectionFactory lettuceConnectionFactory) {
        // springboot2默认redisTemplate使用lettuce客户端
        // 直接在配置中配置spring.redis.lettuce.pool相关参数即可使用lettuce资源池
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        template.setConnectionFactory(lettuceConnectionFactory);
        template.setEnableTransactionSupport(true);
        return template;
    }
```

redisCacheTemplate 生成的bean名为redisCacheTemplate的上下文bean。直接装配：

```
    @Autowired
    RedisTemplate redisTemplate;
```

拿到的是redisTemplate，并没有配置`JdkSerializationRedisSerializer`，导致取值失败。

查看上下文bean：

```
{
aliases: [ ],
scope: "singleton",
type: "org.springframework.data.redis.core.RedisTemplate",
resource: "class path resource [com/cmit/mvne/billing/settle/config/RedisConfig.class]",
dependencies: [
"redisConnectionFactory"
]
},

redisTemplate: {
aliases: [ ],
scope: "singleton",
type: "org.springframework.data.redis.core.RedisTemplate",
resource: "class path resource [org/springframework/boot/autoconfigure/data/redis/RedisAutoConfiguration.class]",
dependencies: [
"redisConnectionFactory"
]
},
```

