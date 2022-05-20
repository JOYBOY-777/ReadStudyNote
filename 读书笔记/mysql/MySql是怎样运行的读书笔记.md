# Mysql脑图

Mysql基础：https://www.processon.com/view/link/61717706f346fb564e2abb9a

Mysql高级：https://www.processon.com/view/link/61cc84b01efad4259ceb1b19



# 第4章 从一条记录说起——InnoDB记录存储结构

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

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/056e8833acfdaf4b99419e8c92941ba.jpg?raw=true)



溢出页：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/0cd2d7421fb81ad03da0449f437ce72.jpg?raw=true)

如果一个字段的大小非常大的话甚至已经超过了一条记录的大小65532字节，那么真实数据中会存储两部分，第一部分是768字节的真实数据，另一部分是其他数据页的地址占用20字节，这些地址指向存储数据的页，并且这些页中用链表连接起来



# 第5章 盛放记录的大盒子——Innodb数据页结构

这章主要是介绍**数据页**的基本结构，上一章介绍了**一行记录**在磁盘中是怎么存储的

**数据页的结构和占用大小**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/innodb%E9%A1%B5%E7%A4%BA%E6%84%8F%E5%9B%BE.jpg?raw=true)

详细说明：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%A1%B5%E7%BB%93%E6%9E%84.jpg?raw=true)



**记录在页中存储（User Records + infimum Supremum）**

​		在我们插入记录的时候，实际上就是把第四章说的记录行存储到**User Records**中，因为这个user records和free space部分占用的字节数是不确定的，所以每当插入一条记录的时候都会去**free space**中**申请空间**，如果这个free space中的空间用完了，如果在插入行记录的话，那么就会去申请**其他的页**，如下：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%AE%B0%E5%BD%95%E5%9C%A8%E9%A1%B5%E4%B8%AD%E7%9A%84%E5%AD%98%E5%82%A8.jpg?raw=true)



插入数据后，记录头在页中的表示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%AE%B0%E5%BD%95%E5%9C%A8%E9%A1%B5%E7%9A%84%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84.jpg?raw=true)

可以看到里面有6个比较重要的属性分别是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%AE%B0%E5%BD%95%E5%A4%B4%E4%BF%A1%E6%81%AF%E7%9A%84%E5%B1%9E%E6%80%A71.jpg?raw=true)

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%AE%B0%E5%BD%95%E5%A4%B4%E4%BF%A1%E6%81%AF%E7%9A%84%E5%B1%9E%E6%80%A72.jpg?raw=true)

详细介绍：

1. delete_flag:用来标记这行记录是否被删除，0没删除，1被删除

   注意：被删除的数据也会在磁盘上存在，因为如果删除了，那么就会把磁盘上的记录重新排列，会有性能上的消耗，所以打上一个删除标记（相当于**物理删除**），被删除的记录会形成一个垃圾链表，被称为可重用空间，当有新的记录插入进这个页的时候，就会**覆盖**掉这些记录占用的空间

2. min_rec_flag:B+树中**非叶子节点**中的**最小**的目录项记录会被记录在这个里面，**0**表示**不是**当前的B+树中最小的记录项

3. n_owned:

4. head_no:因为记录时一条一条的紧密排列在数据库中的，那么就把这个结构称之为**堆**，然后把**记录行**在**堆的相对位置**称之为head_no，前面位置的head_no较小，后面的偏大，并且大1，在图5-5中可以看到这个属性没有0和1

   注意：这个属性没有0和1是因为Mysql自动给这个里面插入了两条记录，称之为伪记录，或者虚拟记录（因为不是用户插入的），一条代表这个页中最小记录（Infimum），另外一个是最大记录(Supremun)，他们的相对位置靠前，任何的用户记录都比最小记录大，比最大记录小

   ​			这两部分不是存放在user records部分的，单独存放在**Infimum+Supremun**，是一个独立的空间

5. recore_type:表示当前记录的类型：0（普通记录）1（B+数非页节点目录项记录）2（最小记录Infimum）3（最大记录Supremun）

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%AE%B0%E5%BD%95%E5%AD%98%E6%94%BE%E6%96%B9%E5%BC%8F.jpg?raw=true)

6. next_record：表示从当前记录的真实数据到下一条记录的真实数据的距离，这个数为正：下一条记录在当前记录的后面，为负：说明在前面

   比如一条记录的这个属性为32，那么就说明当前记录向后找**32字节**就是下一条记录，无论怎么对页中的记录进行增删改查的操作，Innodb始终会维护记录的一个单向链表，并且各个节点按照**主键值**从小到达顺序排列

   Null值列表中的信息为什么要**逆序排放**因为可以使记录中位置靠前的字段和对应的字段程度信息在内存中的位更近，从而提高**高速缓存的命中率**

   ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BD%BF%E7%94%A8%E7%AE%AD%E5%A4%B4.jpg?raw=true)



**页目录（Page Director）**

页目录中有槽，槽是能快速定位到对应分组的**最大记录**的位置，这样就避免了遍历来查找记录行了，会采用二分查找的方式来找到该记录所在分组对应的槽，之后找到槽所在分组中**最小**的那个记录，因为找到了最小的，在通过next_record来**遍历分组中的记录**



**页面头部（Page Header）**

目的是得到存储在**数据页**中记录的**状态信息**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/pageHeader%E7%BB%93%E6%9E%84%E5%8F%8A%E6%8F%8F%E8%BF%B0.jpg?raw=true)



