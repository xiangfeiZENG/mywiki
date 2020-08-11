---
title: Java笔记-类型信息
date: 2018-04-10 20:48:21
categories: java
tags: java
---

## Java笔记-类型信息

运行时类型信息是得你可以在程序运行时发现和使用类型信息。在运行时识别对象和类的信息，主要有两种实现方式：一种是传统的 RTTI （在运行时，识别一个对象的类型），它假定编译时已经知道了所有的类型；另一种是”反射“机制，它允许在运行时发现和使用类的信息。

### Class对象

Class对象是用来创建类的所有的“常规”对象的，它包含了与类有关的信息，java的RTTI基于Class对象。

类是程序的一部分，每个类都有一个class对象。换言之，每当编写并且编译了一个新类，就会产生一个class对象（更恰当地说，是被保存在一个同名的.class文件中）。为了生成这个类的对象，运行这个程序的java虚拟机（JVM）将使用被称为“类加载器”的子系统。当程序创建第一个对类的静态成员的引用时，就会加载这个类。一旦某个类的Class对象被载入内存，它就被用来创建这个类的所有对象。

Class.forName("Gum")：获取并操作Gum类的引用。类名.getClass()也可或得class对象的引用。

Class对象常用方法：

- getName():产生全限定类名，包含包名
- getSimpleName()：仅类名
- getInterfaces()：返回的是class对象数组，实现了那些接口
- getSuperclass()：返回直接基类
- newInstance():使用newInstance来创建对象的类，必须带有默认的构造器

#### 类字面常量

Java海提供了另一种方法来生成Class对象的引用，即使用类字面常量。 如 FancyToy.class

建议使用.class 的形式，以保持与普通类的一致性。当使用.class 来创建Class对象的引用时，不会自动的初始化该Class对象，为了实用类而做的准备工作实际包含三个步骤：

1. 加载，由类加载器执行，查找字节码（通常通过classpath所指定的路径查找），并从这些字节码中创建一个Class对象。
2. 链接，在链接阶段将验证类中的字节码，为静态域分配存储空间，并且如果必须的话，将解析这个类创建的对其他类的所有引用。
3. 初始化，如果该类具有超累，则对其初始化，习性静态初始化器和静态初始化块。

```java
class Initable {
  static final int staticFinal = 47;
  static final int staticFinal2 =
    ClassInitialization.rand.nextInt(1000);
  static {
    System.out.println("Initializing Initable");
  }
}

class Initable2 {
  static int staticNonFinal = 147;
  static {
    System.out.println("Initializing Initable2");
  }
}

class Initable3 {
  static int staticNonFinal = 74;
  static {
    System.out.println("Initializing Initable3");
  }
}

public class ClassInitialization {
  public static Random rand = new Random(47);
  public static void main(String[] args) throws Exception {
    Class initable = Initable.class;
    System.out.println("After creating Initable ref");
    // Does not trigger initialization:
    System.out.println(Initable.staticFinal);
    // Does trigger initialization:
    System.out.println(Initable.staticFinal2);
    // Does trigger initialization:
    System.out.println(Initable2.staticNonFinal);
    Class initable3 = Class.forName("Initable3");
    System.out.println("After creating Initable3 ref");
    System.out.println(Initable3.staticNonFinal);
  }
} /* Output:
After creating Initable ref
47
Initializing Initable
258
Initializing Initable2
147
Initializing Initable3
After creating Initable3 ref
74
*///:~
```

注意：static final值是“编译期常量”，不需要初始化就可以被读取。

迄今为止，一直的RTTI形式包括：

1.  传统的类型转换，如“(Shape)",由RTTI确保类型转换的正确性。

2.  代表对象类型的Class对象，通过查询Class对象可以获取运行时所需要的信息
3. 关键字Instanceof

Instanceof判断对象是否某个特定类型的实例。只能将其与命名类型进行比较，而不能与class对象作比较。

