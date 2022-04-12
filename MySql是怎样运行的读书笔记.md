# Mysql脑图

Mysql基础：https://www.processon.com/view/link/61717706f346fb564e2abb9a

Mysql高级：https://www.processon.com/view/link/61cc84b01efad4259ceb1b19



# 第四章 从一条记录说起——InnoDB记录存储结构

告诉你一条记录行是怎么存储在磁盘上的

**Innodb页**

​		Innodb存储引擎是将表中的数据存放到磁盘上的引擎，处理数据的过程发生在内存中，需要把磁盘中的数据加载到内存中在进行处理，当发生写入或者是更新的操作后，这个存储引擎还需要把记录刷新到磁盘中，但是这样做的话会产生磁盘的IO，本身的磁盘IO读写已经非常的慢了，如果把记录一条一条的加载到磁盘中的话就更慢，所以规定了用**页**来进行磁盘到IO之间的切换，默认大小是**16KB**，意味着这个存储引擎每次发生交互的时候默认会加载16KB的内容到内存，写入到内存也是一样的



**Innodb的行格式**

​		一条记录可以理解为表中的一长条的内容，这些记录通过Innodb存储引擎进行操作，这些记录存放在磁盘中有**四种行格式**：分别为：COMPACT,REDUNDANT,DYNAMIC,COMPRESSED（翻译过来就是：**紧凑，冗余，动态，压缩**）这四种，用ROW_FORMAT = 行格式名称来进行选择



**COMPACT行格式**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/a8d2618a683ce0545ce05a0ebd7c6c2.jpg?raw=true)

分为额外信息还有真实数据（一条数据的）

额外信息：

​		变长字段长度列表：因为在创建表的时候类似于Varchar(10)这种类型，他真正**存储多少字节的数据**是不固定的的，那么为了存储的安全性，需要知道这一条记录中那个字段是变长的，长度分别是多少，并且这个长度的存放顺序是根据列在记录中的顺序是**相反的**

​		具体的字段内容占用的字节数用**1字节或者2字节**可以表示，具体的做法是，比如采用不同的字符集进行编码的字符集需要表示一个字符的字节数是不同的，那么这个称之为W,M表示这个变长字段最多可以存储多少个字符，L表示这个字符实际占用的字节数，如果W*M>255字节的且L大于127字节，那么就用两字节来表示占用的字节数是多少，**这个1字节后者2字节里面存放着这个字段实际占用字节的大小是多少**



​		NULL值列表：如果把null值放入记录的真实数据那么会很浪费空间，所以特意把这些null值放到NULL值列表中，0表示当前这个位置的数据不为null，1表示当前的位置是null的

对应关系：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/47f7ebe248d174995b716ba463ee096.jpg?raw=true)

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/24d7b4d84a028c8b2d4e6dc7c61eab1.jpg?raw=true)



​		记录头信息：这部分内容有5个字节，里面记录了一些信息



真实数据：

除了记录这几个字段的真实数据，Mysql还会默认为每个记录加上隐藏列：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/cb13fd72877ee60d46150e7d70550d8.jpg?raw=true)

Mysql的主键生成策略：首先会使用用户定义的主键，如果没有定义的话，会找列为非null并且UNIQUE的列作为主键，如果都没有的话会使用row_id作为主键

完整形式：

























