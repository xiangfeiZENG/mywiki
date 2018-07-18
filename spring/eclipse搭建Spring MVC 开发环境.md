# eclipse搭建Spring MVC 开发环境

标签（空格分隔）： spring

[TOC]

---

最近学习spring框架，在本地搭建相应的开发环境。

参考文档：
参考1：[使用Eclipse构建Maven的SpringMVC项目](http://limingnihao.iteye.com/blog/830409)
参考2：http://blog.csdn.net/fulai0_0/article/details/42967481

参考1写的较为详细。

---

## 建立Maven项目

### 操作步骤

1. 安装eclipse相关maven插件，地址：
> maven - http://m2eclipse.sonatype.org/sites/m2e
2. 新建项目，选择`maven-archetype-webapp`,`Group ID：org.springframework，artifactId:SpringMVC`


### 问题

问题一：
安装maven插件，创建maven项目，类型：`maven-archetype-webapp`
按照参考1步骤执行遇到以下错误：
```
Could not resolve archetype org.apache.maven.archetypes:maven-archetype-webapp:RELEASE from any of the configured repositories.
Could not resolve artifact
Failed to resolve version for org.apache.maven.archetypes:maven-archetype-webapp:pom:RELEASE: Could not find metadata org.apache.maven.archetypes:maven-archetype-webapp/maven-metadata.xml in local (C:\Users\zengxf02\.m2\repository)
Failed to resolve version for org.apache.maven.archetypes:maven-archetype-webapp:pom:RELEASE: Could not find metadata org.apache.maven.archetypes:maven-archetype-webapp/maven-metadata.xml in local (C:\Users\zengxf02\.m2\repository)
```

**解决方法**,在以下链接中下载相关jar包并安装。
http://maven.ibiblio.org/maven2/org/apache/maven/archetypes/maven-archetype-webapp/1.0/

这里缺少的是`maven-archetype-webapp`,通过下列命令安装即可。
```
mvn install:install-file -DgroupId=org.apache.maven.archetypes -DartifactId=maven-archetype-webapp -Dversion=1.0 -Dpackaging=jar -Dfile=maven-archetype-webapp-1.0.jar
```

错误二：
遇到以下错误：
```
Description	Resource	Path	Location	Type
Could not calculate build plan: 
Failure to transfer org.apache.maven.plugins:maven-resources-plugin:pom:2.4.3 
from http://repo1.maven.org/maven2 was cached in the local repository, resolution will not be reattempted until the update interval of central has elapsed or updates are forced. 
Original error: Could not transfer artifact org.apache.maven.plugins:maven-resources-plugin:pom:2.4.3 from/to central (http://repo1.maven.org/maven2): connection timed out	SpringMVC		Unknown	Maven Problem
```

通过手动下载`http://repo1.maven.org/maven2`中对应的maven-resources-plugin，此时出现以下错误：
```
Description	Resource	Path	Location	Type
Could not calculate build plan: Failure to transfer org.apache.maven.plugins:maven-plugins:pom:18 
from http://repo1.maven.org/maven2 was cached in the local repository, resolution will not be reattempted until the update interval of central has elapsed or updates are forced.
 Original error: Could not transfer artifact org.apache.maven.plugins:maven-plugins:pom:18 from/to central (http://repo1.maven.org/maven2): ConnectException	SpringMVC		Unknown	Maven Problem
```
查看eclipse后台报错日志（workspace下的.metadata中的.log)：
```
Caused by: org.sonatype.aether.transfer.ArtifactTransferException: Could not transfer artifact org.apache.maven.plugins:maven-plugins:pom:18 from/to central (http://repo1.maven.org/maven2): connection timed out
```
怀疑可能是网络原因。

解决方法：删掉maven的repository中maven plugin（D:\repository\org\apache\maven\plugins）中所有的数据，换了个网络，重新update Denpendencies。问题解决。

## 配置Maven项目

### 操作步骤

1. 添加source文件夹`src/main/java，src/test/java ，src/test/resources`,并更改其输出路径，在classPath配置Source选项中的Output中配置，分别配置为`target/classes`、`target/test-classes`。
2. 更改JDK版本。
3. 将项目转换为Dynamic Web项目（通过Project Facets），**注意：**点击Further configuration available…，弹出Modify Faceted Project窗口，此处是设置web.xml文件的路径，我们输入src/main/webapp
4. 设置部署程序集(Web Deployment Assembly)，删除test的两项，因为test是测试使用，并不需要部署；设置将Maven的jar包发布到lib下（Add -> Java Build Path Entries -> Maven Dependencies -> Finish)。
5. 修改pom.xml文件，添加spring依赖。详细见后面问题。



### 遇到问题

通过方案一直接通过add的方式添加spring-web不可行，报memoryOut，可能搜索出的内容太多，直接通过修改pom.xml的方式添加相关的依赖包。