**文件头部（File Header）**

记录各种页的通用信息

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/file%20header.jpg?raw=true)



**文件尾部（File Trailer）**

用于检验页是否完整，占用8字节



# 第6章 快速查询的密集——B+树索引

在innodb存储引擎中，数据页之间用双向链表连接，页中的各个记录之间用单向链表连接，如下图所示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%A1%B5%E5%92%8C%E8%AE%B0%E5%BD%95%E7%9A%84%E5%85%B3%E7%B3%BB.jpg?raw=true)

​		在没有索引的时候，如果用**主键的方式**来查找的话就对槽采用**二分查找**的方式来确定对应的分区，找到分区中主键最小的行记录后再进行从前往后的遍历，以其他条件进行搜索的话：只能从前往后进行遍历，非常的耗费时间，如果记录太多了，分散在很多个页中，那么就需要定位到页，但是也需要把页进行从前往后的遍历

​		

**聚簇索引**：

​		要保证下一个数据页中记录的主键值必须大于上一个页中的，一个简单的聚簇索引记录如下：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%81%9A%E7%B0%87%E7%B4%A2%E5%BC%95.jpg?raw=true)

目录项记录：只存储对应页中的最小的主键值（key）

用户记录：和这个最小主键值所在的页号（page）

B+树中用户记录的页，或者是最下层叶子节点的页中间的记录都是按照**主键的值进行从小到大**的顺序进行排列的，并且在最下层的页中存放着**完整的用户插入记录行**

注意：

假设用户记录页可以存放100条记录，目录项记录可以存放1000条

那么：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/b+%E6%A0%91%E5%AD%98%E6%94%BE%E8%AE%B0%E5%BD%95%E6%95%B0.jpg?raw=true)



**二级索引**（辅助索引）：

​	![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BA%8C%E7%BA%A7%E7%B4%A2%E5%BC%95.jpg?raw=true)这个二级索引与聚簇索引最大的区别就是，聚簇索引是根据主键的大小从小到大进行排列的，并且最下边的一层页节点是完整的用户记录。但是二级索引是你在那个列上加了索引，他就按照那个列的顺序从小到大排列，**当列相等的情况下按照主键的大小排序**，并且最下面的一层页节点不是完整的用户记录，而是**索引列的值+主键**的形式，然后**再从聚簇索引的页节点找到完整的用户记录**，这个过程称为**回表**	



但是如果在目录页中出现两个页的最小索引列的值是一样的情况，那么就不知道这个记录放在那个页中了，所以就需要目录页的**唯一性**（不能出现放在哪个页里面都行的情况），所以要在目录页中加入主键

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%A1%AE%E8%AE%A4%E5%94%AF%E4%B8%80%E6%80%A7.jpg?raw=true)



**联合索引**：

就是把索引加在多个列上（c2,c3），实际上就是把各个记录和页先按照c2列排序，如果c2列相等的话就按照c3列排序

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95.jpg?raw=true)

除了最底层的节点，其他的都是c1,c2页号的格式组成的双向链表，最下面一层是由c1,c2主键构成的，获取完整的记录再去聚簇索引那里回表，获取完整的用户记录

总结：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%B4%A2%E5%BC%95%E6%80%BB%E7%BB%93.jpg?raw=true)



# 第7章 B+树索引的使用

**索引的代价**（在空间和时间上都会出现消耗）

空间上的代价：每建立一个索引，都会相应的产生一棵对应的B+树，一个B+树有很多的节点组成，每个页16kb，如果b+树很大的话那么占用磁盘的空间就会很大。

时间上的代价：在增删改查的时候，会破坏节点和记录的顺序，需要额外的时间进行**页面分裂，页面回收**，维护节点和记录的排序，



**应用B+树索引**

如果想使用某个索引来执行查询，但是**无法通过搜索条件**形成合适的扫描区间来减少需要扫描的记录数量的时候，就不考虑用这个索引

**注意事项：**

1. 所有的搜索条件都可以形成合适的扫描区间

```mysql
select * from single_table where key2>100 and key2>200;
```

这里形成的扫描区间就是key2>200，减少了索引要扫描的数量，可以使用key2上的索引

```mysql
select * from single_table where key2>100 or key2 >200;
```

形成的扫描区间是key2>100，减少了扫描数量，说明这个索引建的还是有用的，减少了扫描范围



2. 有的搜索条件**不能生成**合适的扫描区间

先看例子：

```mysql
select * from single_table where key2>100 and common_field = 'abc';
```

可以看到使用key2索引可以形成(2,正无穷)的扫描区间，但是对于列common_field来说的话，因为没有给这个字段形成扫描区间，所以他的扫描区间是(负无穷，正无穷)，那么由于用and的作用，两边都为true才是true，由于右边的语句对我们减少扫描区间没有作用，那么我们就会把它变为true,从而整个sql变为key2>100 and true,所以我们可以简化：

```	mysql
select * from single_table where key2>100
```

那么，如果用**or**连接的话：

```mysql
select * from single_table where key2>100 or common_field = 'abc';
```

按照上面的逻辑简化：

```mysql
select * from single_table where key2>100 or true
```

简化：

```mysql
select * from single_table where  true
```

结果显而易见，这个索引扫描的条数没有减小，还不用用聚簇索引全表扫描，这期间还少了回表的操作，所以这样写sql是性能浪费很大的，不合理的



