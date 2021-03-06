### scan、sscan、hscan、zscan 命令

==该命令的时间复杂度为哦o(1),可在正式环境使用。不同于keys命令（时间复杂度为o(n)）。==

#### 用法

> scan 游标 match 匹配模式 count 限制返回数量

```
scan 176 MATCH *11* COUNT 1000
```


#### 各命令简介

- SCAN 命令用于迭代当前数据库中的key集合。
- SSCAN 命令用于迭代SET集合中的元素。
- HSCAN 命令用于迭代Hash类型中的键值对。
- ZSCAN 命令用于迭代SortSet集合中的元素和元素对应的分值。

#### scan基本用法
SCAN命令是一个基于游标的迭代器。这意味着命令每次被调用都需要使用上一次这个调用返回的游标作为该次调用的游标参数，以此来延续之前的迭代过程

当SCAN命令的游标参数被设置为 0 时， 服务器将开始一次新的迭代， 而当服务器向用户返回值为 0 的游标时， 表示迭代已结束。

```
redis 127.0.0.1:6379> scan 0
1) "17"
2)  1) "key:12"
    2) "key:8"
    3) "key:4"
    4) "key:14"
    5) "key:16"
    6) "key:17"
    7) "key:15"
    8) "key:10"
    9) "key:3"
   10) "key:7"
   11) "key:1"
redis 127.0.0.1:6379> scan 17
1) "0"
2) 1) "key:5"
   2) "key:18"
   3) "key:0"
   4) "key:2"
   5) "key:19"
   6) "key:13"
   7) "key:6"
   8) "key:9"
   9) "key:11"
```
在上面这个例子中， 第一次迭代使用 0 作为游标， 表示开始一次新的迭代。第二次迭代使用的是第一次迭代时返回的游标 17 ，作为新的迭代参数 。

显而易见，SCAN命令的返回值 是一个包含两个元素的数组， ++第一个数组元素是用于进行下一次迭代的新游标， 而第二个数组元素则是一个数组， 这个数组中包含了所有被迭代的元素++。

在第二次调用 SCAN 命令时， 命令返回了游标 0 ， 这表示迭代已经结束， 整个数据集已经被完整遍历过了。

#### scan命令的特性
##### 遍历的元素
- scan会返回从遍历开始到结束一直存在的元素。
- 同样，如果一个元素在开始遍历之前被移出集合，并且在遍历开始直到遍历结束期间都没有再加入，那么在遍历返回的元素集中就不会出现该元素。

##### 缺点
- **重复**：scan命令可能会返回重复元素，去重的工作应该有应用程序进行处理，同时保证程序的正确性。
- **不确定性**：如果一个元素是在迭代过程中被添加到数据集的， 又或者是在迭代过程中从数据集中被删除的， 那么这个元素可能会被返回， 也可能不会。


#### MATCH

功能对元素的模式匹配工作是在命令从数据集中取出元素后和向客户端返回元素前的这段时间内进行的， 所以如果被迭代的数据集中只有少量元素和模式相匹配， 那么迭代命令或许会在多次执行中都不返回任何元素。

