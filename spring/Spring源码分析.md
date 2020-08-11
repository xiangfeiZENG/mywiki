# Spring 源码解析



链接: https://pan.baidu.com/s/1fMgkiGi2tYoSQlx-IPsYfw 提取码: bxjw



## 基础

- 通过自研框架了解Spring的涉及
- 深入Spring源码洞悉核心脉络
- 课后顺藤摸瓜，逐个击破



![image-20200501110303300](D:\00_reference\mywiki\spring\images\image-20200501110303300.png)







Spring框架：

如何让应用程序能以超出当时大众所惯于接受的易用性和稳定性与JEE平台上的不同组件合作。

- 详尽的文档
- 快速方便地集成项目用到的技术



涉及初衷：

- 用于构造JAVA应用程序的轻量级框架

  - 可以采用Spring来构造任何程序，而不局限于Web程序

  - 轻量级：最少的侵入，与应用程序低耦合，接入成本低
  - 最直观的感受：基于POJO，构建出稳定而强大的应用



为各大技术领域提供支持



架构图：

![image-20200501112600035](D:\00_reference\mywiki\spring\images\image-20200501112600035.png)



spring-core   IOC&DI

- 包含框架基本的核心工具类，其他组件都要使用这个包里的类
- 定义并提供资源的访问方式



spring-beans：Spring主要面向Bean编程（BOP）

- Bean的定义
- Bean的解析
- Bean的创建

BeanFactory



spring-context

- 为Spring提供运行时环境，保存对象的状态
- 扩展了BeanFactory

ApplicationContext



BeanFactory和ApplicationContext的区别？



spring-aop：最小化的动态代理实现

- JDK动态代理 基于接口
- Cglib 基于类
- 只能使用运行时织入，仅支持方法级编织，仅支持方法执行切入点



spring-aspectj + spring-instrument：Full AspectJ

![image-20200501114351463](D:\00_reference\mywiki\spring\images\image-20200501114351463.png)





学习方法

- 阅读Spring官方文档--Spring Framework Reference
- 多动手调试
- 掌握设计模式，熟悉Spring框架的标签和注解的作用





架构

分层MVC，controller层应该尽量薄

单一职责原则

引起一个类变化的因素不要多于一个

- 尽可能让一个类负责相对独立的业务
- 保证类之间的耦合度低，降低类的复杂度





门面模式 Facade Pattern

子系统的外部与其内部的通信必须通过统一的对象进行

- 提供一个高层次的接口，使得子系统更易于使用

  ![image-20200502112734471](D:\00_reference\mywiki\spring\images\image-20200502112734471.png)

例子：

![image-20200502113600580](D:\00_reference\mywiki\spring\images\image-20200502113600580.png)





### 泛型

让数据类型变得参数化

- 定义泛型时，对应的数据类型是不确定的
- 泛型方法被调用时，会指定具体类型
- 核心目标：解决容器类型在编译时安全检查的问题

1. 泛型类

2. 泛型属性

3. 泛型方法

   ![image-20200503090552491](D:\00_reference\mywiki\spring\images\image-20200503090552491.png)



- 基本类型不支持泛型。

- 泛型相关信息不会进入到运行阶段



能否再泛型里面使用具备继承关系的类

- 使用通配符？，但是会使得泛型的类型检查失去意义
- 给泛型加入上边界 ？extends E

![image-20200503092008806](D:\00_reference\mywiki\spring\images\image-20200503092008806.png)

- 给泛型加入下边界 ? super E



泛型接口：

```
public interface GenericIFactory<T,N> {
    T nextObject();
    N nextNumber();
}
```

```java
public class RobotFactory implements GenericIFactory<String, Integer> {
    private String[] stringRobot = new String[]{"Hello","Hi"};
    private Integer[] integerRobot = new Integer[]{111,000};
    @Override
    public String nextObject() {
        Random random = new Random();
        return stringRobot[random.nextInt(2)];//[0,2)
    }

    @Override
    public Integer nextNumber() {
        Random random = new Random();
        return integerRobot[random.nextInt(2)];
    }

    public static void main(String[] args) {
        GenericIFactory<String, Integer> factory = new RobotFactory();
        System.out.println(factory.nextObject());
        System.out.println(factory.nextNumber());
    }
}
```



