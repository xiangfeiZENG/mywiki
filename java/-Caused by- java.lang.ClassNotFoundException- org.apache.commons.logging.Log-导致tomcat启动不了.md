# "Caused by: java.lang.ClassNotFoundException: org.apache.commons.logging.Log"导致tomcat启动不了

标签（空格分隔）： java

---

近日在eclipse中导入了一个web项目，启动tomcat时报错：
```
java.util.concurrent.ExecutionException: org.apache.catalina.LifecycleException: Failed to start component [StandardEngine[Catalina].StandardHost[localhost].StandardContext[/source_aiesb]]
	at java.util.concurrent.FutureTask.report(FutureTask.java:122)
	at java.util.concurrent.FutureTask.get(FutureTask.java:188)
	at org.apache.catalina.core.ContainerBase.startInternal(ContainerBase.java:1122)
	at org.apache.catalina.core.StandardHost.startInternal(StandardHost.java:819)
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:150)
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1574)
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1564)
	at java.util.concurrent.FutureTask.run(FutureTask.java:262)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:745)
Caused by: org.apache.catalina.LifecycleException: Failed to start component [StandardEngine[Catalina].StandardHost[localhost].StandardContext[/source_aiesb]]
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:154)
	... 6 more
Caused by: java.lang.NoClassDefFoundError: Lorg/apache/commons/logging/Log;
	at java.lang.Class.getDeclaredFields0(Native Method)
	at java.lang.Class.privateGetDeclaredFields(Class.java:2509)
	at java.lang.Class.getDeclaredFields(Class.java:1819)
	at org.apache.catalina.util.Introspection.getDeclaredFields(Introspection.java:106)
	at org.apache.catalina.startup.WebAnnotationSet.loadFieldsAnnotation(WebAnnotationSet.java:270)
	at org.apache.catalina.startup.WebAnnotationSet.loadApplicationServletAnnotations(WebAnnotationSet.java:139)
	at org.apache.catalina.startup.WebAnnotationSet.loadApplicationAnnotations(WebAnnotationSet.java:65)
	at org.apache.catalina.startup.ContextConfig.applicationAnnotationsConfig(ContextConfig.java:415)
	at org.apache.catalina.startup.ContextConfig.configureStart(ContextConfig.java:892)
	at org.apache.catalina.startup.ContextConfig.lifecycleEvent(ContextConfig.java:386)
	at org.apache.catalina.util.LifecycleSupport.fireLifecycleEvent(LifecycleSupport.java:117)
	at org.apache.catalina.util.LifecycleBase.fireLifecycleEvent(LifecycleBase.java:90)
	at org.apache.catalina.core.StandardContext.startInternal(StandardContext.java:5405)
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:150)
	... 6 more
Caused by: java.lang.ClassNotFoundException: org.apache.commons.logging.Log
	at org.apache.catalina.loader.WebappClassLoader.loadClass(WebappClassLoader.java:1858)
	at org.apache.catalina.loader.WebappClassLoader.loadClass(WebappClassLoader.java:1709)
	... 20 more
```

看具体错误是因为tomcat启动时找不到commons-log包，“Caused by: java.lang.NoClassDefFoundError: Lorg/apache/commons/logging/Log;”，核查明明已经在classpath中加入了commons-log包，折腾了很久才发现这是**eclipse的一个bug**。

eclipse的web项目如果需要引用其他的jar包，
jar包必须加入WEB-INF/lib才有用 （会自动导入web app包里）

如果仅仅是build path引用本地的jar包
效果与没导入jar包的效果一样，相当于没导包。

> This error is cause by the inclusion of the servlet*.jar in your runtime, it only needs to be available during build time. The servlet container (e.g. tomcat) should provide the servlet*.jar for you at runtime.

> Caused by: java.lang.NoClassDefFoundError: Lorg/apache/commons/logging/Log;
You are probably missing the commons logging jar. make it available, either in the webapp/WEB-INF/lib or container lib.

重新复制一份jar包到web-inf的lib下，问题解决。



