---

title: Java笔记-类型信息
date: 2018-04-12 21:28:11
categories: java
tags: java
---

## Java笔记-泛型

泛型实现了参数化类型的概念。核心概念：告诉编译器想使用什么类型，然后编译器帮你处理一切细节。

### 简单泛型

一个堆栈类例子：

此例子使用了一个末端哨兵，这个末端哨兵是在够着LinkedStack时创建的。

```java
public class LinkedStack<T> {
  private static class Node<U> {
    U item;
    Node<U> next;
    Node() { item = null; next = null; }
    Node(U item, Node<U> next) {
      this.item = item;
      this.next = next;
    }
    boolean end() { return item == null && next == null; }
  }
  private Node<T> top = new Node<T>(); // End sentinel
  public void push(T item) {
    top = new Node<T>(item, top);
  }	
  public T pop() {
    T result = top.item;
    if(!top.end())
      top = top.next;
    return result;
  }
  public static void main(String[] args) {
    LinkedStack<String> lss = new LinkedStack<String>();
    for(String s : "Phasers on stun!".split(" "))
      lss.push(s);
    String s;
    while((s = lss.pop()) != null)
      System.out.println(s);
  }
} /* Output:
stun!
on
Phasers
*///:~
```

一个RandomList例子：

```java
public class RandomList<T> {
  private ArrayList<T> storage = new ArrayList<T>();
  private Random rand = new Random(47);
  public void add(T item) { storage.add(item); }
  public T select() {
    return storage.get(rand.nextInt(storage.size()));
  }
  public static void main(String[] args) {
    RandomList<String> rs = new RandomList<String>();
    for(String s: ("The quick brown fox jumped over " +
        "the lazy brown dog").split(" "))
      rs.add(s);
    for(int i = 0; i < 11; i++)
      System.out.print(rs.select() + " ");
  }
} /* Output:
brown over fox quick quick dog brown The brown lazy brown
*///:~
```



### 泛型接口

泛型也可以用于接口，如生成器（generator）。

```java
public interface Generator<T> { T next(); } ///:~

public class Fibonacci implements Generator<Integer> {
  private int count = 0;
  public Integer next() { return fib(count++); }
  private int fib(int n) {
    if(n < 2) return 1;
    return fib(n-2) + fib(n-1);
  }
  public static void main(String[] args) {
    Fibonacci gen = new Fibonacci();
    for(int i = 0; i < 18; i++)
      System.out.print(gen.next() + " ");
  }
} /* Output:
1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 2584
*///:~

```

注意：泛型有个局限性：基本类型无法作为类型参数。

### 泛型方法

泛型方法使得该方法能够独立于类而产生变化。原则：无论何时，只要你能做到，你就应该尽量使用泛型方法。如果可以使用泛型方法可以去到整个类的泛型话，那就应该只使用泛型方法。

定义泛型方法，只需要将泛型参数列表置于返回值之前。

```java
public class GenericMethods {
  public <T> void f(T x) {
    System.out.println(x.getClass().getName());
  }
  public static void main(String[] args) {
    GenericMethods gm = new GenericMethods();
    gm.f("");
    gm.f(1);
    gm.f(1.0);
    gm.f(1.0F);
    gm.f('c');
    gm.f(gm);
  }
} /* Output:
java.lang.String
java.lang.Integer
java.lang.Double
java.lang.Float
java.lang.Character
GenericMethods
*///:~
```

当使用泛型类时，必须在创建对象的时候指定类型参数的值，而使用泛型方法的时候，通常不必要指明参数类型，因为编译器会为我们找出具体的类型，这称为**类型参数推断（type argument inference）**。

另一个例子：

1. 类型推断只对复制操作有效，其他时候不起作用。`// f(New.map()); // Does not compile`
2. 可以通过显示的类型说明解决此问题。必须在点操作符合方法名之间插入尖括号，然后把类型置于尖括号内。