泛型方法既可以用在泛型类里面，也可以用在普通类中。

```
    public static <E> void printArray(E[] inputArray){
        for (E element : inputArray){
            System.out.printf("%s", element);
            System.out.printf(" ");
        }
        System.out.println();
    }
```

```
        Integer[] integers = {1, 2, 3, 4, 5, 6};
        Double[] doubles = {1.1, 1.2, 1.3, 1.4, 1.5};
        Character[] characters = {'A', 'B', 'C'};
        stringExample.printArray(integers);
        stringExample.printArray(doubles);
        stringExample.printArray(characters);
```



泛型字母的含义：

![image-20200503093457127](D:\00_reference\mywiki\spring\images\image-20200503093457127.png)

![image-20200503093517381](D:\00_reference\mywiki\spring\images\image-20200503093517381.png)



### 设计模式

简单工厂模式：

![image-20200503095450061](D:\00_reference\mywiki\spring\images\image-20200503095450061.png)



优缺点:

![image-20200503100025312](D:\00_reference\mywiki\spring\images\image-20200503100025312.png)





```
public class MouseFactory {
    public static Mouse  createMouse(int type){
        switch (type) {
            case 0: return new DellMouse();
            case 1: return new HpMouse();
            case 2: return new LenovoMouse();
            default: return new DellMouse();
        }
    }
```







工厂方法模式：

![image-20200503100540705](D:\00_reference\mywiki\spring\images\image-20200503100540705.png)



优缺点：

优点：

![image-20200503101010552](D:\00_reference\mywiki\spring\images\image-20200503101010552.png)

缺点：

![image-20200503101329471](D:\00_reference\mywiki\spring\images\image-20200503101329471.png)



抽象工厂：

- 抽象工程模式侧重的是统一产品族
- 工厂方法模式更加侧重于同一产品等级

![image-20200503101718959](D:\00_reference\mywiki\spring\images\image-20200503101718959.png)





![image-20200503104141104](D:\00_reference\mywiki\spring\images\image-20200503104141104.png)



可行性调研：当更多类型的Controller被加入到工程时：

- 添加新产品时依旧违背开闭原则，增加系统复杂度





### 反射

反射机制

允许程序再运行时来进行自我检查并且对内部的成员进行操作。

反射主要是指程序可以访问，检测和修改它本身状态或行为的一种能力，并额能根据自身的行为的状态和结果，调整或修改应用所描述的行为的状态和相关的语义。



如何获取类的构造方法并使用

```java
/*
 * 通过Class对象可以获取某个类中的：构造方法；
 *
 * 获取构造方法：
 *         1).批量的方法：
 *             public Constructor[] getConstructors()：所有"公有的"构造方法
              public Constructor[] getDeclaredConstructors()：获取所有的构造方法(包括私有、受保护、默认、公有)
 *         2).获取单个的方法，并调用：
 *             public Constructor getConstructor(Class... parameterTypes):获取单个的"公有的"构造方法：
 *             public Constructor getDeclaredConstructor(Class... parameterTypes):获取"某个构造方法"可以是私有的，或受保护、默认、公有；
 *
 *             调用构造方法：
 *             Constructor-->newInstance(Object... initargs)
 */
public class ConstructorCollector {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        Class clazz = Class.forName("demo.reflect.ReflectTarget");
        //1.获取所有的公有构造方法
        System.out.println("**********************所有公有构造方法*********************************");
        Constructor[] conArray = clazz.getConstructors();
        for(Constructor c : conArray){
            System.out.println(c);
        }
        //2.获取所有构造方法
        System.out.println("************所有的构造方法(包括：私有、受保护、默认、公有)***************");
        conArray = clazz.getDeclaredConstructors();
        for(Constructor c : conArray){
            System.out.println(c);
        }
        //3.获取单个带参数的公有方法
        System.out.println("*****************获取公有、有两个参数的构造方法*******************************");
        Constructor con = clazz.getConstructor(String.class, int.class);
        System.out.println("con = " + con);
        //4.获取单个私有的构造方法
        System.out.println("******************获取私有构造方法*******************************");
        con = clazz.getDeclaredConstructor(int.class);
        System.out.println("private con = " + con);
        System.out.println("******************调用私有构造方法创建实例*******************************");
        //暴力访问（忽略掉访问修饰符）
        con.setAccessible(true);
        ReflectTarget reflectTarget = (ReflectTarget) con.newInstance(1);
    }
}

```