```java
boolean isInstance(Object obj) 
确定指定的Object是否与此 Object表示的对象分配 类 。  
```

以上方法消除了需要大量Instanceof语句的问题。

instanceof或isInstance 值得是你是这个类吗？或者你是这个类的派生类吗？  如果用==比较实际的Class对象，就没有考虑继承——他或者是这个确切的类型，或者不是。



扩展：工厂模式

```java
class Part {
  public String toString() {
    return getClass().getSimpleName();
  }
  static List<Factory<? extends Part>> partFactories =
    new ArrayList<Factory<? extends Part>>();	
  static {
    // Collections.addAll() gives an "unchecked generic
    // array creation ... for varargs parameter" warning.
    partFactories.add(new FuelFilter.Factory());
    partFactories.add(new AirFilter.Factory());
    partFactories.add(new CabinAirFilter.Factory());
    partFactories.add(new OilFilter.Factory());
    partFactories.add(new FanBelt.Factory());
    partFactories.add(new PowerSteeringBelt.Factory());
    partFactories.add(new GeneratorBelt.Factory());
  }
  private static Random rand = new Random(47);
  public static Part createRandom() {
    int n = rand.nextInt(partFactories.size());
    return partFactories.get(n).create();
  }
}	

class Filter extends Part {}

class FuelFilter extends Filter {
  // Create a Class Factory for each specific type:
  public static class Factory
  implements typeinfo.factory.Factory<FuelFilter> {
    public FuelFilter create() { return new FuelFilter(); }
  }
}

class AirFilter extends Filter {
  public static class Factory
  implements typeinfo.factory.Factory<AirFilter> {
    public AirFilter create() { return new AirFilter(); }
  }
}	

class CabinAirFilter extends Filter {
  public static class Factory
  implements typeinfo.factory.Factory<CabinAirFilter> {
    public CabinAirFilter create() {
      return new CabinAirFilter();
    }
  }
}

class OilFilter extends Filter {
  public static class Factory
  implements typeinfo.factory.Factory<OilFilter> {
    public OilFilter create() { return new OilFilter(); }
  }
}	

class Belt extends Part {}

class FanBelt extends Belt {
  public static class Factory
  implements typeinfo.factory.Factory<FanBelt> {
    public FanBelt create() { return new FanBelt(); }
  }
}

class GeneratorBelt extends Belt {
  public static class Factory
  implements typeinfo.factory.Factory<GeneratorBelt> {
    public GeneratorBelt create() {
      return new GeneratorBelt();
    }
  }
}	

class PowerSteeringBelt extends Belt {
  public static class Factory
  implements typeinfo.factory.Factory<PowerSteeringBelt> {
    public PowerSteeringBelt create() {
      return new PowerSteeringBelt();
    }
  }
}	

public class RegisteredFactories {
  public static void main(String[] args) {
    for(int i = 0; i < 10; i++)
      System.out.println(Part.createRandom());
  }
} /* Output:
GeneratorBelt
CabinAirFilter
GeneratorBelt
AirFilter
PowerSteeringBelt
CabinAirFilter
FuelFilter
PowerSteeringBelt
PowerSteeringBelt
FuelFilter
*///:~
```



### 反射：运行时的类型信息

Class类与java.lang.reflect类库一起对反射的概念进行了支持，该类库包含了Field、Method以及Constructor类（每个类都实现了Member接口）。这些类型的对象是由JVM在运行时创建的，用以表示未知类里对于的成员。可以使用Constructor创建新的对象用get（）和set（）方法读取和修改Field对象关联的字段，用invoke()方法调用与Method对象关联的方法。

反射机制并没有什么神奇之处，当通过反射与一个未知类型的对象打交道时，JVM只是简单地检查这个对象，看他属于哪个特定的类（就像RTTI那样）。在用它做其他事情前必须先加载哪个类的Class对象。所以RTTI和反射之间真正的区别只在于：对于RTTI来说，编译器在编译时打开和检查.class文件。（换句话说，我们可以用”普通“方式调用对象的所有方法），而对于反射机制来说，.class文件在编译时是不可获取的，所以是在运行时打开并检查.class文件。

