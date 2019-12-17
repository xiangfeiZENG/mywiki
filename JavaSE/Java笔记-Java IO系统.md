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



整体类设计采用装饰者模式：

![1543801118173](.\images\1544430724194.png)



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



#### 文件读写的实用工具

注意：

1. read()将每行添加到StringBuffer，并且为每行加上换行符，因为在读的过程中换行符会被去除掉。
2. 需要close()关闭文件。

```java
public class TextFile extends ArrayList<String> {
  // Read a file as a single string:
  public static String read(String fileName) {
    StringBuilder sb = new StringBuilder();
    try {
      BufferedReader in= new BufferedReader(new FileReader(
        new File(fileName).getAbsoluteFile()));
      try {
        String s;
        while((s = in.readLine()) != null) {
          sb.append(s);
          sb.append("\n");
        }
      } finally {
        in.close();
      }
    } catch(IOException e) {
      throw new RuntimeException(e);
    }
    return sb.toString();
  }
  // Write a single file in one method call:
  public static void write(String fileName, String text) {
    try {
      PrintWriter out = new PrintWriter(
        new File(fileName).getAbsoluteFile());
      try {
        out.print(text);
      } finally {
        out.close();
      }
    } catch(IOException e) {
      throw new RuntimeException(e);
    }
  }
  // Read a file, split by any regular expression:
  public TextFile(String fileName, String splitter) {
    super(Arrays.asList(read(fileName).split(splitter)));
    // Regular expression split() often leaves an empty
    // String at the first position:
    if(get(0).equals("")) remove(0);
  }
  // Normally read by lines:
  public TextFile(String fileName) {
    this(fileName, "\n");
  }
  public void write(String fileName) {
    try {
      PrintWriter out = new PrintWriter(
        new File(fileName).getAbsoluteFile());
      try {
        for(String item : this)
          out.println(item);
      } finally {
        out.close();
      }
    } catch(IOException e) {
      throw new RuntimeException(e);
    }
  }
  // Simple test:
  public static void main(String[] args) {
    String file = read("TextFile.java");
    write("test.txt", file);
    TextFile text = new TextFile("test.txt");
    text.write("test2.txt");
    // Break into unique sorted list of words:
    TreeSet<String> words = new TreeSet<String>(
      new TextFile("TextFile.java", "\\W+"));
    // Display the capitalized words:
    System.out.println(words.headSet("a"));
  }
} /* Output:
[0, ArrayList, Arrays, Break, BufferedReader, BufferedWriter, Clean, Display, File, FileReader, FileWriter, IOException, Normally, Output, PrintWriter, Read, Regular, RuntimeException, Simple, Static, String, StringBuilder, System, TextFile, Tools, TreeSet, W, Write]
*///:~
```

##### 读取二进制文件

 bf.available()方法用来产生恰当的数组尺寸，并且read()方法的特定的重载版本填充了这个数组。

```java
public class BinaryFile {
  public static byte[] read(File bFile) throws IOException{
    BufferedInputStream bf = new BufferedInputStream(
      new FileInputStream(bFile));
    try {
      byte[] data = new byte[bf.available()];
      bf.read(data);
      return data;
    } finally {
      bf.close();
    }
  }
  public static byte[]
  read(String bFile) throws IOException {
    return read(new File(bFile).getAbsoluteFile());
  }
} ///:~
```



### 标准I/O

标准输入、标准输出、标准错误，标准I/O的意义在于可以很容易地把程序串联起来，一个程序的标准输出可以成为另一个程序的标准输入。

System.out、System.err事先被包装成了printStream，System.in却是一个没有被包装过的未经过加工的InputStream，这意味着读取System.in之前必须对其进行包装。

#### 从标准输入中读取

```java
public class Echo {
  public static void main(String[] args)
  throws IOException {
    BufferedReader stdin = new BufferedReader(
      new InputStreamReader(System.in));
    String s;
    while((s = stdin.readLine()) != null && s.length()!= 0)
      System.out.println(s);
    // An empty line or Ctrl-Z terminates the program
  }
} ///:~
```



#### 将System.out转换为PrintWriter

```java
public class ChangeSystemOut {
  public static void main(String[] args) {
    PrintWriter out = new PrintWriter(System.out, true);
    out.println("Hello, world");
  }
} /* Output:
Hello, world
*///:~
```

