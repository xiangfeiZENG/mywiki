# Mybatis Plus



Mybatis劣势：

- 简单CRUD操作还得写SQL语句（可以用generater弥补）
- XML中有大量的SQL要维护
- MyBatis自身功能很有限，但支持Plugin



MyBatis-Plus：是一个Mybatis的增强工具，只做增强不做改变。

![framework](/Users/zengxiangfei/Documents/mywiki/database/images/mybatis-plus-framework.png)



MyBatis-Plus特性

- 无侵入、损耗小、强大的CRUD操作
- 支持Lambda形式调用、支持多种数据库
- 支持主键自动生成、支持ActiveRecord模式
- 支持自定义全局通用操作、支持关键词自动转义
- 内置代码生成器、内置分页插件、内置性能分析插件
- 内置全局拦截插件、内置Sql注入剥离器



分页查询：

- MyBatis分页介绍

  不是物理分页（内存分页）

- MP分页插件实现物理分页

```java
@Configuration
public class MybatisPlusConfigure {
    /**
     * 分页插件
     */
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        List<ISqlParser> sqlParserList = new ArrayList<>();
        // 攻击 SQL 阻断解析器、加入解析链
        sqlParserList.add(new BlockAttackSqlParser());
        paginationInterceptor.setSqlParserList(sqlParserList);
        return paginationInterceptor;
    }
}
```





融合sql和mp使用

 ```
select * from user ${ew.customSqlSegment}


 ```



AR模式

- AR模式简介

  对象动态关联表

- MP中AR模式的实现



主键策略

- MP支持的主键策略介绍

  ```
  @Getter
  public enum IdType {
      /**
       * 数据库ID自增
       */
      AUTO(0),
      /**
       * 该类型为未设置主键类型
       */
      NONE(1),
      /**
       * 用户输入ID
       * <p>该类型可以通过自己注册自动填充插件进行填充</p>
       */
      INPUT(2),
  
      /* 以下3种类型、只有当插入对象ID 为空，才自动填充。 */
      /**
       * 全局唯一ID (idWorker)
       */
      ID_WORKER(3),
      /**
       * 全局唯一ID (UUID)
       */
      UUID(4),
      /**
       * 字符串全局唯一ID (idWorker 的字符串表示)
       */
      ID_WORKER_STR(5);
  
      private final int key;
  
      IdType(int key) {
          this.key = key;
      }
  }
  ```

  

- 局部主键策略实现

- 全局主键策略实现









