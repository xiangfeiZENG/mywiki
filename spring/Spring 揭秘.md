## Spring 揭秘

[TOC]

![1539240874774](E:\Wiki\mywiki\images\1539240874774.png)



### IOC容器

全称为Inversion of Control，中文通常翻译为“控制反转”，它还有一个别名叫做依赖注入（Dependency Injection）。好莱坞原则“Don’t call us, we will call you.”

注意:本章更多的是将IoC和依赖注入看作等同的概念进行讲解。但是，在这一点上可能存在不同的观点，比如Expert Spring MVC and Web Flow和Expert One-on-One J2EE without EJB等书中都将依赖注入看作是IoC的一种方式。

![1539241501866](E:\Wiki\mywiki\images\1539241501866.png)

在IoC的场景中，二者之间通过IoC Service Provider来打交道，所有的被注入对象和依赖对象现在由IoC Service Provider统一管理。被注入对象需要什么，直接跟IoC Service Provider招呼一声，后者就会把相应的被依赖对象注入到被注入对象中，从而达到IoC Service Provider为被注入对象服务的目的。IoC Service Provider在这里就是通常的IoC容器所充当的角色。从被注入对象的角度看，与之前直接寻求依赖对象相比，依赖对象的取得方式发生了反转，控制也从被注入对象转到了IoC Service Provider那里。

其实IoC就这么简单！原来是需要什么东西自己去拿，现在是需要什么东西就让别人送过来。

IoC是一种可以帮助我们解耦各业务对象间依赖关系的对象绑定方式！