如何获取并操作成员变量

```java

/*
 * 获取成员变量并调用：
 *
 * 1.批量的
 *      1).Field[] getFields():获取所有的"公有字段"
 *      2).Field[] getDeclaredFields():获取所有字段，包括：私有、受保护、默认、公有；
 * 2.获取单个的：
 *      1).public Field getField(String fieldName):获取某个"公有的"字段；
 *      2).public Field getDeclaredField(String fieldName):获取某个字段(可以是私有的)
 *
 *   设置字段的值：
 *      Field --> public void set(Object obj,Object value):
 *                  参数说明：
 *                  1.obj:要设置的字段所在的对象；
 *                  2.value:要为字段设置的值；
 *
 */

public class FieldCollector {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        //获取Class对象
        Class reflectTargetClass = Class.forName("demo.reflect.ReflectTarget");
        //1.获取所有公有的字段
        System.out.println("************获取所有公有的字段********************");
        Field[] fieldArray = reflectTargetClass.getFields();
        for (Field f : fieldArray){
            System.out.println(f);
        }
        //2.获取所有的字段
        System.out.println("************获取所有的字段(包括私有、受保护、默认的)********************");
        fieldArray = reflectTargetClass.getDeclaredFields();
        for (Field f : fieldArray){
            System.out.println(f);
        }
        //3.获取单个特定公有的field
        System.out.println("*************获取公有字段并调用***********************************");
        Field f = reflectTargetClass.getField("name");
        System.out.println("公有的field name : " + f);
        ReflectTarget reflectTarget = (ReflectTarget)reflectTargetClass.getConstructor().newInstance();
        //4.给获取到的field赋值
        f.set(reflectTarget, "待反射一号");
        //5.验证对应的值name
        System.out.println("验证name : " + reflectTarget.name);
        //6.获取单个私有的Field
        System.out.println("**************获取私有字段targetInfo并调用********************************");
        f = reflectTargetClass.getDeclaredField("targetInfo");
        System.out.println(f);
        f.setAccessible(true);
        f.set(reflectTarget, "13810592345");
        System.out.println("验证信息" + reflectTarget);
    }
}
```

![image-20200503162838139](D:\00_reference\mywiki\spring\images\image-20200503162838139.png)



反射之获取并操作成员方法：



