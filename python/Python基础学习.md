# Python基础学习

[TOC]

python在开发效率上有一定的优势。



## 入门导学

国内：豆瓣、知乎以python语言为主导。

python像一个技术的润滑剂。

#### python的特点

特点1

Life is Simple，I Use Python。简洁

pythonic：很*Python

特点2

非常易于学习

特点3

跨平台，可以运行在windows、linux以及macos

特点4

极为强大而丰富的标准库和第三方库，比如电子邮件，比如图形GUI界面

特点5

Python是面向对象的语言



### Python 之禅

Simple is better than complex.

Now is better than never. Although never is offen better than 'right' now.

做也许好过不做，但不假思索就动手还不如不做。



如果想用python解决生活中的问题，python无疑是最好的选择。



### Python的缺点

- 慢：相较于C、C++、Java，运行效率较慢

  编译型语言（c、c++）、解释性语言（Javascript、Python）

  Java和C#属于什么类型的？编译型，只是会编译成中间代码。

  运行效率与开发效率，鱼和熊掌不可兼得

- 42min



一个学习编程的经典误区

世界不是只有Web，还有很多问题需要使用编程来解决。不要把思维局限在Web上，这只是编程的一个应用方向。



### Python能做什么

几乎是万能的。万金油

- 爬虫：搜索引擎，今日头条
- 大数据与数据分析（Spark）
- 自动化运维与自动化测试
- Web开发：Flask、Django
- 机器学习：Tensor Flow
- 胶水语言：混合其他如C++、Java等来编程。能够把用其他语言制作的各种模块（尤其是C/C++）很轻松的联结在一起

当你遇到问题时，随手拿起python，编写一个工具，这才是python的正确打开方式。



回归语言的本质，享受语言本身的存粹之美。

**数据结构**就是基础。





## Python 的基本类型

### 数字(Number)：

- 包括int  float   
- // 除法得到整型
- 0b10 ：二进制。0o10 ：八进制  0x1F 十六进制
- bin() : 把数字转换为二进制； int()：把数字转换为十进制； hex()：把数字转换为十六进制；oct()：把数字转换为八进制。

- 布尔类型（bool）： 表示真、假 int(True) = 1 , int(False) = 0 , bool(1) = True , bool(0) = False；空值都会被认为是False；None也是False。

- 复数，36j

### 字符串（str）

单引号，双引号，三引号

```
字符串内换行的两种方式：
'''
helloworld
helloworld
'''

'hello\
world'


```



转义字符：特殊的字符，无法"看见"的字符。与语言本身语法有冲突的字符。

```
\n 换行
\r 回车
\' 单引号
\t 横向制表符
```



在一个字符串前面加上一个小写的r，这个字符串不再是一个普通的字符串，而是一个原始的字符串。

```
>>> print('hello \\n world')
hello \n world
>>> print(r"echo \n")
echo \n
```



字符串的基本操作方法：

- 字符串合并

  - 加号 +。字符串乘法，重复多次。

- 字符串拆分：

- 获取单个的字符："hello world"[0] = 'h' ;  "hello world"[-1] = 'd'

- 获取一段字符："hello world"[0:4] = 'hell',这个第二位一定要到你截取字符串位置的下一位。"hello world"[0:-1] , 这里第二个字符不是位置，而是指步长。

  ```
  不填值表示到末尾
  >>> "hello world"[6:]
  'world'
  >>> "hello world"[:-5]
  'hello '
  第二位数字表示步幅
  >>> "hello world"[0:-1]
  'hello worl'
  >>> "hello world"[0:4]
  'hell'
  
  第一位表示位置，取值的时候包含该位置
  >>> "hello world"[-4:]
  'orld'
  ```






### 组

#### 列表(list)

```python
[1,2,3,4]
# 列表里面可以嵌套列表
[[1,2],[3,4],[True,False]]

# 注意有冒号返回的是列表
>>> [1,2,3,4][0]
1
>>> [1,2,3,4][-1:]
[4]
>>> [1,2,3,4] + [5]
[1, 2, 3, 4, 5]

```



#### 元组(tuple)

无法改变的列表

为什么有了列表还要元组？



```
# 特殊：这个小括号既可以表示元组，也可以表示数学运算，且数学运算级别高。
>>> type((1))
<class 'int'>

# 定义一个元素的元组
（1,)

# 定义一个元素都没有的元组
()
```

27min









