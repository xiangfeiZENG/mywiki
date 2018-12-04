## Spring 揭秘

[TOC]

![1539240874774](E:\Wiki\mywiki\images\1539240874774.png)



### IOC容器基本概念

全称为Inversion of Control，中文通常翻译为“控制反转”，它还有一个别名叫做依赖注入（Dependency Injection）。好莱坞原则“Don’t call us, we will call you.”

注意:本章更多的是将IoC和依赖注入看作等同的概念进行讲解。但是，在这一点上可能存在不同的观点，比如Expert Spring MVC and Web Flow和Expert One-on-One J2EE without EJB等书中都将依赖注入看作是IoC的一种方式。

![1539241501866](E:\Wiki\mywiki\images\1539241501866.png)

在IoC的场景中，二者之间通过IoC Service Provider来打交道，所有的被注入对象和依赖对象现在由IoC Service Provider统一管理。被注入对象需要什么，直接跟IoC Service Provider招呼一声，后者就会把相应的被依赖对象注入到被注入对象中，从而达到IoC Service Provider为被注入对象服务的目的。IoC Service Provider在这里就是通常的IoC容器所充当的角色。从被注入对象的角度看，与之前直接寻求依赖对象相比，依赖对象的取得方式发生了反转，控制也从被注入对象转到了IoC Service Provider那里。

其实IoC就这么简单！原来是需要什么东西自己去拿，现在是需要什么东西就让别人送过来。

IoC是一种可以帮助我们解耦各业务对象间依赖关系的对象绑定方式！



### IoC Service Provider

Spring的IoC容器就是一个提供依赖注入服务的IoC Service Provider。

IoC Service Provider的职责相对来说比较简单，主要有两个：业务对象的构建管理和业务对象间的依赖绑定。

- **业务对象的构建管理**：在IoC场景中，业务对象无需关心所依赖的对象如何构建如何取得，IoC Service Provider需要将对象的构建逻辑从客户端对象那里剥离出来，以免这部分逻辑污染业务对象的实现。
- **业务对象间的依赖绑定**：这是核心，IoC Service Provider通过结合之前构建和管理的所有业务对象，以及各个业务对象间可以识别的依赖关系，将这些对象所依赖的对象注入绑定，从而保证每个业务对象在使用的时候，可以处于就绪状态。

当前流行的IoC Service Provider产品使用的注册对象管理信息的方式主要有以下几种：直接编码方式、配置文件方式、元数据方式。

### Spring 的IoC容器至BeanFactory

Spring的IoC容器是一个提供IoC支持的轻量级容器，除了基本的IoC支持，它作为轻量级容器还提供了IoC之外的支持。如在Spring的IoC容器之上，Spring还提供了相应的AOP框架支持、企业级服务集成等服务。Spring的IoC容器和IoC Service Provider所提供的服务之间存在一定的交集，二者的关系如下图所示。

![1543828152097](E:\Wiki\mywiki\images\1543828152097.png)

Spring提供了两种容器类型：BeanFactory和ApplicationContext。

- BeanFactory。基础类型IoC容器，提供完整的IoC服务支持。如果没有特殊指定，默认采用延迟初始化策略（lazy-load）。只有当客户端对象需要访问容器中的某个受管对象的时候，才对该受管对象进行初始化以及依赖注入操作。所以，相对来说，容器启动初期速度较快，所需要的资源有限。对于资源有限，并且功能要求不是很严格的场景，BeanFactory是比较合适的IoC容器选择。
- ApplicationContext。ApplicationContext在BeanFactory的基础上构建，是相对比较高级的容器实现，除了拥有BeanFactory的所有支持，ApplicationContext还提供了其他高级特性，比如事件发布、国际化信息支持等。ApplicationContext所管理的对象，**在该类型容器启动之后，默认全部初始化并绑定完成**。所以，相对于BeanFactory来说，ApplicationContext要求更多的系统资源，同时，因为在启动时就完成所有初始化，容器启动时间较之BeanFactory也会长一些。在那些系统资源充足，并且要求更多功能的场景中，ApplicationContext类型的容器是比较合适的选择。

BeanFactory和ApplicationContext的继承关系图：

![1543828021321](E:\Wiki\mywiki\images\1543828021321.png)

作为Spring提供的基本的IoC容器，BeanFactory可以完成作为IoC Service Provider的所有职责，包括**业务对象的注册**和**对象间依赖关系的绑定**。



BeanFactory接口只定义如何访问容
器内管理的Bean的方法，各个BeanFactory的具体实现类负责具体Bean的注册以及管理工作。
BeanDefinitionRegistry接口定义抽象了Bean的注册逻辑。通常情况下，具体的BeanFactory实现
类会实现这个接口来管理Bean的注册。





