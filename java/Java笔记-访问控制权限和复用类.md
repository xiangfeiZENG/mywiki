---
title: Java笔记-访问控制权限和复用类
date: 2018-03-10 12:00:21
categories: java
tags: java
---



## Java笔记-访问控制权限和复用类

#### 访问权限控制

访问控制权限的等级，从最大权限到最小权限一次为：public、protected、包访问权限（没有关键词）和private。

包：库单元——包内包含有一组类，他们在单一的名字空间下被组织在一起了。

当编译一个Java源代码文件时，此文件通常被称为编译单元。每个编译单元只能有一个public类，否则编译器就不会接受。

包访问权限：如果某个成员不提供任何访问权限修饰词，该权限下包中所有其他类对那个成员都有访问权限，但对于这个包之外的所有类，每个成员却是private。

protected提供包访问权限，此外，类的所有派生类都具有该成员的访问权限。

#### 复用类

##### 组合语法

只需在新的类中产生现有的类的对象，由于新的类由现有类的对象所组成，这种方法称为组合。

编译器并不是简单地为每一个引用都创建默认对象。若要初始化这些引用，可以再代码中的下列位置进行：

- 在定义对象的地方，这意味着能够在构造器调用之前被初始化。
- 在类的构造器中
- 在正要使用这些对象之前，这种方式成为**惰性初始化**
- 使用实例初始化。



**@Override 注解** 当你覆写某个方法时，可以选择添加这个注解，可以防止你不想重载时意外地进行了重载。



##### 继承语法

extends关键字，所有的对象都隐式地从Java的标准跟类Object进行继承。构建过程是从基类“向外”扩展的。

如果没有默认的基类构造器，或者想调用一个带参数的基类构造器，就必须用关键词super显式地编写调用基类构造器的语句。调用基类构造器必须是你在导出类构造器中要做的第一件事。

```java
class Game {
  Game(int i) {
    print("Game constructor");
  }
}

class BoardGame extends Game {
  BoardGame(int i) {
    super(i);
    print("BoardGame constructor");
  }
}
```

##### 代理

将一个成员对象置于所要构造的类中（就像组合），但与此同时我们在新类中暴露该成员对象的所有方法（就像继承）。代理是继承与组合之间的中庸之道。

```java
public class SpaceShipControls {
  void forward(int velocity) {}
  void back(int velocity) {}
} ///:~


public class SpaceShipDelegation {
  private String name;
  private SpaceShipControls controls =
    new SpaceShipControls();
  public SpaceShipDelegation(String name) {
    this.name = name;
  }
  // Delegated methods:
  public void back(int velocity) {
    controls.back(velocity);
  }
  public void forward(int velocity) {
    controls.forward(velocity);
  }
  public static void main(String[] args) {
    SpaceShipDelegation protector =
      new SpaceShipDelegation("NSEA Protector");
    protector.forward(100);
  }
} ///:~
```



##### final关键字

告知一块数据时恒定不变的：

1. 一个永不改变的编译器常量
2. 一个在运行时被初始化的值，你不希望它被改变

对于对象引用，final使引用恒定不变。

final static 基本类型全用大写字母命名，而且字与字之间用下划线隔开：

`static final String VAL_ONE="ContantVal"`

final 参数

Java允许在参数列表中以声明的方式将参数指明为final。这意味着你无法再方法中更改参数所指向的对象。这一特性主要用来向匿名内部类传递数据。

```java
public class FinalArguments {
  void with(final Gizmo g) {
    //! g = new Gizmo(); // Illegal -- g is final
  }
  void without(Gizmo g) {
    g = new Gizmo(); // OK -- g not final
    g.spin();
  }
  // void f(final int i) { i++; } // Can't change
  // You can only read from a final primitive:
  int g(final int i) { return i + 1; }
}
```

final和private关键字

类中所有的private方法都隐式地指定为final的。

final类

当将某个类的整体定义为final时，表明你不打算继承该类，而且也不允许别人这样做。