```java
/*
 * 获取成员方法并调用：
 *
 * 1.批量的：
 *      public Method[] getMethods():获取所有"公有方法"；（包含了父类的方法也包含Object类）
 *      public Method[] getDeclaredMethods():获取所有的成员方法，包括私有的(不包括继承的)
 * 2.获取单个的：
 *      public Method getMethod(String name,Class<?>... parameterTypes):
 *                  参数：
 *                      name : 方法名；
 *                      Class ... : 形参的Class类型对象
 *      public Method getDeclaredMethod(String name,Class<?>... parameterTypes)
 *
 *   调用方法：
 *      Method --> public Object invoke(Object obj,Object... args):
 *                  参数说明：
 *                  obj : 要调用方法的对象；
 *                  args:调用方式时所传递的实参；
):
 */
public class MethodCollector {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        //1、获取Class对象
        Class reflectTargetClass = Class.forName("demo.reflect.ReflectTarget");
        //2、获取所有公有方法
        System.out.println("***************获取所有的public方法，包括父类和Object*******************");
        Method[] methodArray = reflectTargetClass.getMethods();
        for(Method m : methodArray){
            System.out.println(m);
        }
        //3、获取该类的所有方法
        System.out.println("***************获取所有的方法，包括私有的*******************");
        methodArray = reflectTargetClass.getDeclaredMethods();
        for(Method m : methodArray){
            System.out.println(m);
        }
        //4、获取单个公有方法
        System.out.println("***************获取公有的show1()方法*******************");
        Method m = reflectTargetClass.getMethod("show1", String.class);
        System.out.println(m);
        //5、调用show1并执行
        ReflectTarget reflectTarget = (ReflectTarget)reflectTargetClass.getConstructor().newInstance();
        m.invoke(reflectTarget, "待反射方法一号");
        //6、获取一个私有的成员方法
        System.out.println("***************获取私有的show4()方法******************");
        m = reflectTargetClass.getDeclaredMethod("show4", int.class);
        System.out.println(m);
        m.setAccessible(true);
        String result = String.valueOf(m.invoke(reflectTarget, 20));
        System.out.println("返回值 ： " + result);
    }
}

```





### 注解

反射的获取源

- 用XML来保存类相关的信息以供反射调用

- 用注解来保存类相关信息以供反射调用



注解：提供一种为程序元素设置元数据的方法

- 元数据是添加到程序元素如方法、字段、类和包上的额外信息
- 注解是一种分散式的元数据设置方式，XML是集中式的设置方式
- 注解不能直接干扰程序代码的运行



```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)

public @interface TestAnnotation {
}


javac demo/annotation/TestAnnotation.java

javap -verbose demo/annotation/TestAnnotation.java
```

![image-20200504103401903](D:\00_reference\mywiki\spring\images\image-20200504103401903.png)



![image-20200504103652622](D:\00_reference\mywiki\spring\images\image-20200504103652622.png)



![image-20200504103903325](D:\00_reference\mywiki\spring\images\image-20200504103903325.png)



![image-20200504104012484](D:\00_reference\mywiki\spring\images\image-20200504104012484.png)





![image-20200504104111924](D:\00_reference\mywiki\spring\images\image-20200504104111924.png)

```
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,

    /** Field declaration (includes enum constants) */
    FIELD,

    /** Method declaration */
    METHOD,

    /** Formal parameter declaration */
    PARAMETER,

    /** Constructor declaration */
    CONSTRUCTOR,

    /** Local variable declaration */
    LOCAL_VARIABLE,

    /** Annotation type declaration */
    ANNOTATION_TYPE,

    /** Package declaration */
    PACKAGE
    ...
```

```
public enum RetentionPolicy {
    /**
     * Annotations are to be discarded by the compiler.
     */
    SOURCE,

    /**
     * Annotations are to be recorded in the class file by the compiler
     * but need not be retained by the VM at run time.  This is the default
     * behavior.
     */
    CLASS,

    /**
     * Annotations are to be recorded in the class file by the compiler and
     * retained by the VM at run time, so they may be read reflectively.
     *
     * @see java.lang.reflect.AnnotatedElement
     */
    RUNTIME
}
```



其他元注解：

![image-20200504105359444](D:\00_reference\mywiki\spring\images\image-20200504105359444.png)



![image-20200504105706338](D:\00_reference\mywiki\spring\images\image-20200504105706338.png)

![image-20200504105805124](D:\00_reference\mywiki\spring\images\image-20200504105805124.png)