从复杂的搜索条件中找出扫描区间

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%A4%8D%E6%9D%82%E7%9A%84%E6%90%9C%E7%B4%A2%E6%9D%A1%E4%BB%B6.jpg?raw=true)

对于这种复杂的搜索条件的话，这里面既然用到了三个索引那么我们就按照用到了那个索引，分别对这个索引进行条件的简化，最后在看看，他的搜索条件是否减少，减少了用索引就是比去全表扫描快的



使用**联合索引**执行查询时对应的扫描区间

我们给三个varchar(20)的字段设立了一个联合索引，这个索引在磁盘中的样子是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95%E5%AE%9E%E4%BE%8B.jpg?raw=true)

重点来了，针对这个联合索引来说的查询语句

```mysql
select * from single_table where key_part1 = 'a'
```

那么这个形成的扫描区间就是：['a','a']的区间，形成这个搜索条件的语句是：key_part = 'a'，形成的图如下：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/7-11.jpg?raw=true)

['a','a']的解释就是第一条符合搜索条件的，到最后一个符合搜索条件的，因为在联合索引中只涉及到了一个列，那么扫描区间就有一个

扫描区间：[('a','b'),('a','b')]形成的搜索条件：key_part1 = 'a' and key_part2 = 'b'

```mysql
select * from single_table where key_part1 = 'a' and key_part2 = 'b'
```

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/7-12.jpg?raw=true)

这个的意思就是两个索引**从上到下看**起来第一条符合搜索条件的，到最后一条符合搜索条件的



```mysql
select * from single_table where key_part1 = 'a' and key_part2 = 'b' and key_part3 = 'c'
```

形成的扫描区间：以及示意图其实都是一样的，只不过c也增加了排序



```mysql
select * from single_table where key_part1 < 'a'
```

形成的扫描区间：(负无穷,a)，对应的内存图：第一列索引不符合条件的，上色

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/7-13.jpg?raw=true)



```mysql
select * from single_table where key_part1 = 'a' and key_part2 > 'a' and key_part2 < 'd'
```

形成的扫描区间：[(a,a),(a,d)]

内存图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/7-14.jpg?raw=true)



```mysql
select * from single_table where key_part2 = 'a'
```

因为这个索引列属于联合索引，三个字段一起的，先是根据key_part1进行排序，但是这个是直接用的key_part2，

因为这个二级索引的记录不是按照这个字段来进行排序的，因此这个搜索条件使用不到这个联合索引



```mysql
select * from single_table where key_part1 = 'a' and key_part3 = 'c'
```

由于这个联合索引先是按照key_part1进行排序，但是他只能根据这个key_part1 = 'a'的搜索条件定位到扫描区间[a,a]这个扫描区间与后面的查询语句key_part = 'c'没有关系，我们可以先根据这个条件查询出携带key_part3的记录，如果后面的记录符合等于c的条件，那么就进行回表操作，否则就不回表，这种方法叫**索引下推**



```mysql
select * from single_table where key_part1 < 'b' and key_part2 = 'a'
```

这个只能根据联合索引找到key_part1 < 'b'的记录，不能根据后面的条件进一步的减少需要扫描的数量，因为当前<b的部分还是按照key_part1的顺序来进行排序的，扫描区间是(负无穷，b),**所以尽量利用搜索条件来减少需要扫描的数量**是很关键的

内存图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/7-15.jpg?raw=true)



```mysql
select * from single_table where key_part1 <= 'b' and key_part2 = 'a'
```

别看就加了个=，但是有了这=后面的key_part2 = 'a'就可以帮助减少要扫描的数目了，虽然在key_part1 <'b' 的部分中的顺序不是按照key_part2的顺序排列的，但是**等于key_part的部分是按照key_part2的顺序进行排列的**，因为联合索引的特性



**索引用于排序**

在mysql中，在内存或者磁盘中进行排序的方式统称为**文件排序**，那么索引搭配上排序就会出现一些神奇的事情

```mysql
select * from single_table order by key_part1,key_part2,key_part3
```

因为有了联合索引，那么就沿着记录行所在的单向链表向后扫描就行了，因为这个联合索引已经替你排好序了



**使用联合索引排序时注意事项**

1. order by子句后面的列的顺序也必须跟你定义的联合索引的顺序是一致的，因为联合索引的列排列的顺序是固定的
2. 当你用order by key_part1,或者order by key_part1,key_part2进行排序时也是可以的
3. 当前两个联合索引列的值确定后对第三个列进行排序那么和这个联合索引也可以使用比如：

```mysql
select * from single_table where key_part1 = 'a' and key_part2 = 'b' order by key_part3 limit 10
```

这个sql就是能利用到联合索引的排序，因为列等于a按照第二个列等于b排，这两个都相等按照key_part3排序



**不可以使用索引进行排序的情况**

1. ASC(默认),DESC混用的情况

以下两种排序的可以用到索引：

```mysql
order by key_part1,key_part2 limit 10
等价于
order by key key_part1 desc,key_part2 desc limit 10
```

这两种都可以用到索引，但是保证必须排序的顺序是相同的，要么一致，不能混用

混用的情况：

```mysql
select * from single_table order by key_part1,key_part2 desc limit 10;
```

这样的话就得反复的从索引中查找记录，但是这样的混用**是不会使用联合索引**执行排序的



排序列包含非同一个索引的列,就是用来排序的列不是同一个索引

