# Remote Dictionary Server（远程数据服务）

## redis

### 1.简介
redis是一种缓存数据库，key-value型，可将数据存储在内存中，也可持久化到硬盘中。

Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务

可以支持复杂的数据结构：
- string类型：key-value
- lists列表：1个key存储多个值，按插入顺序排序的字符串元素的集合，使用链表形式实现如下图所示：
- sets集合：不重复且无序的字符串元素的集合。
- hashes散列：由field和关联的value组成的map。field和value都是字符串的；
- sorted sets有序集合：但是每个字符串元素都关联到一个叫score浮动数值（floating number value）。里面的元素总是通过score进行着排序，所以不同的是，它是可以检索的一系列元素。（例如你可能会问：给我前面10个或者后面10个元素）。


每个value可最大存储1G；

使用队列实现单线程存储操作；