类方法提取器：

此处正则表达式是去除包名和类名留下方法名。

```java
public class ShowMethods {
  private static String usage =
    "usage:\n" +
    "ShowMethods qualified.class.name\n" +
    "To show all methods in class or:\n" +
    "ShowMethods qualified.class.name word\n" +
    "To search for methods involving 'word'";
  private static Pattern p = Pattern.compile("\\w+\\.");
  public static void main(String[] args) {
    if(args.length < 1) {
      print(usage);
      System.exit(0);
    }
    int lines = 0;
    try {
      Class<?> c = Class.forName(args[0]);
      Method[] methods = c.getMethods();
      Constructor[] ctors = c.getConstructors();
      if(args.length == 1) {
        for(Method method : methods)
          print(
            p.matcher(method.toString()).replaceAll(""));
        for(Constructor ctor : ctors)
          print(p.matcher(ctor.toString()).replaceAll(""));
        lines = methods.length + ctors.length;
      } else {
        for(Method method : methods)
          if(method.toString().indexOf(args[1]) != -1) {
            print(
              p.matcher(method.toString()).replaceAll(""));
            lines++;
          }
        for(Constructor ctor : ctors)
          if(ctor.toString().indexOf(args[1]) != -1) {
            print(p.matcher(
              ctor.toString()).replaceAll(""));
            lines++;
          }
      }
    } catch(ClassNotFoundException e) {
      print("No such class: " + e);
    }
  }
} /* Output:
public static void main(String[])
public native int hashCode()
public final native Class getClass()
public final void wait(long,int) throws InterruptedException
public final void wait() throws InterruptedException
public final native void wait(long) throws InterruptedException
public boolean equals(Object)
public String toString()
public final native void notify()
public final native void notifyAll()
public ShowMethods()
*///:~
```



### 动态代理

代理是基本的设计模式之一，提供了额外或不同的操作，而插入的用来代替”实际“对象的对象。这些操作通常涉及与“实际”对象的同学，因此代理通常充当着中间人的角色。Java动态代理比代理更进一步，它可以动态地创建代理并动态的处理对所有代理方法的调用。

动态代理实例：

Proxy.newProxyInstance()：

- 第一个参数是一个类加载器（可以从已经被加载的对象中获取器类加载器）
- 第二个参数是你希望实现的接口列表（不是类或抽象类）
- 第三个参数是InvocationHandler接口的一个实现

```java
class DynamicProxyHandler implements InvocationHandler {
  private Object proxied;
  public DynamicProxyHandler(Object proxied) {
    this.proxied = proxied;
  }
  public Object
  invoke(Object proxy, Method method, Object[] args)
  throws Throwable {
    System.out.println("**** proxy: " + proxy.getClass() +
      ", method: " + method + ", args: " + args);
    if(args != null)
      for(Object arg : args)
        System.out.println("  " + arg);
    return method.invoke(proxied, args);
  }
}	

class SimpleDynamicProxy {
  public static void consumer(Interface iface) {
    iface.doSomething();
    iface.somethingElse("bonobo");
  }
  public static void main(String[] args) {
    RealObject real = new RealObject();
    consumer(real);
    // Insert a proxy and call again:
    Interface proxy = (Interface)Proxy.newProxyInstance(
      Interface.class.getClassLoader(),
      new Class[]{ Interface.class },
      new DynamicProxyHandler(real));
    consumer(proxy);
  }
} /* Output: (95% match)	
doSomething
somethingElse bonobo
**** proxy: class $Proxy0, method: public abstract void Interface.doSomething(), args: null
doSomething
**** proxy: class $Proxy0, method: public abstract void Interface.somethingElse(java.lang.String), args: [Ljava.lang.Object;@42e816
  bonobo
somethingElse bonobo
*///:~
```