```mysql
select * from single_table order by key1,key2 limit 10;
```

很显然，key1,key2不是属于同一个索引



排序列与联合索引的列不连接

```mysql
select * from single_table order by key_part1,key_part3 limit 10;
```

很显然，key_part1与key_part3中间断开了，缺少了key_part2，那么这种情况也不能走联合索引（顺序要一致）



形成扫描区间的索引列与排序列不同

```mysql
select * from single_table where key1 = 'a'order by key2 limit 10;
```

这个形成了扫描区间，但是用不到key2这个索引，感觉这两个压根就没有什么关系...



不能把索引列加上函数的名字放在排序列里面

```mysql
select * from single_table order by UPPER(key1) limit10
```

比如说让这个字段变为大写在利用索引进行排序，有点扯，这是用不到索引的,换句话说就是索引白建了，不能利用索引已经排序的特点



**索引用于分组**

例子：

```mysql
select key_part1,key_part2,key_part3,count(*) from single_table group by key_part1,key_part2,key_part3;
```

这个sql的意思其实就是先把key_part1值相同的值进行分组，这个值一样之后再根据key_part2的值一样的进行分组，然后以此类推，如果不简历索引的话会产生很多的临时表，扫描聚簇索引的记录放在这个临时表中，但是有了索引（已经排好序了）就不用产生临时表的操作了

所以分组列的顺序要与联合索引列的**顺序一致**



**回表的代价**

找到二级索引进行回表后，先看看聚簇索引的页面在不在内存中，如果不存在内存中，还得需要读取**主键值不连续的聚簇索引记录**会产生很多的随机io，有时候如果需要进行回表的记录比较的多的时候，那其实还需要回表，就不如用全表扫描，去扫描聚簇索引的记录



**更好的创建个使用索引（重点）**

1. 只为用于**搜索，排序，或者分组的列**创建索引，比如出现在where,连接子句中的连接列，order by，group by子句的列创建索引

```mysql
select common_field,key_part3 from single_table where key1 = 'a';
```

common_field,key_part3这连个语句就没有必要建立索引了，where 后面的key1可以创建索引



2. 考虑索引列中**不重复值**的个数

用二级索引查出来的重复值的个数越多，那么需要回表的次数就越多，随机主键查找聚簇索引io的操作就越多，性能越不好，相反不重复的越多查出来的结果集就越少回表的次数就减小



3. 索引列的类型尽量的小

索引类的类型尽量的小的话，那么需要的空间就越小，就能正向的循环



4. 为列前缀简历索引

不如在给一个字符串简历索引的时候，尽管二级索引没有完整的用户记录，但是最下面的一层也需要存储索引列的值个主键，那么你的一个字符串的值越大，这个空间的浪费就越大，所以在创建这个索引的时候，给这个字符串的前几位创建索引为了节省空间

```mysql
alter table single_table add index idx_key1(key1(10);
但是这样就不能运用到排序上面了                                            
```



5. 覆盖索引

```mysql
select key1,id from single_table where key1 > 'a' and key1 < 'c';

select key1 from single_table order by key1
```

上面那个利用了二级索引的最下面一层时索引列+主键，这时候你要查询的列也是这两项，那么就避免了回表的操作，下面那个sql也是利用了索引已经排好序，你查询的时候又查找的是这个索引，也避免了回表的操作，所以在查询的时候，尽量只包含索引项，避免回表



6. 让索引列以列名的形式在搜索条件中**单独**出现

```mysql
这个mysql只会全表扫描
select * from single_table where key2*2<4;
这个会走索引
select * from single_table where key2<4/2;
```



7. 插入记录的时候尽量保证主键大小依次递增

这样就避免了在插入一个“不恰当”主键大小的记录行时页面要进行分裂的操作，所以一切就是为了节省资源



8. 冗余和重复索引

其实就是给一个列加上多个索引，每加一个索引就意味着多建一棵b+树，其实是没有必要的，可以利用之前的索引已经排列好的序，来节省空间



# 第10章 条条大路通罗马——单表访问方法

​		mysql的server层有个优化器，优化器把查询语句进行优化，优化完了之后生成一个**执行计划**，然后会按照执行计划中的步骤调用存贮引擎中的接口进行查询

这章主要是介绍mysql中怎么进行单表查询的，mysql执行查询语句的方式成为**访问方法**，在执行一条查询语句的时候，可以选择不同的访问方法进行查询



**访问方法介绍**：

**const：**通过**主键**或者**唯一二级索引**列来定位**一条记录**的访问方式，就是利用主键或者二级索引来搜索一条记录

```mysql
聚簇索引
select * from a where id = 100;
唯一二级索引
select * from a where key2 = 100;
```



**ref：**因为普通二级索引能匹配的记录有很多条,我们把用普通二级索引列与常数进行比较，并且形成的扫描区间为**单点扫描区间**，那么这种**采用二级索引**来进行查询的访问就是ref

```mysql
key3为普通二级索引
select * from a where key3 = 100;
```

注意无论是二级索引还是唯一二级索引，因为不限制Null值的数量所以访问方式最多是ref，如果有多个普通二级索引的搜索条件的话也是ref,只要有一个不是=的搜索条件就不行



**ref_or_null：**看名字就知道就是根据二级索引把列为空的值也找出来

```mysql
注意搜索列都是key1
select * from a where key1 = 'abc' or key1 is null
```



