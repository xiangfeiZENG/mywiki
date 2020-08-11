---
title: Java笔记-多态
date: 2018-03-26 20:00:21
categories: java
tags: java
---



## Java笔记-多态

多态是继数据抽象和继承之后的第三种基本特征。多态通过分离做什么和怎么做，从另一角度将接口和实现分离开来。

方法调用绑定：将一个方法调用同一个方法主体关联起来被称作绑定。若在程序执行前进行绑定，叫做**前期绑定**。在运行时绑定称为 **后期绑定** 。Java中除了static方法和final方法（private方法属于final方法）之外，其他所有的方法都是后期绑定。所以声明某个方法为final，可以防止其他人覆盖该方法，更重要的一点或许是这样做可以有效地“关闭”动态绑定。

缺陷：域与静态方法

注意：只有方法调用可以是多态的，如果你直接访问某个域，这个访问将在编译期进行解析。

#### 构造器内部的多态方法行为

如下实例：

```java
class Glyph {
  void draw() { print("Glyph.draw()"); }
  Glyph() {
    print("Glyph() before draw()");
    draw();
    print("Glyph() after draw()");
  }
}	

class RoundGlyph extends Glyph {
  private int radius = 1;
  RoundGlyph(int r) {
    radius = r;
    print("RoundGlyph.RoundGlyph(), radius = " + radius);
  }
  void draw() {
    print("RoundGlyph.draw(), radius = " + radius);
  }
}	

public class PolyConstructors {
  public static void main(String[] args) {
    new RoundGlyph(5);
  }
} /* Output:
Glyph() before draw()
RoundGlyph.draw(), radius = 0
Glyph() after draw()
RoundGlyph.RoundGlyph(), radius = 5
*///:~
```

初始化的实际过程：

1. 在其他任何事物发生之前，将分配给对象的存储空间初始化为二进制的零。
2. 调用基类构造器，以上步骤会不断反复递归下去。此时，调用被覆盖后的draw()方法（要在调用RoundGlyph狗朝气之前调用），由于步骤1的缘故，此时会发现radius的值为0。
3. 按照声明顺序调用成员的初始化方法。
4. 调用导出类的构造器主体。

用尽可能简单地方法是对象进入正常状态，如果可以的话，避免调用其他方法。



#### 用继承进行设计

更好的方式是选择“组合”，

```
class Actor {
  public void act() {}
}

class HappyActor extends Actor {
  public void act() { print("HappyActor"); }
}

class SadActor extends Actor {
  public void act() { print("SadActor"); }
}

class Stage {
  private Actor actor = new HappyActor();
  public void change() { actor = new SadActor(); }
  public void performPlay() { actor.act(); }
}

public class Transmogrify {
  public static void main(String[] args) {
    Stage stage = new Stage();
    stage.performPlay();
    stage.change();
    stage.performPlay();
  }
} /* Output:
HappyActor
SadActor
*///:~
```

引用在运行时可以与另一个不同的对象重新绑定，在运行期间获得了动态灵活性（这也称作 **状态模式**），与此相反，继承要求在编译期间完全确定下来。通用准则 **用继承表达行为间的差异，并用字段表达状态上的变化**。

如果不运用数据抽象和继承，就不可能理解或者甚至不可能创建多态的例子。多态是一种不能单独来看待的特性，它只能作为类关系“全景”中的一部分，与其他特性协同工作。



