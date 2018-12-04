---
title: Java笔记-Java I/O系统
date: 2018-04-18 21:21:20
categories: java
tags: java
---

## Java笔记-Java I/O系统

Java I/O系统需要处理各种I/O源端和想要与之通信的接收端（文件、控制台、网络链接等），而且还需要以不同的方式进行通信（顺序、随机存取、缓冲、二进制、按字符、按行、按字等）。

### File类

File既能代表一个特定文件的名称，又能代表一个目录下一组文件的名称。

目录列表器,：

```java
public class DirList3 {
  public static void main(final String[] args) {
    File path = new File(".");
    String[] list;
    if(args.length == 0)
      list = path.list();
    else
      list = path.list(new FilenameFilter() {
        private Pattern pattern = Pattern.compile(args[0]);
        public boolean accept(File dir, String name) {
          return pattern.matcher(name).matches();
        }
      });
    Arrays.sort(list, String.CASE_INSENSITIVE_ORDER);
    for(String dirItem : list)
      System.out.println(dirItem);
  }
} /* Output:
DirectoryDemo.java
DirList.java
DirList2.java
DirList3.java
*///:~
```

目录实用工具：

```java
public final class Directory {
  public static File[]
  local(File dir, final String regex) {
    return dir.listFiles(new FilenameFilter() {
      private Pattern pattern = Pattern.compile(regex);
      public boolean accept(File dir, String name) {
        return pattern.matcher(
          new File(name).getName()).matches();
      }
    });
  }
  public static File[]
  local(String path, final String regex) { // Overloaded
    return local(new File(path), regex);
  }
  // A two-tuple for returning a pair of objects:
  public static class TreeInfo implements Iterable<File> {
    public List<File> files = new ArrayList<File>();
    public List<File> dirs = new ArrayList<File>();
    // The default iterable element is the file list:
    public Iterator<File> iterator() {
      return files.iterator();
    }
    void addAll(TreeInfo other) {
      files.addAll(other.files);
      dirs.addAll(other.dirs);
    }
    public String toString() {
      return "dirs: " + PPrint.pformat(dirs) +
        "\n\nfiles: " + PPrint.pformat(files);
    }
  }
  public static TreeInfo
  walk(String start, String regex) { // Begin recursion
    return recurseDirs(new File(start), regex);
  }
  public static TreeInfo
  walk(File start, String regex) { // Overloaded
    return recurseDirs(start, regex);
  }
  public static TreeInfo walk(File start) { // Everything
    return recurseDirs(start, ".*");
  }
  public static TreeInfo walk(String start) {
    return recurseDirs(new File(start), ".*");
  }
  static TreeInfo recurseDirs(File startDir, String regex){
    TreeInfo result = new TreeInfo();
    for(File item : startDir.listFiles()) {
      if(item.isDirectory()) {
        result.dirs.add(item);
        result.addAll(recurseDirs(item, regex));
      } else // Regular file
        if(item.getName().matches(regex))
          result.files.add(item);
    }
    return result;
  }
  // Simple validation test:
  public static void main(String[] args) {
    if(args.length == 0)
      System.out.println(walk("."));
    else
      for(String arg : args)
       System.out.println(walk(arg));
  }
} ///:~
```

File类不仅仅指代表存在的文件或目录，也可以用File对象来创建新的目录或尚不存在的整个目录路径。还可以删除文件。

### 输入和输出

编程语言的I/O类库中常使用流这个抽象概念，它代表任何有能力产出数据的**数据源对象**或者是由能力接受数据的**接收端对象**。“流”屏蔽了实际的I/O设备中处理数据的细节。

Java类库中的I/O类分成输入和输出两个部分，任何自InputStream或Reader派生而来的类都含有read()的基本方法，用于读取单个字节或者字节数组。任何自OutputStream或Writer派生而来的类都含有名为write()的基本方法，用于写单个字节或者字节数组。

整体类图如下：



![1543800921220](C:\Users\zengxf01\AppData\Local\Temp\1543800921220.png)



整体类设计采用装饰者模式：

![1543801118173](C:\Users\zengxf01\AppData\Local\Temp\1543801118173.png)



Reader 和Writer提供兼容Unicode与面向字符的I/O功能。并不是用来替代InputStream和OutputStream，设计Reader和Writer继承层次结构主要是为了国际化。老的I/O流继承层次结构仅支持8位字节流，并且不能很好的处理16位的Unicode字符。

有时候必须把来自“字节”层次结构中的类和“字符”层次结构中的类结合起来使用，为了实现这个目的，要用到“适配器”（adapter）类：InputStreamReader可以把InputStream转换为Reader，而OutputStreamWriter可以把OutputStream转换为Writer。



### I/O流的典型使用方法

#### 缓冲输入文件

注意要关闭输入流：in.close();  必须添加换行符，因为readLine()已经将他们删除。

```java
public class BufferedInputFile {
  // Throw exceptions to console:
  public static String
  read(String filename) throws IOException {
    // Reading input by lines:
    BufferedReader in = new BufferedReader(
      new FileReader(filename));
    String s;
    StringBuilder sb = new StringBuilder();
    while((s = in.readLine())!= null)
      sb.append(s + "\n");
    in.close();
    return sb.toString();
  }
  public static void main(String[] args)
  throws IOException {
    System.out.print(read("BufferedInputFile.java"));
  }
} /* (Execute to see output) *///:~
```