**range：**当使用二级索引进行查询的时候，如果产生了**多个单点扫描区间或者范围扫描区间**的访问方法

```mysql
select * from a where key2 in (1248,6961) or (key2 >= 38 and key2 <=88)
```



**index：**扫描全部的二级索引记录的访问方式，相当于要查询的字段就是全部的索引列

```mysql
select a,b,c from single_table where b = 'abc';
使用innodb进行全表扫描时添加了order by语句也是index
select * from a order by id
```



**all：** 使用全表扫描，就是扫描全部聚簇索引记录



**注意事项：**

二级索引+回表

当一条查询语句中有多个索引列的时候，一般会选择**使用消耗比较少的那个索引列**

```mysql
select * from a where key1 = 'abc' and key2 > 1000;
```

MRR多范围读取的意思是说，先读取一部分二级索引，之后排好序在进行回表，这样就比一个一个读取在回表的代价少一点



索引合并：

上一种情况的特殊情况，使用多个索引进行查询

```mysql
select * from a where key1 = 'a' and key2 = 'b';
```

实际上索引合并就是找交集的意思，分别从对应的索引树中找出符和条件的记录，然后在取并集，然后在回表，注意如果在使用某个二级索引执行查询时，读取出的二级索引记录不是按照主键的大小排列的话就不能使用索引合并，这样找并集没有顺序，根本不好找



union 索引合并

就是都先按照索引查找，查找完了在进行结果的去重

```mysql
select * from a where key1 = 'a' or key3 = 'b';
```

注意：查询出来的二级索引记录必须是根据主键的大小排好序的，才能使用索引合并



# 第11章 两个表的亲密接触——连接的原理

连接就是把表中的记录取出来进行依次匹配，并把匹配后的组合发给客户端

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%AC%9B%E5%8D%A1%E5%B0%94%E7%A7%AF.jpg?raw=true)

产生笛卡尔积的连接查询：

```mysql
select * from t1,t2;
```



**连接的过程**：

过滤条件的分类：

1. 涉及单表的分类：t1.m1>1
2. 涉及两表的分类：t1.m1 = t2.m2 、t1.m1 > t2.m2 等

例子：

```mysql
select * from t1,t2 where t1.m1 > 1 and t1.m1 = t2.m2 and t2.n2 < 'd'
```

连接的过程：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%BF%9E%E6%8E%A5%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B.jpg?raw=true)

首先从驱动表中（t1）找到两个驱动条件，然后根据两表的过滤条件进行相应条件的带入，然后再到被驱动表中查询，最后得到相应的结果集，驱动表被访问一次就行，被驱动表要访问若干次



**内连接和外连接：**

内连接：对于连接的两个表，驱动表中的记录在被驱动表中找不到匹配的记录，那么这个记录就不会加入结果集

外连接：对于连接的两个表，驱动表中的记录在被驱动表中找不到匹配的记录，那么这个记录也会加入结果集，匹配不到的记录用null值代替



左外连接语法：

```mysql
select * from t1 left[outer] join t2 on 连接条件[where 普通过滤条件]
```

右外连接语法：

```mysql
select * from t1 right[outer] join t2 on 连接条件[where 普通过滤条件]
```

内连接：

```mysql
select * from t1 [inner | cross] join t2 on 连接条件[where 普通过滤条件]
```

在内连接中**on子句的意思其实是与where**一样的

例子：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%BF%9E%E6%8E%A5%E4%BE%8B%E5%AD%90.jpg?raw=true)



**连接的原理：**

**嵌套循环连接：**

比如连接表有三张，先从驱动表中查出结果，然后在匹配被启动表，这时候这个表有成为了新的驱动表到第三个表中查询结果集，就相当于单个for循环一样嵌套，每查出一个记录返回客户端，然后在从驱动表中拿到下一条记录匹配



**使用索引加快连接速度：**

```mysql
select * from t1,t2 where t1.m1 > 1 and t1.m1 = t2.m2 and t2.n2 < 'd'
简化后：
select * from t2 where t2.m2 = 2 and t2.n2<'d';
select * from t2 where t2.m2 = 3 and t2.n2<'d';
```

可以选择在m2或n2加上索引，并且查询的时候最好查询出需要的列，尽量来利用索引



**基于块的嵌套循环连接（joinbuffer）：**

因为我们要根据从驱动表中查询出来的记录到被驱动表中查询，有多少个就查询多少次,这样导致随机io，我们可以在执行连接前**申请一个固定的内存空间**，把若干条驱动表中查询的记录放在这个空间中，然后开始扫描被驱动表（根据被驱动表的扫描条件），每一条被驱动表中的记录与多条驱动表中的记录匹配

例子：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/joinbuffer.jpg?raw=true)



# 第14章 基于规则的优化（内含子查询优化二三事）

这一章主要是讲了如何查询重写，就是把垃圾的语句变为可以高效执行的语句



**条件简化（查询优化器干的事）**

**移除不必要的括号**

```mysql
select * from (t1,(t2,t3)) where t1.a = t2.a and t2.b = t3.b
查询优化器移除
select * from t1,t2,t3 where t1.a = t2.a and t2.b = t3.b
```



**常量传递**

```mysql
a = 5 and b > a
转化成
a = b and b > 5
```



**移除没有用的条件**：把一些永远为真的条件优化掉