注意第二个参数true，开启自动清空功能，否则看不到输出。



ProcessBuilder实例

```java
public class OSExecute {
  public static void command(String command) {
    boolean err = false;
    try {
      Process process =
        new ProcessBuilder(command.split(" ")).start();
      BufferedReader results = new BufferedReader(
        new InputStreamReader(process.getInputStream()));
      String s;
      while((s = results.readLine())!= null)
        System.out.println(s);
      BufferedReader errors = new BufferedReader(
        new InputStreamReader(process.getErrorStream()));
      // Report errors and return nonzero value
      // to calling process if there are problems:
      while((s = errors.readLine())!= null) {
        System.err.println(s);
        err = true;
      }
    } catch(Exception e) {
      // Compensate for Windows 2000, which throws an
      // exception for the default command line:
      if(!command.startsWith("CMD /C"))
        command("CMD /C " + command);
      else
        throw new RuntimeException(e);
    }
    if(err)
      throw new OSExecuteException("Errors executing " +
        command);
  }
} ///:~
```

```
public class OSExecuteDemo {
  public static void main(String[] args) {
    OSExecute.command("javap OSExecuteDemo");
  }
} /* Output:
Compiled from "OSExecuteDemo.java"
public class OSExecuteDemo extends java.lang.Object{
    public OSExecuteDemo();
    public static void main(java.lang.String[]);
}
*///:~
```



### 新I/O

jdk1.4 的java.nio.*包中引入了新的Java I/O类库，其目的在于提高速度，旧的I/O包已经使用nio重新实现过，一边重复利用这种速度提高，因此我们不显示地使用nio编写代码，也能从中受益。

速度的提高来自于所使用的结构更接近与操作系统执行I/O的方式：通道和缓冲器。唯一直接与通道交互的缓冲器是ByteBuffer。

旧的I/O类库中有三个类被修改了，用以产生FileChannel，这三个被修改的类是FileInputStream、FileOutputStream以及用于即读又写的RandomAccessfile.

nio的目标就是快速移动大量数据，因此ByteBuffer的大小就显得尤为重要。

例子：

```java
public class BufferToText {
  private static final int BSIZE = 1024;
  public static void main(String[] args) throws Exception {
    FileChannel fc =
      new FileOutputStream("data2.txt").getChannel();
    fc.write(ByteBuffer.wrap("Some text".getBytes()));
    fc.close();
    fc = new FileInputStream("data2.txt").getChannel();
    ByteBuffer buff = ByteBuffer.allocate(BSIZE);
    fc.read(buff);
    buff.flip();
    // Doesn't work:
    System.out.println(buff.asCharBuffer());
    // Decode using this system's default Charset:
    buff.rewind();
    String encoding = System.getProperty("file.encoding");
    System.out.println("Decoded using " + encoding + ": "
      + Charset.forName(encoding).decode(buff));
    // Or, we could encode with something that will print:
    fc = new FileOutputStream("data2.txt").getChannel();
    fc.write(ByteBuffer.wrap(
      "Some text".getBytes("UTF-16BE")));
    fc.close();
    // Now try reading again:
    fc = new FileInputStream("data2.txt").getChannel();
    buff.clear();
    fc.read(buff);
    buff.flip();
    System.out.println(buff.asCharBuffer());
    // Use a CharBuffer to write through:
    fc = new FileOutputStream("data2.txt").getChannel();
    buff = ByteBuffer.allocate(24); // More than needed
    buff.asCharBuffer().put("Some text");
    fc.write(buff);
    fc.close();
    // Read and display:
    fc = new FileInputStream("data2.txt").getChannel();
    buff.clear();
    fc.read(buff);
    buff.flip();
    System.out.println(buff.asCharBuffer());
  }
} /* Output:
????
Decoded using Cp1252: Some text
Some text
Some text
*///:~
```

注意：Charset.forName(encoding).decode(buff)

向ByteBuffer插入基本类型的最简单地方法是：利用asCharBuffer()、asShortBuffer()等获得该缓冲器上的视图，然后使用视图的put()方法。视图缓冲器可以让我们通过某个特定的基本类型的视窗查看其底层的ByteBuffer。

ByteBuffer通过一个被”包装“过的8字节数组产生，然后通过各种不同的基本类型的视图缓冲器显示出来。