```java
public class New {
  public static <K,V> Map<K,V> map() {
    return new HashMap<K,V>();
  }
  public static <T> List<T> list() {
    return new ArrayList<T>();
  }
  public static <T> LinkedList<T> lList() {
    return new LinkedList<T>();
  }
  public static <T> Set<T> set() {
    return new HashSet<T>();
  }	
  public static <T> Queue<T> queue() {
    return new LinkedList<T>();
  }
  // Examples:
  public static void main(String[] args) {
    Map<String, List<String>> sls = New.map();
    List<String> ls = New.list();
    LinkedList<String> lls = New.lList();
    Set<String> ss = New.set();
    Queue<String> qs = New.queue();
  }
} ///:~
```

```java
public class LimitsOfInference {
  static void
  f(Map<Person, List<? extends Pet>> petPeople) {}
  public static void main(String[] args) {
    // f(New.map()); // Does not compile
  }
} ///:~

public class ExplicitTypeSpecification {
  static void f(Map<Person, List<Pet>> petPeople) {}
  public static void main(String[] args) {
    f(New.<Person, List<Pet>>map());
  }
} ///:~
```



一个通用的Generator

```java
public class BasicGenerator<T> implements Generator<T> {
  private Class<T> type;
  public BasicGenerator(Class<T> type){ this.type = type; }
  public T next() {
    try {
      // Assumes type is a public class:
      return type.newInstance();
    } catch(Exception e) {
      throw new RuntimeException(e);
    }
  }
  // Produce a Default generator given a type token:
  public static <T> Generator<T> create(Class<T> type) {
    return new BasicGenerator<T>(type);
  }
} ///:~
```

测试：

```java
public class CountedObject {
  private static long counter = 0;
  private final long id = counter++;
  public long id() { return id; }
  public String toString() { return "CountedObject " + id;}
} ///:~

public class BasicGeneratorDemo {
  public static void main(String[] args) {
    Generator<CountedObject> gen =
      BasicGenerator.create(CountedObject.class);
    for(int i = 0; i < 5; i++)
      System.out.println(gen.next());
  }
} /* Output:
CountedObject 0
CountedObject 1
CountedObject 2
CountedObject 3
CountedObject 4
*///:~
```

使用通用Generator的类的条件：1. 必须是public  2. 必须具备默认构造器（newInstance要求）



### 擦除的神秘之处

在泛型代码内部，无法获得任何有关泛型参数类型的信息。

```java
public class ErasedTypeEquivalence {
  public static void main(String[] args) {
    Class c1 = new ArrayList<String>().getClass();
    Class c2 = new ArrayList<Integer>().getClass();
    System.out.println(c1 == c2);
  }
} /* Output:
true
*///:~

public class LostInformation {
  public static void main(String[] args) {
    List<Frob> list = new ArrayList<Frob>();
    Map<Frob,Fnorkle> map = new HashMap<Frob,Fnorkle>();
    Quark<Fnorkle> quark = new Quark<Fnorkle>();
    Particle<Long,Double> p = new Particle<Long,Double>();
    System.out.println(Arrays.toString(
      list.getClass().getTypeParameters()));
    System.out.println(Arrays.toString(
      map.getClass().getTypeParameters()));
    System.out.println(Arrays.toString(
      quark.getClass().getTypeParameters()));
    System.out.println(Arrays.toString(
      p.getClass().getTypeParameters()));
  }
} /* Output:
[E]
[K, V]
[Q]
[POSITION, MOMENTUM]
*///:~
```

Java泛型是使用**擦除**来实现的，这意味着当你使用泛型时，任何具体的类型信息都被擦除了，你唯一知道的就是你在使用一个对象。因此List<String>和List<Integer>在运行时事实上是相同的类型，这两种形式都被擦除成他们的“原生”类型，即List。

边界<T extends HasF>声明T必须具有类型HasF或者从HasF导出的类型。泛型类型参数将擦除到它的第一个边界。

擦除的核心动机是它是得泛化的客户端可以使用非泛化的类库来使用，反之亦然，这经常被称为“迁移兼容性”。