```mysql
(a < 1 and b = b) or (a = 6 or 5 != 5)
优化
(a < 1 and true) or (a = 6 or false)
简化
a < 1 or a = 6
```

在有表达式的时候也会直接把值给算出来

```mysql
a = 5 + 1那么直接就会算出来
```



**having子句和where子句的合并**

如果查询语句中没有出现sum,max等函数和group by语句，那么就会把这两个语句合并起来



**常数表检查**

利用主键等值匹配或者唯一二级索引列等值匹配作为搜索条件来进行查询



**外连接消除**

左外链接和右外连接只是在驱动表的选择上不同，那么查询优化器会把**右外连接转化为左外连接**，我们在写查询语句（连接语句）的时候，外连接可以和内连接相互转换，只需要在where后指定被驱动表中的类**is not null**就行了，或者指定一个字段不为null:

```mysql
select * from t1 left join t2 on t1.m1 = t2.m2 where t2.n2 is not null
```

这个语句就是跟inner join没有什么区别



```mysql
select * from t1 left join t2 on t1.m1 = t2.m2 where t2.m2 = 2;
相当于
select * from t1 join t2 on t1.m1 = t2.m2 where t2.m2 = 2
```

在外连接查询中，指定的where子句中包含被驱动表中列**不为null**的条件称为**空值拒绝**，这时候内外连接就可以转化，从而查询优化器可以评估不同表的不同连接顺序的成本



**子查询优化**

子查询可以在一个查询语句中的各个位置出现：

1. 出现在select中

```mysql
select (select m1 from t1 limit 1)
```

2. 在from语句中

```mysql
select m,n from(select m2 + 1 as m,n2 as n from t2 where m2 > 2) as t
```

这个from后面可以理解为产生了一个派生表

3. 在where或on子句的表达式中

```mysql
select * from t1 where m1 in (select m2 from t2);
```

4. 在order by group by字句中都没有啥意义，虽然语法支持，但是没啥意思
5. 子查询按照查询出来的结果集多少分为：标量子查询，行子查询，列子查询，表子查询



**子查询在布尔表达式中的使用**

= > <等是布尔表达式，这些后面只能跟标量子查询和行子查询

```mysql
标量
select * from t1 where m1 < (select min(m2) from t2)
行
select * from t1 where (m1,n1) = (select m2,n2 from t2 limit 1);
```



在 in any some all后面的子查询

1. in、not in

操作数 [not] in (子查询)

```mysql
select * from t1 where (m1,n1) in(select m2,n2 from t2)
```

从t1表中找到一些记录，这些记录的m1,n1列的值在后面的子查询中查找

2. any、some（这两个是一个意思）

```mysql
select * from t1 where m1 > any(select m2 from t2)
等价于
select * from t1 where m1 > (select min(m2) from t2)
```

意思是对于t1表中的m1列来说只要m1大于子查询中最小值就行了，就是m1的取值是大于子查询的最小值

3. all

```mysql
select * from t1 where m1 > all(select m2 from t2)
等价于
select * from t1 where m1 > (select max(m2) from t2)
```

意思与上面相似



**子查询注意事项**

1. 子查询必须用小括号
2. select字句中的子查询必须是标量子查询
3. 可以用limit来达到行子查询后者标量子查询
4. 对于in、any、some、all子查询来说不允许有limit



**子查询在Mysql中是怎样执行的**

对于不相关标量子查询，行子查询来说：

```mysql
select * from s1 where key1 = (select common_field from s2 where key3 = 'a' limit 1)
```

1. 单独执行后面的子查询，然后将子查询得到的结果当做外层查询的参数，这两个过程是单独执行的



对于相关标量子查询来说：

```mysql
select * from s1 where key1 = (select common_field from s2 where s1.key3 = s2.key3 limit 1)
```

1. 首先从外层也就是s1表中获取一条记录
2. 把这个记录带到子查询中，让子查询执行出结果
3. 在执行外部查询



**In子查询优化（不是很全面）**

例子：

```mysql
select * from s1 where key1 in (select common_field from s2 where key3 = 'a')
```

首先这个in后面的子查询查询出来的结果可以称为一张**物化表**其实也就是临时表，然后在用这个外层的s1表与这个物化表做**join内连接**的操作,转换为内连接以后就可以让查询优化器选择用那个表做驱动表，选择性能浪费低的那个

相当于：

```mysql
select s1.* from s1 inner join 物化表 on key1 = m_val
```



# 第15章 查询优化的百科全书——EXPLAIN详解(待细化)

先放两个图，以后在完善

**EXPLAIN语句输出中的各个列的作用**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/explain1.jpg?raw=true)

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/explain2.jpg?raw=true)



**select_type:**这个看看你的查询怎么样，效率牛逼牛逼

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/select_type.jpg?raw=true)



# 第17章 调节磁盘和CPU的矛盾——Innodb的Buffer Pool

说白了就是把页通过一次IO从磁盘加载到内存，之后不着急释放这个页占用的内存，而是把这个空间缓存起来,如果在有请求发过来的话，就访问这个缓存好的空间就行了，从而减少IO操作

**Buffer Pool**

在mysql启动的时候向操作系统申请的一块**连续的内存空间**，这个内存空间可以自定义大小，默认是128M,可以通过以下命令设置buffer poll的大小：

```mysql
innodb_buffer_poll_size = 字节数
```

