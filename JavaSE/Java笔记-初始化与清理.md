---
title: Java笔记-初始化与清理
date: 2018-03-06 19:42:41
categories: java
tags: java
---



## Java笔记-初始化与清理

“不安全”的编程方式主键成为编程代价高昂的主因之一，初始化和清理正是设计安全的两个问题。Java通过构造器保证初始化。

### 用构造器确保初始化

默认构造器：即无参构造器，若编写类中没有构造器，编译器会自动创建一个默认构造器，若已编写构造器，编译器将不会自动创建默认构造器。

### 方法重载

方法名相同，而形式参数不同，不能使用返回值区分重载方法。

### this关键字

为了让方法了解具体是那个对象调用方法，编译器暗自把“所操作对象的引用”作为第一个参数传递给方法。使用this关键字表示被传递的引用，**this关键字只能在方法内部使用**，表示对“调用方法的那个对象”的引用。

```java
eg;
Banana a,b;
...
a.peel(1);
b.peel(2);
编译器处理后，实际的调用变成如下：
Banana.peel(a,1);
Banana.peel(b,1);
```

可以通过在return中返回this，达到在一条语句里面对同一个对象执行多次操作。

```java
a.increment().increment();
```

在构造器中调用构造器，使用this，编译器禁止在构造器外的任何地方调用构造器。

```java
public class Flower {
  int petalCount = 0;
  String s = "initial value";
  Flower(int petals) {
    petalCount = petals;
  }
  Flower(String ss) {
    s = ss;
  }
  Flower(String s, int petals) {
    this(petals);
//!    this(s); // Can't call two!
    this.s = s; // Another use of "this"
  }
  void printPetalCount() {
    //! this(11); // Not inside non-constructor!
  }
}
```

#### static含义

static方法就是没有this的方法。在static方法的内部不能调用非静态方法。

实际保存在class对象中？

### 清理：终结处理和垃圾回收

垃圾回收器只知道释放哪些经由new分配的内存，java允许类中定义一个名为finalize()的方法，工作原理如下：一旦垃圾回收器准备好释放对象占用的存储空间，将首先调用器finalize()方法（用于释放特殊内存区域，如c或c++创建的内存区域），并且在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。但是，无论是”垃圾回收“还是”终结”，都不保证一定会发生。如果Java虚拟机（JVM）并未面临内存耗尽的情形，他是不会浪费时间去执行垃圾回收以恢复内存的。

Java中的对象垃圾回收牢记以下三点：

1. 对象可以不被垃圾回收（参照第3点，可能还未回收或者非new创建的对象）。
2. 垃圾回收并不等于“析构”。
3. 垃圾回收只与内存有关。

#### 垃圾回收工作原理

其他语言，在堆上分配对象的代价十分高昂，然而垃圾回收器对于提高对象的创建速度，却有明显的效果。（存储空间的释放会影响存储空间的分配），这是Java虚拟机的工作方式。

判断对象是否存活：

- 引用记数；简单但是速度慢，无法解决对象之间相互循环引用问题，从未实际被Java虚拟机使用。
- 可达性分析：以“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链（Reference Chain），如果对象不可达，则称为可回收对象。Java中GC Roots 包括：
  - 虚拟机栈（栈帧中的本地变量表）中引用的对象
  - 方法区中类静态属性引用的对象
  - 方法区中常量引用的对象
  - 本地方法栈中JNI（即一般说的Native方法）引用的对象。

“自适应的，分代的，停止-复制，标记-清扫”式垃圾回收器。

### 成员初始化相关

初始化总结如下：

1. 即使没有显示地使用static关键字，构造器实际上也是静态方法。因此，当首次创建类型为Dog的对象时（构造器可以看成静态方法），或者Dog类的静态方法/静态域首次被访问时，Java解释器必须查找类路径，以定位Dog.class文件。
2. 然后载入Dog.class(创建一个Class对象)，有关静态初始化的所有动作都会执行。因此，静态初始化只在Class对象首次加载的时候进行一次。
3. 当用new Dog()创建对象的时候，首先将在堆上为Dog对象分配足够的存储空间。
4. 这块存储空间会被清零，这就自动的将Dog对象中的所有基本类型数据都设置成了默认值（对数字来说是0,），而引用则被设置成了null。
5. 执行所有出现于字段定义处的初始化动作。
6. 执行构造器。

`int[] a;`，编译器不允许指定数组的大小，这只是对数组的一个引用，数组创建相应的存储空间，必须写初始化表达式。

### 可变参数列表

eg：pringArray(Object ... args){...}，本质上仍然是数组。

```java
public class VarArgs {
  static void printArray1(Object[] args) {
    for(Object obj : args)
      System.out.print(obj + " ");
    System.out.println();
  }
   static void printArray2(Object... args) {
    for(Object obj : args)
      System.out.print(obj + " ");
    System.out.println();
  }
    
  public static void main(String[] args) {
    printArray1(new Object[]{"one", "two", "three" });
    printArray2("one","two","three");
}
```

可变参数列表进行自动包装，让传参更加直观简单。

### 枚举类型

enum用来表示常量集，拥有顺序和方法，本质上是类，能够在switch语句中使用。可以将enum用作另外一种创建数据类型的方式。

```java
public enum Spiciness {
  NOT, MILD, MEDIUM, HOT, FLAMING
} ///:~

public class EnumOrder {
  public static void main(String[] args) {
    Spiciness howHot = Spiciness.MEDIUM;
    System.out.println(howHot);  
    //ordinal()显示顺序 
    for(Spiciness s : Spiciness.values())
      System.out.println(s + ", ordinal " + s.ordinal());
    }
    
    switch(howHot) {
      case NOT:    System.out.println("not spicy at all.");
                   break;
      case MILD:
      case MEDIUM: System.out.println("a little hot.");
                   break;
      case HOT:
      case FLAMING:
      default:     System.out.println("maybe too hot.");
    }
}

```