ByteBuffer是将数据移进移出通道的唯一方式。

![1544094448661](.\images\1544094448661.png)

内存映射文件及其性能讨论：

```java
public class MappedIO {
  private static int numOfInts = 4000000;
  private static int numOfUbuffInts = 200000;
  private abstract static class Tester {
    private String name;
    public Tester(String name) { this.name = name; }
    public void runTest() {
      System.out.print(name + ": ");
      try {
        long start = System.nanoTime();
        test();
        double duration = System.nanoTime() - start;
        System.out.format("%.2f\n", duration/1.0e9);
      } catch(IOException e) {
        throw new RuntimeException(e);
      }
    }
    public abstract void test() throws IOException;
  }
  private static Tester[] tests = {
    new Tester("Stream Write") {
      public void test() throws IOException {
        DataOutputStream dos = new DataOutputStream(
          new BufferedOutputStream(
            new FileOutputStream(new File("temp.tmp"))));
        for(int i = 0; i < numOfInts; i++)
          dos.writeInt(i);
        dos.close();
      }
    },
    new Tester("Mapped Write") {
      public void test() throws IOException {
        FileChannel fc =
          new RandomAccessFile("temp.tmp", "rw")
          .getChannel();
        IntBuffer ib = fc.map(
          FileChannel.MapMode.READ_WRITE, 0, fc.size())
          .asIntBuffer();
        for(int i = 0; i < numOfInts; i++)
          ib.put(i);
        fc.close();
      }
    },
    new Tester("Stream Read") {
      public void test() throws IOException {
        DataInputStream dis = new DataInputStream(
          new BufferedInputStream(
            new FileInputStream("temp.tmp")));
        for(int i = 0; i < numOfInts; i++)
          dis.readInt();
        dis.close();
      }
    },
    new Tester("Mapped Read") {
      public void test() throws IOException {
        FileChannel fc = new FileInputStream(
          new File("temp.tmp")).getChannel();
        IntBuffer ib = fc.map(
          FileChannel.MapMode.READ_ONLY, 0, fc.size())
          .asIntBuffer();
        while(ib.hasRemaining())
          ib.get();
        fc.close();
      }
    },
    new Tester("Stream Read/Write") {
      public void test() throws IOException {
        RandomAccessFile raf = new RandomAccessFile(
          new File("temp.tmp"), "rw");
        raf.writeInt(1);
        for(int i = 0; i < numOfUbuffInts; i++) {
          raf.seek(raf.length() - 4);
          raf.writeInt(raf.readInt());
        }
        raf.close();
      }
    },
    new Tester("Mapped Read/Write") {
      public void test() throws IOException {
        FileChannel fc = new RandomAccessFile(
          new File("temp.tmp"), "rw").getChannel();
        IntBuffer ib = fc.map(
          FileChannel.MapMode.READ_WRITE, 0, fc.size())
          .asIntBuffer();
        ib.put(0);
        for(int i = 1; i < numOfUbuffInts; i++)
          ib.put(ib.get(i - 1));
        fc.close();
      }
    }
  };
  public static void main(String[] args) {
    for(Tester test : tests)
      test.runTest();
  }
} /* Output: (90% match)
Stream Write: 0.56
Mapped Write: 0.12
Stream Read: 0.80
Mapped Read: 0.07
Stream Read/Write: 5.32
Mapped Read/Write: 0.02
*///:~
```



### 文件加锁

文件锁对其他的操作系统进程是可见的，因为java的文件加锁直接映射到了本地操作系统的加锁工具。

```java
public class FileLocking {
  public static void main(String[] args) throws Exception {
    FileOutputStream fos= new FileOutputStream("file.txt");
    FileLock fl = fos.getChannel().tryLock();
    if(fl != null) {
      System.out.println("Locked File");
      TimeUnit.MILLISECONDS.sleep(100);
      fl.release();
      System.out.println("Released Lock");
    }
    fos.close();
  }
} /* Output:
Locked File
Released Lock
*///:~
```

tryLock()是非阻塞的，它设法获取锁，但是如果不能获得，它将直接从方法调用返回，lock()则是阻塞式，它要阻塞进程直至锁可以获得，或利用lock()的线程终端，或调用lock()的通道关闭。 FileLock.release()可以释放锁。







