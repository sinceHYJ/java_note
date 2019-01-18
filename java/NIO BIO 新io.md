### NIO
原来的io是阻塞的，新io完全采用新的理念。主要的概念：

- **Channels and Buffers（通道和缓冲区**）：标准的IO基于字节流和字符流进行操作的，而NIO是基于通道（Channel）和缓冲区（Buffer）进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。

- **Asynchronous IO（异步IO**）：Java NIO可以让你异步的使用IO，例如：当线程从通道读取数据到缓冲区时，线程还是可以进行其他事情。当数据被写入到缓冲区时，线程可以继续处理它。从缓冲区写入通道也类似。

- **Selectors（选择器）**：Java NIO引入了选择器的概念，选择器用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个的线程可以监听多个数据通道


![image](BB2D74B3F7544D3597A7B1DFF21D6FB4)

#### 1.通道与缓冲区
我们可以将其想象成1个煤矿，通道（channel）是一个包含煤层（数据）的矿，而缓冲区（buffer）是派送矿藏的卡车。卡车载送这煤炭进出，我们从卡车上取数据，也就是说我们不会和通道直接发生关系。我们只与缓冲区交互。

通道是全双工的

ByteBuffer是将数据移进、移出通道的唯一方式。

通道的write操作是将缓冲区里的数据写入通道里。
![通道、缓冲区的读写方向](WEBRESOURCEd083ba8bd099856c67d510704945e0a3)

#### 2.缓冲区
##### 用缓冲区操作数据
buffer有一系列直接操作数据的方法。
```java
clear()
flip()
mark()
position()
capacity()
```
##### 视图缓冲器
要时刻明白ByteBuffer里面存储是字节，不是可以直接读写的字符等。

但是我们可以**视图缓冲器**将字节缓冲期以什么样的格式进行读写；例如：
```java
fc = new FileOutputStream("data.txt").getChannel();
buff = ByteBuffer.allocate(1024);
buff.asCharBuffer().put("some text");
fc.write(buff);
// 读取数据
buff.getInt()
buff.getFloat()
buff.getDouble()
```
或者可以将ByteBuffer转化为各类型对应的buffer,然后调用get()方法;
```java
CharBuffer charbuff = buff.asCharBuffer();
charbuffer.get();
```
在存储字节时按照高位优先的方式存储，即将最重要的字节放在地址的存储单元。

对于一段同样的字节缓冲区，使用不同的视图缓冲器读取出来的数据肯定是不一样的。因为美中基本类型所占用的长度是不一样的。
##### 3.选择器 selector
使用选择器可用给一个线程监听多个channel上发生的事件。例如使用1个选择器监听网络上的多个信道端口。

<html>
<!--在这里插入内容-->
<font color = red>要时刻记住异步的操作：并不是等完成之后才返回，有可能是操作未完成就已经返回。例如：连接未建立之前已经返回、数据尚未全部写完之后就返回。</font>
</html>

