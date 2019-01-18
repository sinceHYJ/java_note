##### 客户端
```java
public static String requestNonBlocking(String server, String params, int port) throws Exception {

        byte[] argument = params.getBytes();
        int servPort = port != -1 ? port : 80;
        //创建一个信道，并设为非阻塞模式
        SocketChannel clntChan = SocketChannel.open();
        clntChan.configureBlocking(false);
        //向服务端发起连接
        if (!clntChan.connect(new InetSocketAddress(server, servPort))) {
            //不断地轮询连接状态，直到完成连接
            while (!clntChan.finishConnect()) {
                logger.info("连接已建立！");
            }
        }
        //分别实例化用来读写的缓冲区
        ByteBuffer writeBuf = ByteBuffer.wrap(argument);

        // 写数据
        while (writeBuf.hasRemaining()) {
            clntChan.write(writeBuf);
        }

        // 接收数据
        ByteBuffer readBuf = ByteBuffer.allocate(READ_BUFFER_SIZE);
        StringBuilder stringBuffer = new StringBuilder();
        //其实是在给buffer写数据
        int byteLen = clntChan.read(readBuf);
        while (byteLen != -1) {
            byteLen = clntChan.read(readBuf);
            readBuf.flip();
            while (readBuf.hasRemaining()) {
                stringBuffer.append((char) readBuf.get());
            }
            readBuf.clear();
        }

        // 将接收到的数据进行返回
        String result = stringBuffer.toString();
        //关闭通道
        clntChan.close();
        return result;
    }

```
##### 服务端
```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.util.Iterator;

/**
 * @ClassName ServerNonBlocking
 * @Author heyingjie
 * @Date 2018/8/27 15:06
 * @Description
 */
public class ServerNonBlocking {
    //缓冲区的长度
    private static final int BUFSIZE = 256;
    //select方法等待信道准备好的最长时间
    private static final int TIMEOUT = 3000;
    public static void main(String[] args) throws IOException {
        if (args.length < 1){
            throw new IllegalArgumentException("Parameter(s): <Port> ...");
        }
        //创建一个选择器
        Selector selector = Selector.open();
        for (String arg : args){
            //实例化一个信道
            ServerSocketChannel listnChannel = ServerSocketChannel.open();
            //将该信道绑定到指定端口
            listnChannel.socket().bind(new InetSocketAddress(Integer.parseInt(arg)));
            //配置信道为非阻塞模式
            try {
                listnChannel.configureBlocking(false);
            } catch (IOException e) {
                e.printStackTrace();
            }
            //将选择器注册到各个信道
            listnChannel.register(selector, SelectionKey.OP_ACCEPT);
        }
        //创建一个实现了协议接口的对象
        TCPProtocol protocol = new EchoSelectorProtocol(BUFSIZE);
        //不断轮询select方法，获取准备好的信道所关联的Key集
        while (true){
            //一直等待,直至有信道准备好了I/O操作
            if (selector.select(TIMEOUT) == 0){
                //在等待信道准备的同时，也可以异步地执行其他任务，
                //这里只是简单地打印"."
                System.out.print(".");
                //continue;
            }
            //获取准备好的信道所关联的Key集合的iterator实例
            Iterator<SelectionKey> keyIter = selector.selectedKeys().iterator();
            //循环取得集合中的每个键值
            while (keyIter.hasNext()){
                SelectionKey key = keyIter.next();

                if (key.isAcceptable()){
                    protocol.handleAccept(key);
                }

                if (key.isReadable()){
                    protocol.handleRead(key);
                }
                if (key.isValid() && key.isWritable()) {
                    protocol.handleWrite(key);
                }
                //这里需要手动从键集中移除当前的key
                keyIter.remove();
            }
        }
    }
}
```
##### 处理的操作
```java
/**
 * @ClassName EchoSelectorProtocol
 * @Author heyingjie
 * @Date 2018/8/27 16:22
 * @Description
 */
public class EchoSelectorProtocol  implements TCPProtocol{

    private int bufSize; // 缓冲区的长度
    public EchoSelectorProtocol(int bufSize){
        this.bufSize = bufSize;
    }

    //服务端信道已经准备好了接收新的客户端连接
    @Override
    public void handleAccept(SelectionKey key) throws IOException {
        System.out.println("handleAccept方法"+key.channel().toString());
        SocketChannel clntChan = ((ServerSocketChannel) key.channel()).accept();
        clntChan.configureBlocking(false);
        //将选择器注册到连接到的客户端信道，并指定该信道key值的属性为OP_READ，同时为该信道指定关联的附件
        clntChan.register(key.selector(), SelectionKey.OP_READ, ByteBuffer.allocate(bufSize));

    }

    //客户端信道已经准备好了从信道中读取数据到缓冲区
    @Override
    public void handleRead(SelectionKey key) throws IOException{
        System.out.println("handleReader方法"+key.channel().toString());
        SocketChannel clntChan = (SocketChannel) key.channel();
        //获取该信道所关联的附件，这里为缓冲区
        ByteBuffer buf = (ByteBuffer) key.attachment();

        long bytesRead = clntChan.read(buf);
        //如果read（）方法返回-1，说明客户端关闭了连接，那么客户端已经接收到了与自己发送字节数相等的数据，可以安全地关闭
        if (bytesRead == -1){
            clntChan.close();
        }else if(bytesRead > 0){
            //如果缓冲区总读入了数据，则将该信道感兴趣的操作设置为为可读可写
            key.interestOps(SelectionKey.OP_READ | SelectionKey.OP_WRITE);
        }
    }

    //客户端信道已经准备好了将数据从缓冲区写入信道
    @Override
    public void handleWrite(SelectionKey key) throws IOException {
        System.out.println("handleWriter方法"+key.channel().toString());
        //获取与该信道关联的缓冲区，里面有之前读取到的数据
        ByteBuffer buf = (ByteBuffer) key.attachment();
        //重置缓冲区，准备将数据写入信道
        buf.flip();
        SocketChannel clntChan = (SocketChannel) key.channel();
        //将数据写入到信道中
        clntChan.write(buf);
        if (!buf.hasRemaining()){
            //如果缓冲区中的数据已经全部写入了信道，则将该信道感兴趣的操作设置为可读
            clntChan.close();
        }
        //为读入更多的数据腾出空间
        //buf.compact();
    }
}
```

该实例中，将客户端发送的数据原原本本返回去。返回完成之后则关闭通道。