```java
public class AnnotationParser {
    //解析类的注解
    public static  void  parseTypeAnnotation() throws ClassNotFoundException {
        Class clazz = Class.forName("demo.annotation.ImoocCourse");
        //这里获取的是class对象的注解，而不是其里面的方法和成员变量的注解
        Annotation[] annotations = clazz.getAnnotations();
        for(Annotation annotation : annotations){
            CourseInfoAnnotation courseInfoAnnotation =  (CourseInfoAnnotation) annotation;
            System.out.println("课程名:" + courseInfoAnnotation.courseName() + "\n" +
                    "课程标签：" + courseInfoAnnotation.courseTag() + "\n" +
                    "课程简介：" + courseInfoAnnotation.courseProfile() + "\n" +
                    "课程序号：" + courseInfoAnnotation.courseIndex() );
        }
    }
    //解析成员变量上的标签
    public static void parseFieldAnnotation() throws ClassNotFoundException {
        Class clazz = Class.forName("demo.annotation.ImoocCourse");
        Field[] fields = clazz.getDeclaredFields();
        for(Field f : fields){
            //判断成员变量中是否有指定注解类型的注解
            boolean hasAnnotation = f.isAnnotationPresent(PersonInfoAnnotation.class);
            if(hasAnnotation){
                PersonInfoAnnotation personInfoAnnotation = f.getAnnotation(PersonInfoAnnotation.class);
                System.out.println("名字：" + personInfoAnnotation.name() + "\n" +
                        "年龄：" + personInfoAnnotation.age() + "\n" +
                        "性别：" + personInfoAnnotation.gender() + "\n");
                for(String language : personInfoAnnotation.language()){
                    System.out.println("开发语言：" + language);
                }

            }
        }
    }
    //解析方法注解
    public static void parseMethodAnnotation() throws ClassNotFoundException{
        Class clazz = Class.forName("demo.annotation.ImoocCourse");
        Method[] methods = clazz.getDeclaredMethods();
        for (Method method : methods) {
            /*
             * 判断方法中是否有指定注解类型的注解
             */
            boolean hasAnnotation = method.isAnnotationPresent(CourseInfoAnnotation.class);
            if(hasAnnotation){
                CourseInfoAnnotation courseInfoAnnotation = method.getAnnotation(CourseInfoAnnotation.class);
                System.out.println("课程名：" + courseInfoAnnotation.courseName() + "\n" +
                        "课程标签：" + courseInfoAnnotation.courseTag() + "\n" +
                        "课程简介：" + courseInfoAnnotation.courseProfile() + "\n"+
                        "课程序号：" + courseInfoAnnotation .courseIndex() + "\n");
            }
        }
    }


    public static void main(String[] args) throws ClassNotFoundException {
        //parseTypeAnnotation();
        parseFieldAnnotation();
        //parseMethodAnnotation();
    }
}

```



注解底层实现：



![image-20200504113439110](D:\00_reference\mywiki\spring\images\image-20200504113439110.png)



注解的工作原理：

```
// 生产动态代理类
-Djdk.proxy.ProxyGenerator.saveGeneratedFiles=true

// 传递参数，打印类加载过程
-XX:TraceClassLoading
```



AnnotationInvocationHandler



- 通过键值对的形式为注解属性赋值

  ![image-20200602191200803](D:\00_reference\mywiki\spring\images\image-20200602191200803.png)

- 编译器检查注解的使用范围，将注解信息写入元素属性表

- 运行时JVM将RUNTIME的所有注解属性取出最终存入map里

- 创建AnnotationInvocationHandler实例并传入前面的map

- JVM使用JDK动态代理为注解生成代理类，并初始化处理器

- 调用invoke方法，通过传入方法名返回注解对应的属性值





Spring 如何解决这个问题：

- 结合了工厂模式和反射机制的Spring IOC容器值得借鉴
- 



控制反转IoC-Inversion of Control（Spring Core最核心的部分）

- 依托一个类似工厂的Ioc容器
- 将对象的创建、依赖关系的管理以及生命周期交由Ioc容器管理
- 降低系统在实现上的复杂性和耦合度，易于扩展，满足开闭原则



需要先了解依赖注入（Denpendency Injection）：