#### 从内存输入

StringReader中read()每次读取一个字符，并发送值控制台，注意read()是以int形式返回下一字节，必须类型转换成char才能正确打印。

```java
public class MemoryInput {
  public static void main(String[] args)
  throws IOException {
    StringReader in = new StringReader(
      BufferedInputFile.read("MemoryInput.java"));
    int c;
    while((c = in.read()) != -1)
      System.out.print((char)c);
  }
} /* (Execute to see output) *///:~
```



#### 格式化内存输入

ByteArrayInputStream：在内存中创建缓冲区，所有送往“流”的数据都要放置在此缓冲区中。关闭ByteArrayInputStream没有任何效果。 在关闭流之后，可以调用此类中的方法，而不生成IOException 。

DataInputStream：可以按照可移植方式从流读取基本数据类型（int，char，long等）



```java
public class FormattedMemoryInput {
  public static void main(String[] args)
  throws IOException {
    try {
      DataInputStream in = new DataInputStream(
        new ByteArrayInputStream(
         BufferedInputFile.read(
          "FormattedMemoryInput.java").getBytes()));
      while(true)
        System.out.print((char)in.readByte());
    } catch(EOFException e) {
      System.err.println("End of stream");
    }
  }
} /* (Execute to see output) *///:~
```



#### 基本的文件输出

通过BufferWriter将FileWriter包装起来用以缓冲输出，用PrintWriter提供格式化机制。

```java
public class BasicFileOutput {
  static String file = "BasicFileOutput.out";
  public static void main(String[] args)
  throws IOException {
    BufferedReader in = new BufferedReader(
      new StringReader(
        BufferedInputFile.read("BasicFileOutput.java")));
    PrintWriter out = new PrintWriter(
      new BufferedWriter(new FileWriter(file)));
    int lineCount = 1;
    String s;
    while((s = in.readLine()) != null )
      out.println(lineCount++ + ": " + s);
    out.close();
    // Show the stored file:
    System.out.println(BufferedInputFile.read(file));
  }
} /* (Execute to see output) *///:~
```



#### 存储和恢复数据

用DataOutputStream写入数据，用DataInputStream恢复数据。两个类都是面向字节的，需要使用InputStream和OutputStream。

如果使用DataOutputStream写入数据，Java保证我们可以使用DataInputStream准确地读取数据——无论读和写数据的平台多么不同。

当我们使用DataOutputStream时，写字符串并且让DataInputStream能够恢复它的唯一可靠地做法是使用UTF-8编码，writeUTF()与readUTF()

```java
public class StoringAndRecoveringData {
  public static void main(String[] args)
  throws IOException {
    DataOutputStream out = new DataOutputStream(
      new BufferedOutputStream(
        new FileOutputStream("Data.txt")));
    out.writeDouble(3.14159);
    out.writeUTF("That was pi");
    out.writeDouble(1.41413);
    out.writeUTF("Square root of 2");
    out.close();
    DataInputStream in = new DataInputStream(
      new BufferedInputStream(
        new FileInputStream("Data.txt")));
    System.out.println(in.readDouble());
    // Only readUTF() will recover the
    // Java-UTF String properly:
    System.out.println(in.readUTF());
    System.out.println(in.readDouble());
    System.out.println(in.readUTF());
  }
} /* Output:
3.14159
That was pi
1.41413
Square root of 2
*///:~

```



#### 读写随机访问文件

使用RandomAccessFile时，需要知道文件排版，才能正确操作，利用seek()可以再文件中到处移动，并修改文件中的某个值。

使用RandomAccessFile，类似于组合使用了DataInputStream和DataOutputStream（因为他实现了相同的接口：DataInput和DataOutput），它有效的与I/O继承层次接口的其他部分实现了分离，因为它不支持装饰，所以不能将其与InputStream和OutputStream自雷的任何部分组合起来。

```java
public class UsingRandomAccessFile {
  static String file = "rtest.dat";
  static void display() throws IOException {
    RandomAccessFile rf = new RandomAccessFile(file, "r");
    for(int i = 0; i < 7; i++)
      System.out.println(
        "Value " + i + ": " + rf.readDouble());
    System.out.println(rf.readUTF());
    rf.close();
  }
  public static void main(String[] args)
  throws IOException {
    RandomAccessFile rf = new RandomAccessFile(file, "rw");
    for(int i = 0; i < 7; i++)
      rf.writeDouble(i*1.414);
    rf.writeUTF("The end of the file");
    rf.close();
    display();
    rf = new RandomAccessFile(file, "rw");
    rf.seek(5*8);
    rf.writeDouble(47.0001);
    rf.close();
    display();
  }
} /* Output:
Value 0: 0.0
Value 1: 1.414
Value 2: 2.828
Value 3: 4.242
Value 4: 5.656
Value 5: 7.069999999999999
Value 6: 8.484
The end of the file
Value 0: 0.0
Value 1: 1.414
Value 2: 2.828
Value 3: 4.242
Value 4: 5.656
Value 5: 47.0001
Value 6: 8.484
The end of the file
*///:~
```



