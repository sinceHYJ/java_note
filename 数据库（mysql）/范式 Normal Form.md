# 含义
设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小。

**每个范式都是再上一个范式的基础上增加了限制。**

# 1NF 第一范式
第一范式考虑的是**原子性**，即每列不可再分。

比如，某联系人表具有姓名、性别、电话3个字段，其中假设字段电话有家庭电话，工作电话2种，则这个不满足第一范式的要求。该表应设计为：姓名、性别、家庭电话、工作电话。

# 2NF 第二范式
在1NF的基础上，增加**非主键列必须完全依赖于主键列**。所谓完全依赖是指不能存在仅依赖于主键一部分的情况。如果有，则该列及主键的一部分应该拆分出来为一个新的表，原表、新表为一对多关系。

比如：订单明细表：【OrderDetail】（OrderID，ProductID，UnitPrice，Discount，Quantity，ProductName）。 
因为我们知道在一个订单中可以订购多种产品，所以单单一个 OrderID 是不足以成为主键的，主键应该是（OrderID，ProductID）。显而易见 Discount（折扣），Quantity（数量）完全依赖（取决）于主键（OderID，ProductID），而 UnitPrice，ProductName 只依赖于 ProductID。所以 OrderDetail 表不符合 2NF。不符合 2NF 的设计容易产生冗余数据。 
可以把【OrderDetail】表拆分为【OrderDetail】（OrderID，ProductID，Discount，Quantity）和【Product】（ProductID，UnitPrice，ProductName）来消除原订单表中UnitPrice，ProductName多次重复的情况。


# 3NF 第三范式

第三范式：**消除传递依赖**。

传递依赖的含义是，非主键列A依赖于主键，而另一非主键列依赖于列A，传递依赖。

比如：考虑一个订单表【Order】（OrderID，OrderDate，CustomerID，CustomerName，CustomerAddr，CustomerCity）主键是（OrderID）。 

其中 OrderDate，CustomerID，CustomerName，CustomerAddr，CustomerCity 等非主键列都完全依赖于主键（OrderID），所以符合 2NF。不过问题是 CustomerName，CustomerAddr，CustomerCity 直接依赖的是 CustomerID（非主键列），而不是直接依赖于主键，它是通过传递才依赖于主键，所以不符合 3NF。 
通过拆分【Order】为【Order】（OrderID，OrderDate，CustomerID）和【Customer】（CustomerID，CustomerName，CustomerAddr，CustomerCity）从而达到 3NF。

# 注意

<html>
<font color=red>2NF与3NF的区别：2NF：非主键列是否完全依赖于主键；3NF：非主键列是依赖于主键还是依赖于其它非主键列。</font>
</html>