编辑pom文件，添加相关依赖，update Denpendencies成功。
```xml
<dependencies>  
        <!-- junit -->  
        <dependency>  
            <groupId>junit</groupId>  
            <artifactId>junit</artifactId>  
            <version>4.12-beta-3</version>  
            <scope>test</scope>  
        </dependency>  
        <!-- log4j -->  
        <dependency>  
            <groupId>log4j</groupId>  
            <artifactId>log4j</artifactId>  
            <version>1.2.17</version>  
        </dependency>  
          
        <dependency>  
            <groupId>jstl</groupId>  
            <artifactId>jstl</artifactId>  
            <version>1.2</version>  
        </dependency>  
          
        <!-- spring 的基本依赖 开始 -->  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-core</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-expression</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-beans</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-aop</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-context</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-context-support</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-tx</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-web</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-jdbc</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-webmvc</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-aspects</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
  
        <dependency>  
            <groupId>org.springframework</groupId>  
            <artifactId>spring-test</artifactId>  
            <version>4.1.2.RELEASE</version>  
        </dependency>  
        <!-- spring 的基本依赖 结束 -->  
  </dependencies>  
```

## 构建SpringMVC框架

### 操作步骤

#### 编辑web.xml文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	     xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
	                         http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" 
	     version="2.5" >
	
	<!-- 区分项目名称，防止默认重名 -->
	<context-param>
		<param-name>webAppRootKey</param-name>
		<param-value>maven.example.root</param-value>
	</context-param>

	<!-- Spring的log4j监听器 -->
	<listener>
		<listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
	</listener>

	<!-- 字符集 过滤器  -->
	<filter>
		<filter-name>CharacterEncodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>CharacterEncodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

	<!-- Spring view分发器 -->
	<servlet>
		<servlet-name>dispatcher</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/dispatcher-servlet.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>dispatcher</servlet-name>
		<url-pattern>*.do</url-pattern>
	</servlet-mapping>

</web-app>
```

#### 编写Spring配置文件dispatcher-servlet.xml

此处可添加MVC驱动、注解检测、视图解析等。

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"   
       xmlns:aop="http://www.springframework.org/schema/aop"   
       xmlns:context="http://www.springframework.org/schema/context"  
       xmlns:mvc="http://www.springframework.org/schema/mvc"   
       xmlns:tx="http://www.springframework.org/schema/tx"   
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xsi:schemaLocation="http://www.springframework.org/schema/aop   
        http://www.springframework.org/schema/aop/spring-aop-3.0.xsd   
        http://www.springframework.org/schema/beans   
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd   
        http://www.springframework.org/schema/context   
        http://www.springframework.org/schema/context/spring-context-3.0.xsd   
        http://www.springframework.org/schema/mvc   
        http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd   
        http://www.springframework.org/schema/tx   
        http://www.springframework.org/schema/tx/spring-tx-3.0.xsd">  
  
    <mvc:annotation-driven />  
    <context:component-scan base-package="com.xiangfeizeng.SpringMVC" />  
  
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">  
        <property name="prefix" value="/WEB-INF/views/" />  
        <property name="suffix" value=".jsp" />  
    </bean>  
  
</beans>  
```

#### 编写一个Controller层测试类

编写一个SpringMVC的Controller层测试类。此类只有一个方法做地址映射，并向页面传递一个数据。代码如下：
```java
package com.xiangfeizeng.SpringMVC.view;


import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class GeneralController {

	@RequestMapping(value="index.do")
	public void index_jsp(Model model){
		model.addAttribute("xfei", "hello xfei!");
		System.out.println("index.jsp");
	}
}
```

#### 编写index.jsp页面

首先在src/main/webapp/WEB-INF下建文件夹views。此处和dispatcher-servlet.xml配置文件中的prefix属性路径要一样。
在views下建index.jsp文件，我们使用jstl获取Controlleradd的数据。

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Insert title here</title>
	</head>
	
	<body>
		<c:out value="${xfei}"></c:out>
	</body>
</html>
```

#### 发布到tomcat

此处根据参考2进行执行。

步骤：
1. Run As -> RunConfigurations 
2. 点击Browse Workspace选择项目
3. Goals中输入：tomcat:run
4. Configure中选择的是external maven（外部maven）

点击run报错：
```
-Dmaven.multiModuleProjectDirectory system property is not set. Check $M2_HOME environment variable and mvn script match.
```

在选择当前执行的jdk（install java），点击set，在VM arguments中添加：`-Dmaven.multiModuleProjectDirectory=$M2_HOME`,问题解决。

这里应该是通过maven下载了tomcat，然后再工程target中跑起来了。

#### 测试

访问：http://localhost:8080/SpringMVC/index.do
显示预期结果。


  