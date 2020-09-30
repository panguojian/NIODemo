# 二、NIO快速入门
**首先我们来看看IO和NIO的区别：**
|IO|NIO|
|:--:|:---:|
|面向流(Stream Oriented)|面向缓冲区(Buffer Oriented)|
|堵塞IO(Blocking IO)|非堵塞IO(Non Blocking IO)|
|(无)|选择器(Selector)|  

+ 可简单认为：IO是面向流的处理;
+ NIO是面向块(缓冲区)的处理;
+ 面向流的I/O 系统一次一个字节地处理数据;
+ 一个面向块(缓冲区)的I/O系统以块的形式处理数据。

**NIO主要有三个核心部分组成：**
+ buffer缓冲区
+ Channel管道
+ Selector选择器
## 2.1 buffer缓冲区和Channel通道BB
在NIO中并不是以流的方式来处理数据的，而是以Buffer缓冲区和Channel通道配合使用来处理数据。  
简单理解一下：Channel管道比作成铁路，Buffer缓冲区比作成火车(运载着货物)，而我们的NIO就是通过Channel通道运输存储数据的Buffer缓冲区的来实现数据的处理！  
**要时刻记住：Channel不与数据打交道，它只负责运输数据。**  
+ 相对于传统IO而言，流是单向的。
+ 对于NIO而言，有了Channel通道这个概念，我们的读写都是双向的(铁路上的火车能从广州去北京、自然就能从北京返还到广州)！

### 2.1.1 buffer缓冲区核心要点
我们来看看Buffer缓冲区有什么值得我们注意的地方。

Buffer是缓冲区的抽象类：

其中ByteBuffer是用得最多的实现类(在管道中读写字节数据)。

根据数据类型不同（boolean除外），提供了相应类型的缓冲区：

**上述缓冲区的管理方式几乎一样，都是通过allocate()获取缓冲区**
> Buffer的实现类
> + ByteBuffer
> + CharBuffer
> + ShortBuffer
> + IntBuffer
> + LongBuffer
> + FloatBuffer
> + DoubleBuffer  
**拿到一个缓冲区我们往往会做什么？很简单，就是读取缓冲区的数据/写数据到缓冲区中。所以，缓冲区的核心方法就是:**
+ put()：存入数据到缓冲区中
+ get()：获取缓冲区中的数据  
**Buffer类维护了4个核心变量属性来提供关于其所包含的数组的信息。它们是：**  


|属性|解释|
|:--:|:---:|
|capacity|容量，表示缓冲区中最大存储数据的容量。一旦声名不能改变|
|limit|界限，表示缓冲区中可以操作数据的大小。（limit后数据不能进行读写）|
|position|位置，表示缓冲区中正在操作数据的位置|
|mark|标记，表示记录当前position的位置。可以通过reset()恢复mark的位置|  

### 2.1.2 Buffer代码演示
**首先展示一下是如何创建缓冲区的，核心变量的值是怎么变化的。**
```java
public static void main(String[] args) {
// 创建一个缓冲区
ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

// 看一下初始时4个核心变量的值
System.out.println("初始时-->limit--->"+byteBuffer.limit());
System.out.println("初始时-->position--->"+byteBuffer.position());
System.out.println("初始时-->capacity--->"+byteBuffer.capacity());
System.out.println("初始时-->mark--->" + byteBuffer.mark());

System.out.println("--------------------------------------");

// 添加一些数据到缓冲区中
String s = "Java3y";
byteBuffer.put(s.getBytes());

// 看一下初始时4个核心变量的值
System.out.println("put完之后-->limit--->"+byteBuffer.limit());
System.out.println("put完之后-->position--->"+byteBuffer.position());
System.out.println("put完之后-->capacity--->"+byteBuffer.capacity());
System.out.println("put完之后-->mark--->" + byteBuffer.mark());
}
```