buffer pool是一片连续的内存空间，也被划分若干个16kb大小的页面，为了控制这些页面，创造出了**控制块**来控制这些页面，**每一个控制块对应一个缓冲页**如图所示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/buffer%20poll.jpg?raw=true)

碎片就是不能够完整存放一个缓冲页大小的空间



**free 链表的管理**

free 链表就是把空闲的缓冲页的控制块用链表来穿起来，缓冲页需要存放数据时就从这个链表中删除即可：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/free%E9%93%BE%E8%A1%A8.jpg?raw=true)

由图可见每一个控制块对应一个缓冲页，看着是控制块的链表实际上是缓冲页的链表



**缓冲页的hash处理**

这个就是利用hash key、value来判断你这个数据页存不存在这个hash桶中

* 表空间号+页号作为hash的key
* 控制块的内存地址作为hash的value

这样就可以在hash桶中快速的定位**你这个页对应的缓冲页存不存在**，不存在的话就从free链表中抽一个出来缓冲数据



**flush链表的管理**

如果我们修改了缓冲页中的数据，这样就与磁盘中存放的数据不一样了，这样就形成了**脏页**，所以要把修改的缓冲页刷新到磁盘，但是频繁刷新回导致io次数太多很慢，所以我们要在**未来的某个时间点**进行刷新，为了找到脏页，特意为这些脏页创建了一个叫**flush**的链表：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/flush%E9%93%BE%E8%A1%A8.jpg?raw=true)

跟free其实是差不多的，只不多里面存放的是脏页，free链表是空闲的缓冲页



**LRU链表的管理**

有个问题就是bufferpool的大小毕竟是固定的，这时候在有新的数据页添加进来的话就很麻烦，为了解决这个事情其实就是把旧的页面删除，新的页面放进来，把hash命中率最高的页面留下（使用**最频繁**的页面）删除那些不常使用的页面，给新的页面留出位置

* 简单的LRU链表的管理：我们每使用一个缓冲页的时候，就把该页的控制块加载到LRU链表的头部，这样头部的就是最近并且最频繁使用的，尾部的就是会被淘汰的缓冲页
* 划分区域的LRU链表

提出这个问题是因为innodb存储引擎贴心的为我们提供了一个**预读**的功能，什么是预读呢？就是在一下两种情况的时候，只要你符合一点那innodb存储引擎就会认为你要把后面的数据也读取了，相当于一个**预判机制**，当然这也会引起很多问题

预读的分类：

* 线性预读：设置一个系统变量，当你**顺序**访问的某个区的页面超过了这个值的话，就会**异步的**读取**下一个区中**全部的页面，这个值默认是56个页面

* 随即预读：这个就是随机访问13个连续的页面机会开启预读，将**本区**中所有的页面加入到buffer pool中去，当然这个是不是默认开启的

但是预读虽然是为了你好，但是这么多的页面没准你也用不到，那么就白白的加入到了LRU链表的头部了，显然这样是不好的，还有就是在进行全表扫描的时候，一堆没有用或者用出不大的数据放在LRU链表的头部这样的选择显然也是不好的

那么可能降低Buffer Pool命中率的情况分为：

*  加载到buffer pool中的页面不一定被用到
* 有特别多低频的页面被加载到buffer poll中

为了解决预读功能以及全表扫描对buffer pool命中率产生的影响LRU被修改成下面的样子：





















































# 第18章 从猫爷借钱说起——事务简介

​		终于到了比较重要的事务了，对于类似银行这种对金钱涉及比较严格的项目来说就要避免出现一些差错，所以为了在网络世界中满足现实世界的需要，我们就要进行事务的操作了

**原子性（Atomicity）:**

​		践行要么全做，要么不做的信条，不能存在所谓的中间状态，如果在执行操作的过程中发生了错误的话，就把已经执行的操作恢复成没有执行之前的状态

**隔离性（Isolation）：**

​		说白了就是**两个事务不能相互影响**，也就是交叉运行，因为交叉运行会导致数据出现问题，要保证两个事务**串行化运行**而不是交替运行

**一致性（Consistency）：**

​		就是在现实的世界中的约束要同步在数据库的世界中，比如现实中一个人的身份证是唯一的，那么在数据库中的约束也要同步，有的不能同步的就要放在业务的代码当中进行同步，我的另外一个理解就是一致性要保证总的量是不变的，**符合所有规定的约束**，**最后的结果符合现实中的约束就行**

**持久性（Durability）：**

​		当现实世界中的状态映射到数据库中时，这个转换的结果将永久保留，不能再进行撤销 



**事务的概念：** 就是在数据库中保持上述的这四个概念的操作就是事务，一个事务对应着一个或者多个数据库的操作

**事务的状态**： 

* 活动的：说明事务正在进行
* 部分提交：就是事务的操作完成了，但是是在内存中，还没有刷新到磁盘
* 失败的：在上述的操作时，出现了不可控制因素（比如突然断电）
* 中止的：事务执行了半截变成了失败的话，那么就要进行**回滚**，撤销这个失败的事务，那么此时事务的状态就是终止的状态
* 提交的：部分提交的事务刷新到了磁盘的时候，那么我们就说这个事务是成功的事务

配图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BA%8B%E5%8A%A1%E7%8A%B6%E6%80%81.jpg?raw=true)



**事务中的语法：**

* begin、start transaction：开启一个事务
* read only：表示这是一个只读事务
* read write：这是个就能读，也能写的事务
* with consistent snapshot:开启一个一致性读事务

