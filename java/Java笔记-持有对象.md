---
title: Java笔记-接口和内部类
date: 2018-04-06 21:30:18
categories: java
tags: java
---

## Java笔记-持有对象

Java容器类类库的用途是“保存对象”，并将其划分为两个不同概念：

1) Collection:一个独立元素的序列，允许你使用键来查找值。包括list，set。

2) Map:一组成对的“键值对”对象，允许你使用键来查找值。

#### 添加一组元素

 ```java
public class AddingGroups {
  public static void main(String[] args) {
    Collection<Integer> collection =
      new ArrayList<Integer>(Arrays.asList(1, 2, 3, 4, 5));
    Integer[] moreInts = { 6, 7, 8, 9, 10 };
    collection.addAll(Arrays.asList(moreInts));
    // Runs significantly faster, but you can't
    // construct a Collection this way:
    Collections.addAll(collection, 11, 12, 13, 14, 15);
    Collections.addAll(collection, moreInts);
    // Produces a list "backed by" an array:
    List<Integer> list = Arrays.asList(16, 17, 18, 19, 20);
    list.set(1, 99); // OK -- modify an element
    // list.add(21); // Runtime error because the
                     // underlying array cannot be resized.
  }
} ///:~
 ```

 Collections.addAll()方法的效率要比直接通过构造器构造要快很多，首选方式。

Arrays.asList()，静态工厂方法，将数组视为List，底层是数组，不许与调整尺寸或者add(),delete()。 

Map更加复杂，并且除了用另一个Map之外，Java标准类库没有提供任何其他自动初始化他们的方法。

### 迭代器

如果只是向前便利List，并不打算修改List对象本身，foreach语法显得更加简洁。

迭代器通常被称为轻量级对象：创建它的代价小。作用（Java的Iterator只能单向移动）：

1. 使用方法iterator()要求容器返回一个Iterator。Iterator将准备好返回序列的第一个元素。
2. 使用next()或得序列中的下一个元素。
3. 使用hasNext()检查序列中是否还有元素。
4. 使用remove ()将迭代器新近返回的元素删除。

ListIterator是一个更加强大的Iterator的子类型，它只能用于各种list类的访问，可以双向移动。

### LinkedList

执行插入和删除时比ArrayList更高效，随机访问操作方面要更逊色一些。

常用方法：

- getFirst()和element()完全一样，都返回列表的头（第一个元素）而不删除它，如果List为空，则抛出NoSuchElementException。peek()方法与这两个方式只是稍有差异，在列表为空时返回null。removeFirst()与remove完全一样，移除并返回列表的头列表为空时抛出异常，poll()在列表为空时返回null。
- addFirst()插入值头部，add()与addLast()相同，插入到类表尾部，removeLast()移除并返回列表最后一个元素。

#### Stack

“栈”是指“后进先出”（LIFO)容器。通常使用LinkedList实现：

```java
public class Stack<T> {
  private LinkedList<T> storage = new LinkedList<T>();
  public void push(T v) { storage.addFirst(v); }
  public T peek() { return storage.getFirst(); }
  public T pop() { return storage.removeFirst(); }
  public boolean empty() { return storage.isEmpty(); }
  public String toString() { return storage.toString(); }
} ///:~
```

java.util.Stack使用的是继承Vector的方式，上述实现更加合理，接口更干净，不会继承其他的一些不必要的方法。

#### Queue

队列是一个典型的先进先出（FIFO）的容器。队列在并发编程中特别重要。LinkedList提供了方法以支持队列的行为，并且它实现了Queue接口，因此LinkedList可以用作Queue的一种实现，通过将LinkedList向上转型为Queue。offer()将元素插入到队尾。

```java
public class QueueDemo {
  public static void printQ(Queue queue) {
    while(queue.peek() != null)
      System.out.print(queue.remove() + " ");
    System.out.println();
  }
  public static void main(String[] args) {
    Queue<Integer> queue = new LinkedList<Integer>();
    Random rand = new Random(47);
    for(int i = 0; i < 10; i++)
      queue.offer(rand.nextInt(i + 10));
    printQ(queue);
    Queue<Character> qc = new LinkedList<Character>();
    for(char c : "Brontosaurus".toCharArray())
      qc.offer(c);
    printQ(qc);
  }
} /* Output:
8 1 1 1 5 14 3 1 0 1
B r o n t o s a u r u s
*///:~
```

#### PriorityQueue

优先级队列声明下一个弹出元素是最需要的元素（具有最高的优先级）。默认排序时自然顺序（由小到大），但是可以通过提供自己的Comparator来修改这个顺序。

```java
stringPQ = new PriorityQueue<String>( strings.size(), Collections.reverseOrder());
stringPQ.addAll(strings);
QueueDemo.printQ(stringPQ);

  private static class ReverseComparator
        implements Comparator<Comparable<Object>>, Serializable {

        private static final long serialVersionUID = 7207038068494060240L;

        static final ReverseComparator REVERSE_ORDER
            = new ReverseComparator();

        public int compare(Comparable<Object> c1, Comparable<Object> c2) {
            return c2.compareTo(c1);
        }

        private Object readResolve() { return Collections.reverseOrder(); }

        @Override
        public Comparator<Comparable<Object>> reversed() {
            return Comparator.naturalOrder();
        }
    }
```



### Set

Set不保存重复的元素，最长被使用的是测试**归属性**。

HashSet使用了散列函数，使得其有快速地查询速度。

TreeSet将元素存储在红-黑树数据结构中，按照比较结果的升序保存对象。

LinkedHashList因为查询速度的原因也使用了散列，但是看起来他所以用了链表来维护元素的插入顺序。

### Map

将对象映射到其他对象的能力。keySet()方法产生了所有键组成的Set，它在foreach语句中用来迭代遍历该Map。

HashMap，TreeMap，LinkedHashMap。

### Collection和Iterator

Collection是描述所有序列容器的共性根接口，java.util.AbstractCollection类提供了Collection的默认实现。Collection是Iterator类型（实现了Iterator接口）。

### Foreach与迭代器

foreach之所以能够工作，是由于Java SE5引入了新的被称为Iterable的接口，该接口包含一个能够产生Iterator的iterator()方法，并且Iterable接口被foreach用来在序列中移动。

```java
public class IterableClass implements Iterable<String> {
  protected String[] words = ("And that is how " +
    "we know the Earth to be banana-shaped.").split(" ");
  public Iterator<String> iterator() {
    return new Iterator<String>() {
      private int index = 0;
      public boolean hasNext() {
        return index < words.length;
      }
      public String next() { return words[index++]; }
      public void remove() { // Not implemented
        throw new UnsupportedOperationException();
      }
    };
  }	
  public static void main(String[] args) {
    for(String s : new IterableClass())
      System.out.print(s + " ");
  }
} /* Output:
And that is how we know the Earth to be banana-shaped.
*///:~
```

### 总结

简单地容器分类：

![1542599302111](E:\Wiki\mywiki\images\1542599302111.png)







