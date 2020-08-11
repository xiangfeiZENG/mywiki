# Spring Boot 精简jar包

通过spring-boot-thin-launcher实现，

工具包代码地址：https://github.com/spring-projects-experimental/spring-boot-thin-launcher

使用手册参考：https://www.baeldung.com/spring-boot-thin-jar



其他解决办法：

**https://phauer.com/2019/no-fat-jar-in-docker-image/**

## 简介

本文将研究**如何使用[\*spring-boot-thin-launcher\*](https://github.com/dsyer/spring-boot-thin-launcher)项目将Spring Boot项目构建到一个精简的JAR文件中。**

Spring Boot以其“胖” JAR部署而闻名，其中一个可执行工件包含应用程序代码及其所有依赖项

Spring Boot也广泛用于开发微服务。有时这与“胖JAR”方法是矛盾的，因为在许多工件中一遍又一遍地包含相同的依赖项可能会浪费大量资源。



Spring Boot Thin Launcher是一个小型库，它从归档文件本身捆绑的文件中读取工件的依赖项，从Maven存储库中下载它们，最后启动应用程序的主类。

因此，**当我们使用该库构建项目时，我们将获得一个包含代码的JAR文件，一个枚举其依赖性的文件以及执行上述任务的库中的主类。**



## 基本用法

### Maven以赖

```
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <dependencies>
        <!-- The following enables the "thin jar" deployment option. -->
        <dependency>
            <groupId>org.springframework.boot.experimental</groupId>
            <artifactId>spring-boot-thin-layout</artifactId>
            <version>1.0.11.RELEASE</version>
        </dependency>
    </dependencies>
</plugin>
```

该[launcher](https://search.maven.org/classic/#search|ga|1|a%3A"spring-boot-thin-layout")将读取的依赖关系*的pom.xml*文件，该文件在生成的JAR Maven的商店*META-INF / Maven的*目录。

**我们将像往常一样执行构建，例如，使用\*mvn install\*。**

If we want to be able to produce both thin and fat builds (for example in a project with multiple modules) we can declare the custom layout in a dedicated Maven profile.



### **Maven和依赖项：\*thin.properties\***

**除了\*pom.xml\*之外，我们还可以让Maven生成\*thin.properties\*文件。**在这种情况下，文件将包含依赖项的完整列表，包括可传递的依赖项，并且启动程序将首选它而不是*pom.xml*。

这样做的mojo（插件）是*spring-boot-thin-maven-plugin：properties，*默认情况下，它在*src / main / resources / META-INF中*输出*thin.properties*文件，但是我们可以使用在*thin.output*属性：

```
$ mvn org.springframework.boot.experimental:spring-boot-thin-maven-plugin:properties -Dthin.output=.
```

请注意，即使我们保留了默认目录，输出目录也必须存在才能成功实现目标。







## 其他方法

# 写在前面

在如今程序员的世界中，spring boot 越来越流行，不管是开发web应用还是构建spring cloud 微服务架构都离不开它，

不同于传统的web应用 需要单独部署容器来发布war包， spring boot 应用可以把整个项目打包成我们熟悉的jar来运行，大大方便了我们的开发部署。

# 问题凸显

上述提到Spring boot将整个应用打成一个Jar来执行，大大提高了我们的效率。

但是同时也给我们带来了烦恼，随着我们项目的不但迭代，也导致Jar不断的肥胖，对于高速迭代的项目上传一个如此肥胖的Jar简直痛不欲生。

那怎么办？ 程序员是一个懒人职业，总会想到办法来一次搞定这些问题的。

下面就让我们来看看吧！

# 解决方案

解决上述问题，只需要如下几步就可以搞定了。

1. 通常我们是用spring-boot-maven-plugin 进行打包、通过阅读文档发现可以通过配置使得该插件在打包时忽略特定的依赖，文档：[spring-boot-maven-plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin/start-mojo.html#excludeGroupIds)
2. 首先备份原先的依赖： 可以用使用 mvn dependency:copy-dependencies 首先将依赖导出。
3. 将一些变化不大的 jar copy 到外部文件 lib 文件夹中（和 pom 文件中配置对应 ）
4. 修改 pom 文件

```
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                                <layout>ZIP</layout>
                                <!--去除在生产环境中不变的依赖-->
                                <excludeGroupIds>
                                     org.springframework.boot,
                                     org.springframework,
                                     org.springframework.data,
                                     org.apache.tomcat.embed
                               </excludeGroupIds>
                            </configuration>     
        </plugin>
    </plugins>
</build>            
```

> 注：layout 必须是 ZIP 、 excludeGroupIds 中时忽略也是就需要打在外部的 jar 、根据自己项目的情况进行配置,exclude的更多用法 请参考文档[spring-boot-maven-plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin/start-mojo.html#excludeGroupIds)

1. 项目启动 将 项目的 jar 和 刚创建的 lib 放在同级目录下（不是必须的）。
2. 启动项目： java -Dloader.path="lib/" -jar xx.jar

ok! 就这么简单的帮spring boot jar 减肥成功了。



利用插件输出lib

```
 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <layout>ZIP</layout>
                    <includes>
                        <include>
                            <groupId>com.example</groupId>
                            <artifactId>demo</artifactId>
                        </include>
                    </includes>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>copy-dependency</id>
                        <phase>package</phase>
                        <goals>
                            <goal>copy-dependencies</goal>
                        </goals>
                        <configuration>
                            <type>jar</type>
                            <includeTypes>jar</includeTypes>
                            <includeScope>runtime</includeScope>
                            <outputDirectory>${project.build.directory}/lib</outputDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```



身份证、结婚证

1. 住房申请表  住房公  --》

2. 身份证原件
3. 公积金联名卡原件
4. 配偶身份证、结婚证
5. 房产证复印件、购房发票或契税凭证（原件和复印件）

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        