```mysql
begin read only;
begin read write;
```

* 提交事务用**commit**结束，在begin和commit中就可以写事务的语句了
* rollback：如果在begin后面写的事务发现出错了，那么就用rollback结尾，说明来恢复之前的数据

其实在默认情况下，我们在写sql的时候，**每一个单独的sql就是一个事务**



**隐式提交** 

* 比如你在写一个事务的时候没写commit，然后你写了类似于create table的话那么你写的事务就自动的提交了
* 或者是没写commit然后写从新又开始写了一个事务
* 或者用alter user、等mysql数据库自带的表等



**保存点**

如果你写了很多的语句，已经执行完成了，但是发现有个东西写错了那么就用roll back的话就把没有写错的也给回滚了，保存点存在的意义就是在上面打几个点比如第一个保存点后面的语句写错了，那么就回滚到第一个保存点修改

语法：

* 创建保存点：save point 保存点名称
* 回滚到保存点：rollback to 保存点名称





# 第19章 说过的话就一定要做到——redo日志（待完成）





# 第20章 后悔了怎么办——undo日志（待完成）





# 第21章 一条记录的多付面孔——事务隔离级别和MVCC

**事务隔离级别：**

​		Mysql有客户端还有服务端，那么我们就可以用多个客户端连接服务端，那么理论上服务端就可以处理多个客户端的请求，这时候就会出现**事务并发**的情况

​		如果并发执行的事务**访问的是相同的数据的话**并不是串行化执行，那么符合现实世界的约束**总的金额不变的**这个一致性要求就不满足了：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BA%8B%E5%8A%A1%E4%BA%A4%E6%9B%BF%E6%89%A7%E8%A1%8C.jpg?raw=true)

​		根本原因在于我们的事务是交替执行访问数据，我们需要让两个事务**隔离开来**而不是交替执行，让这个事务进行串行化执行，要知道**至少一个事务**对数据进行**写操作**时才会导致一致性问题，但是如果保证都是串行化来让两个事务进行完全的隔离，性能上的消耗是非常的大的，我们就需要**牺牲一部分隔离性**来提升性能上的优化



**事务并发执行时遇到的一致性问题**

* 脏写：就是一个事务修改了另一个**未提交**事务**修改过的数据**

```mysql
w1[x=1]w2[x=2]w2[y=2]c2w1[y=1]c1
```

这时就会发生**违反一致性**的事情出现，比如：我们要求x,y的值始终一样，但是比如发生了脏写：事务1修改了x的值为1没提交，事务2修改了x,y的值为2（脏写），之后事务2提交，然后事务1修改y的值为1，这样x是2，y是1这两个值就不一样

也会破坏**原子性**和**持久性**

```mysql
w1[x=2]w2[x=3]w2[y=3]c2a1
```

事务1修改x的值为2，事务2修改x的值为3,y值为3（**脏写**）事务2提交，事务1回滚，那么如果事务1回滚就要把x值修改为0，但是x的值在事务2中已经提交了，要进行**部分回滚**也就是y值保留修改，x值还原，这显然在一个事务中一个成功一个失败是不行的破坏了原子性

那么对事务2进行回滚的话，此时事务2已经提交了，因为事务1导致事务2的持久性显然是不行的



* 脏读：就是一个事务读取到了另一个**未提交**事务**修改过的数据**

```mysql
w1[x=1]r2[x=1]r2[y=0]c2w1[y=1]c1
```

事务2读了事务1修改过的数据x,y值为1,0**（脏读）**，这时候已经不一样了，之后事务2提交，事务1修改y的值为1，事务1提交，虽然最后到数据库的值是满足一致性要求的但是这个不一致的状态显然**不应该暴露给用户**

严格解释：事务1修改数据未提交，这时候事务2读取未提交事务1修改的数据，之后事务1终止回滚，事务2提交，那你事务2就读取了一个**不存在**的值



* 不可重复度：一个事务修改了另一个**未提交**事务**读取**的数据

```mysql
r1[x=0]w2[x=1]w2[y=1]c2r1[y=1]c1
```

事务1先读取x的值，然后事务2修改了x,y的值，事务2提交，事务1在读取y的值，这时候x,y的值不一致导致一致性问题

更符合常理的解释：就是事务1读取了x的值，但是事务2修改了未提交事务1读取的x的值，这时事务2提交，这时事务1在**读取x的值就会不一样**，即使两次读取的值不一样



* 幻读：就是事务1读取了符合搜索条件的记录，之后事务2添加了几条符合搜索条件的记录，之后事务1在读，那么就会出现两次不一致的情况，破坏了一致性,虽然最后的数据没有变但是这样的结果也不应该暴露给用户



**SQL标准中的4中隔离级别**

破坏一致性的严重程度大致排序是：

脏写>脏读>不可重复读>幻读，因为存在这样的排序，所以给存储引擎设立了不同的隔离级别，分别是：

* 读未提交（read uncommitted）
* 读已提交（read committed）
* 可重复度（repeatable read）
* 可串行化（serializable）

以上的这些隔离级别对应的破坏一致性的程度分别的对应关系分别是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB.jpg?raw=true)

脏写由于太逆天了，对一致性约束破坏的太严重，所以就不带他玩了



设置隔离界别的语法与作用域：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB%E8%AF%AD%E6%B3%95.jpg?raw=true)



**MVCC原理**

**版本链**：





















# 第22章 工作面试老大难——锁















