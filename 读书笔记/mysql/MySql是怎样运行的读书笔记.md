Mysql脑图

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



# 第15章 查询优化的百科全书——EXPLAIN详解

这一章感觉就知道explain出来的每个列的意思就行了

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

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/lru%E9%93%BE%E8%A1%A8.jpg?raw=true)

可以看到这个链表被分为了两个区域

* 一部分存储使用频率高的缓冲页（young区）
* 一部分存储使用频率低的缓冲页(old区)

我们可以根据一个参数：innodb_old_blocks_pct来确定old区占用的大致比例，并且你还可以设置这个比例，有了这个链表就可以解决上述的问题了：

* 针对于预读带来的问题：规定在磁盘中**初次被加载到buffer pool中的页面**来说直接把他放到**old区的头部**，对于预读来说，可能不进行后续访问，那么这个新的预读的页面**就放在old区域的头部**，如果不访问了那么就即将被淘汰
* 针对全表扫描的优化：首次加载的页面放到了old区的头部，如果**后续会访问到的话**，就把这个被访问的页放到**young区的头部**，但是这样有个问题，也会把访问高的页面给顶掉，为了解决这个问题又引出了一个参数：innodb_old_blocks_time，这个变量的意思是**第一次和最后一次访问时间的差**，如果重复访问时间间隔太小了**小于这个参数**（全表扫描嘛，访问的肯定是一个页面上的数据）那么就不加入young区的头部，否则**大于**这个参数的话就把这个控制块对应的缓冲页加入**young区头部**，这个参数也可以自定义设置默认是1s
* 对于LRU链表进一步优化：就是每一次访问缓冲页的时候都得把这个控制块放到新生区（young区）的头部的话那么这样反复调整链表也是一件很麻烦的事又浪费资源，所以有有了个新的规定就是当你这个缓冲页在**young区的4/1后面时**（也就是在后面的4/3）才加入到头部，前1/4都是命中率比较高的所以无所谓

整体free链表LRU链表还有flush链表这三者整体的关系是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%93%BE%E8%A1%A8%E6%B5%81%E7%A8%8B.jpg?raw=true)



**刷新脏页到磁盘**

后台**有专门负责刷新脏页的线程**会每隔一段时间定期的把在脏页刷新到磁盘

有两种刷新脏页到磁盘的方法分别是：

* 从LRU链表的冷数据（old区域）中刷新一部分页面到磁盘

因为在LRU链表中的**缓冲块记录着你的缓冲页的数据是否修改的信息**，那么在扫描这个链表的时候就可以知道哪些缓冲页是修改过的或者是动了手脚的，从而刷新到磁盘，扫描页的数量可以用**innodb_lru_scan_depth**这个参数来指定

* 从flush链表中刷新一部分页面到磁盘（定时刷新）

后台刷新线程比较慢buffer pool满了，或者系统特别繁忙时，出现后台线程刷新磁盘比较慢的情况，这时候用户线程从磁盘中加载数据页到buffer pool的空间满了的情况，那么将会有三个选择：

* 会查看LRU链表冷数据区**尾部**存不存在**没有修改过的缓冲页**，有的话就释放
* 没有的话就刷新LRU链表尾部的**一个脏页**到磁盘中
* **用户线程**来**帮忙**刷新flush链表



**多个buffer pool实例**

当你配置buffer pool太大并且是多线程的情况下会把这个buffer pool拆分成为多个小的buffer pool实例，并且都是独立的里面有自己的链表，通过**innodb_buffer_pool_instance**来修改缓冲池的数量，当innodb_buffer_pool_size的大小<1GB的时候，设置多个实例是无效的



**innodb_buffer_pool_chunk_size**

在mysql5.7.5版本之前只能在**服务启动**的时候设置缓冲池大小，但是为了方便快捷，在以后的版本中可以在服务运行的时候设置这个大小，但是会引发一个问题就是重新申请这个内存空间然后在复制之前的缓冲池，这也是非常消耗资源的，会向操作系统申请一大块连续的内存空间

为了解决这个问题，我们就要修改分为内存空间的单位——**chunk（启动时配置）**，一个chunk就是一个连续的内存地址，内存分布如图所示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/chunk.jpg?raw=true)

可以看到这个buffer pool被分别了两个实例，每个实例里面又分为了两个chunk，一个chunk就是一段连续的内存，里面有空闲的缓冲页，因为有了这个东西，我们下次在**启动时**修改buffer pool大小的时候就以这个chunk为单位申请，通过**innodb_buffer_pool_chunk_size**指定



配置buffer pool要注意的事项：

1. innodb_buffer_pool_size必须是innodb_buffer_pool_chunk_size*innodb_buffer_pool_instance的倍数（保证每个buffer pool实例的chunk大小相同）
2. 如果innodb_buffer_pool_chunk_size*innodb_buffer_pool_instance>innodb_buffer_pool_size那么innodb_buffer_pool_chunk_size的大小调整为innodb_buffer_pool_size/innodb_buffer_pool_instance的值

总结：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/bufferpool%E6%80%BB%E7%BB%93.jpg?raw=true)



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





# 第19章 说过的话就一定要做到——redo日志（数据正向还原的过程）

为了解决事务修改的buffer pool页面带来的破坏持久性的问题

得特意强调下**持久性**：对于已经提交的事务，在事务提交后即使数据库发生崩溃，事务对数据库所做的**改变**也应当**一直存在**

但是有一种情况可能会影响持久性：比如我们在buffer pool中修改了页面，然后事务进行提交，但是提交之后出现了某种故障，导致**内存之中**的数据消失了，那么就破坏了持久性，那么怎么解决呢？

* 在事务提交完成之前，把事务修改的页面刷新到磁盘，但是又会导致两个问题：

1. 如果你修改的内容比较小的话（只是修改了页中的一小个字节）那么你就需要刷新整个页面，这样显然是不好的，修改很小的部分就要付出一次IO操作
2. 可能会导致很多的随机IO，事务修改的页面可能不连续，而是随机的

因为存在这两个问题，为了解决我们不需要在事务提交之前刷新内存到磁盘，而是把**事务**的**修改记录下来**，这样就算系统崩溃了，按照这个记录在修改一下就行了，这个**事务执行过程中产生的日志**叫做**redo日志**，刷新redo日志到磁盘的效果明显比整个或者多个随机的页面的效果要来的好，具体的好处是：

1. redo占用的空间小
2. redo日志是顺序写入的，使用顺序IO



**redo日志格式**

这个日志就是记录了**事务对数据库**进行了那些**修改**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/redo%E6%97%A5%E5%BF%97.jpg?raw=true)

* type：这个redo日志的类型
* spaceID：表空间号
* page number：页号
* redo日志的具体内容



**及其简单的redo日志结构**

这种日志记录的是：在某个页面的某个偏移量处修改了几个字节的值

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/redo%E6%97%A5%E5%BF%97%E7%AE%80%E5%8D%95%E7%B1%BB%E5%9E%8B.jpg?raw=true)

大致结构：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/redo%E6%97%A5%E5%BF%97%E7%B1%BB%E5%9E%8B.jpg?raw=true)



**复杂的redo日志**

如果我们建立了索引，那么对用户数据的添加等操作就会影响B+树，那么把一条记录插入到一个页面时，修改的地方会非常的多，那么用简单的redo日志格式来记录这些修改有两种解决方案：

* 在一个页中有多少处修改的地方就记录多少redo日志，但是这样可能消耗的空间太大了
* 记录在一个页中第一个被修改的数据到最后一个被修改的数据中的数据当做一条**物理redo日志中的具体数据**

但是这两个解决方案还是太**浪费空间**了，所以又有几种新型的redo日志出现了：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%A4%8D%E6%9D%82%E7%9A%84redo%E6%97%A5%E5%BF%97.jpg?raw=true)

以插入一条紧凑型行格式记录为例的redo日志：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/redo%E5%A4%8D%E6%9D%82.jpg?raw=true)

n_uniques:在一条记录中，需要几个字段的值才能**确保记录的唯一性**，有什么用呢？插入一条记录的时候就可以根据这个字段，对前n_uniques个字段**进行排序**

* 对于聚簇索引来说：这个值为主键的列数（主键的数量）
* 对于二级索引来说：这个值为主键的列数+索引列的列数（数量）

field_len:该记录若干个字段占用存储空间的大小

offset:该记录的**前一条记录**在页面中的地址，为的是维护在数据页中的单向链表中每个记录的next_record属性，就是让上一个记录中的next_record记录指向自己

end_seg_len：**间接的**记录了一条记录的真实存储空间大小

记住：**redo日志会把事务在执行过程中对数据库所做的所有修改都记录下来**，在系统崩溃重启后把事务做的修改恢复



**以组的形式写入redo日志**

由于插入操作会涉及不同的数据页（聚簇索引，二级索引的B+树），那么我们就用**组**的形式区分开来：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/redo%E5%88%86%E7%BB%84.jpg?raw=true)

在向B+树插入的过程中可能会出现两种情况

* 数据页剩余空间充足，直接插入，然后写一条MLOG_COMP_REC_INSERT的redo日志，这样称为乐观插入
* 数据页剩余空间不充足，那就要进行**分页**操作，对多个页面进行修改，插入进去之后会产生多条redo日志，这个过程中会修改很多统计状态的信息，产生很多的redo日志，成为悲观插入

在向B+树中添加记录的过程中必须是**原子性的**，**执行原子性操作必须以组的形式**记录redo日志，要么全部执行redo日志要么不执行rodu日志，那么就根据操作可能会**产生redo日志的多少**进行分类：比如这个组中都是因为原子操作从而产生多条redo日志的，或者是产生一条原子记录的

* 原子性操作产生多条redo日志

直接在产生的一次原子操作过程中产生的多条redo日志的**最后一条redo日志**的后面加上MLOG_MULTI_REC_END的日志**结尾**，如果崩溃了，就解析到以MLOG_MULTI_REC_END结尾的redo日志就行了，如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/redo%E7%BB%93%E5%B0%BE.jpg?raw=true)

* 原子性操作产生一条redo日志

这种情况用什么来区分呢？为了节省空间，直接在type（8bit）拿出7个位表示redo日志类型，1个bit保证是不是产生单一的redo日志

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/type.jpg?raw=true)

为1说明是单一的redo日志，否则就不是

每一次插入页的过程就是一个小的事务，一下是包含关系：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/MTR.jpg?raw=true)

 

**redo日志的写入过程**

**redo log block：**

redo日志存放在哪里呢？答案为大小为512这字节的block页中，如图所示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/redo%20block.jpg?raw=true)

真正的日志文件放在log block body中占用496字节，头部和尾部存放着一些管理信息

* LOG_BLOCK_HDR_NO:每一个block的编号
* LOG_BLOCK_HDR_DATA_LEN：block中使用了多少字节，初始值为12，到512满
* LOG_BLOCK_FIRST_REC_GROUP：表示block中第一个MTR生成的redo**日志记录组**的偏移量

这个日志记录组包含了一个MTR操作生成的一个或者多个redo log

* LOG_BLOCK_CHECKPOINT_NO：表示checkpoint的序号
* LOG_BLOCK_CHECKSUM：block的校验值，用于正确性校验



**redo日志缓冲区**

跟buffer pool同理，不能把redo log直接写到磁盘中，因为很慢，所以给他创建了缓冲区，也是一块连续的内存，默认大小为16MB，通过innodb_log_buffer_size来指定，如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/log%20buffer.jpg?raw=true)

有了这个log buffer那么就可以写入了，写入的顺序是顺序写入，从前往后写，那么到底要写到那个批偏移量处呢？答案是用全局变量buf_free的如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%86%99%E5%85%A5log%20buffer.jpg?raw=true)



举个例子，一个事务可能会产生多个MTR，一个MTR又可能产生单个或者一组redo日志如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BA%A7%E7%94%9Fredo%E6%97%A5%E5%BF%97.jpg?raw=true)



明确一下，这是两个操作不同数据的独立事务，不存在影响一致性的情况，那么他们就可以**交叉**写入log buffer如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BA%A4%E5%8F%89%E5%86%99%E5%85%A5log%20buffer.jpg?raw=true)



**redo日志刷盘时期**

* log buffer空间不足，当这个空间小于百分之50的时候就会进行刷盘
* 事务提交的时候，肯定在buffer pool中的页面出现了脏页，但是buffer pool可以不立即进行刷盘，但是为了确保安全必须把事务产生的redo log刷新到磁盘
* 在buffer pool中把脏页刷新到磁盘前，会把产生这个脏页的事务生成的redo log刷新到磁盘，为了保证安全
* 后台有线程每秒一次的速度刷新
* 正常关闭服务的时候
* 做checkpoint时



**log sequence number（lsn）**

只要有事务不断的提交，那么redo日志就会不断的增多，那么这个lsn就是用来记录当前已经写入的redo日志量，初始值规定为**8704**，产生一次MTR操作就会增加这个值：

初始状态：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/lsn%E5%88%9D%E5%A7%8B.jpg?raw=true)

当发生一次MTR时，lsn就会增加：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/lsn%E5%86%99%E5%85%A5.jpg?raw=true)

当MTR比较大时，会出现以下状态：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%A4%A7MTR%E5%86%99%E5%85%A5redo%20log.jpg?raw=true)



**flushed_to_disk_lsn**

表示刷新到磁盘中redo日志的量，初始化大小为8704，怎么找到那些redo日志被刷新到磁盘的位置呢？用**buf_next_to_write**表示，如下图所示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/19-28.jpg?raw=true)

如果已经有MTR增加，但是**没有刷新redo日志到磁盘**的情况：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/19.29.jpg?raw=true)

flushed_to_disk_lsn：这个值就是表示刷新到日志的redo日志的量，只有把redo日志刷新到磁盘了这个值才能增加，**默认值是8704**，这个值也可以运用到Buffer pool的flush链表中，flush链表中的脏页按照第一次修改发生的时间顺序进行排序，按照**old_modification**代表的lsn值进行排序，多次更新的在脏页**不会**重复插入到flush链表中,但是会更新为newest_modification



**checkpoint:**

我们先要理解一件事就是redo日志文件的**文件组**，在磁盘上有很多名字为ib_logfile0...的文件，这些文件共同构成了文件组，它会**循环着使用**，是一个闭环如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6%E7%BB%84.jpg?raw=true)

这样做会使最后写入的redo日志与开头写入的redo日志**追尾**，要记住redo日志存在的价值是什么？就是可以保证事务的持久性，就算系统崩溃也可以按照redo日志进行数据的恢复，如果**redo日志对应的脏页**已经刷新到**磁盘中**这时候，redo日志就没有用了，可以被**覆盖掉**，所以得出依据：判断某些redo日志占用的磁盘空间是否可以覆盖的依据就是**对应的脏页是否已经刷新到磁盘中**

用一个全局变量**checkpoint_lsn**来表示当前系统中可以被覆盖的redo日志是多少，初始值是**8704**，如果脏页a被刷新到磁盘上，那么对应的redo日志就可以被覆盖了，我们增加checkpoint_lsn，这就叫**执行一次checkpoint**



执行一次checkpoint的步骤：

* 计算当前系统中可以被覆盖的redo日志对应的**lsn值最大**是多少，比他小的都可以被覆盖

比如一个页a刷新到了磁盘，他对应的flush链表尾结点对应的oldest_modifiction是8916，把这个值给checkpoint_lsn，**并且<redo日志对应的lsn值都可以被覆盖**

* 将checkpoint_lsn与对应的**redo日志文件组的偏移量**以及此次**checkpoint的编号**写到日志文件的管理信息中执行一次checkpoint就+1

记录完lsn之后，redo日志文件组中各个lsn的关系是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/redolsn.jpg?raw=true)

最后补充两张图：分别是在脏页没有刷新到磁盘和刷新到磁盘各个内存的变化

之前：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%B9%8B%E5%89%8D.jpg?raw=true)

之后：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%B9%8B%E5%90%8E.jpg?raw=true)



# 第20章 后悔了怎么办——undo日志（数据逆向还原的过程）

有的时候我们在一个事务执行过程中出现了某种错误，或者是发现写事物的时候有的语句写错了，可能会用到rollback语句，让数据还原到未发生改变之前的状态，比如：

* 我们在插入记录的时候，记录一下主键，那么在逆向还原的时候就可以根据主键删除
* 删除的时候，把这行记录给记录下来，这样在还原的时候添加上就行
* 更新的时候，把旧值给记录下来，这样就可以保证还原的时候把就是给更新上

所以：**为了回滚**而记录的东西称为**撤销日志undo log**



**事务Id**

事务的分配时机：如果某个事务在执行的过程中对表进行了**增删改**的操作，那么innodb就会给这个事务分配一个**事务Id**,怎么分配呢基于Mysql(5.7)？

* 对于只读事务来说：在他对用户创建的**临时表**进行层删改操作的时候才会分配一个事务Id
* 对于读写事务来说：对随便一个表进行增删改操作时会分配一个事务Id
* 不分配默认值是0



**事务Id的生成**

* 首先服务器在内存中维护一个全局变量，需要为事务分配Id的时候就分配，并且再把这个值自增1
* 这个值为256的倍数的时候就把他刷新到**系统表空间的**页号为5的页面中的Max Trx Id的属性中（8字节）
* 当服务重启的时候再把这值加载到内存，并加上256后赋值给当前系统中的全局变量

这样做的目的是：先分配事务id的事务得到较小的事务id，后分配的得到较大的



**一条聚簇索引记录在页面中的真实结构**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%81%9A%E7%B0%87%E7%B4%A2%E5%BC%95%E7%9C%9F%E5%AE%9E%E8%AE%B0%E5%BD%95.jpg?raw=true)

这个trx_id就是那个事务对这个聚簇索引记录进行了改动，那么记录的就是对应的事务id



**undo日志的格式**

* insert操作对应的undo日志：

要想回滚insert操作那么就记录主键信息就行了，内存中大致的结构是（TRX_UNDO_INSERT_REC）

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/insert%E5%AF%B9%E5%BA%94%E7%9A%84undo.jpg?raw=true)

注意：

* undo no：从0开始递增，每生成一条undo日志，这个值就增1，前提是**没有提交事务**的情况下
* 有可能一个事务中会夹杂着多张表，那么就会有多个主键，不论是一列还是多个列，每个主键的存储空间大小(len),和真实值(value)就会被记录上
* 只需要针对**聚簇索引记录**来记录一条undo日志

当我们插入两条记录时：

```mysql
insert into gun_demo(id,key1,col) values(1,'AWM','狙击枪'),(2,'m416','步枪');
```

那么因为**一个事务插入的两条记录**，肯定对应两个undo日志

第一条：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%AC%AC%E4%B8%80%E6%9D%A1undo.jpg?raw=true)

第二条：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%AC%AC%E4%BA%8C%E6%9D%A1undo.jpg?raw=true)

这个roll_pointer也十分简单就是指向**记录对应的undo日志**：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/roll%20pointer.jpg?raw=true)

可见就是一个指针，指向undo日志



**delete操作对应的undo日志：**

当我们删除一条记录的时候，其实就是把当前的行记录的标志位deleted_flag设置为1

一个正常的删除操作的流程：

* 删除事务没有进行的时候：垃圾链表还有行记录的单向链表都没有变化：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%AD%A3%E5%B8%B8%E7%8A%B6%E6%80%81.jpg?raw=true)

* 当一个删除的事务开启的时候，注意这个过程中并没有提交，只是把记录的删除标志位设置为1，但是没有加入垃圾链表中，这个过程是**半删除**状态，为了MVCC保证

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%8D%8A%E5%88%A0%E9%99%A4%E7%8A%B6%E6%80%81.jpg?raw=true)

* 当一个删除的事务提交后：会有专门的线程来删除，并且把这个记录添加到垃圾链表，并放到头结点,就是对这个记录进行**真正意义上的删除**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%88%A0%E9%99%A4%E7%8A%B6%E6%80%81.jpg?raw=true)



那么可以看到，如果事务提交了，那么就不需要回滚了，所以回滚的操作只是针对于这个**delete mark**的中间状态来说明的，那么这个undo日志(**TRX_UNDO_DEL_MARK_REC**)的模型大概是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/delete%20undo.jpg?raw=true)

这个里面提出了一个叫**版本链的东西**，比如我在**一个事务中**先插入了一行记录，然后再删除他，那么首先添加了一行记录后，对应的记录行上面就会产生对应的trx_id还有roll_pointer(注意这两个是针对于**添加操作生成的**)，关键的来了：在进行**半删除**(delete mark)**之前**会把**由于Insert产生的trx_id和roll_pointer这两个添加到这个undo日志对应的属性中**，这样做的目的是能**通过delete的undo日志找到insert的undo日志**，于是就产生了一个跟链子一样的东西：我觉得就是**上一次对该记录进行改动时产生的undo日志**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%89%88%E6%9C%AC%E9%93%BE.jpg?raw=true)

举个例子：

```mysql
insert into gun_demo(id,key1,col) values(1,'AWM','狙击枪'),(2,'m416','步枪');
delete from gun_demo where id = 1;
```

那么会生成一个delect mark对应的undo日志：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/delete%20undo%20im.jpg?raw=true)

注意的点：

* 这个删除的操作执行了那么就会出现第三条undo日志，编号为2（从0开始）
* 因为这个删除的语句占用了两个索引列分别是聚簇索引id，还有二级索引key1，分别统计他们列在**记录行的位置**，很显然主键是0，用户记录是3，然后是**占用空间的大小和真实值**，最后这部分空间的大小加上上部分的大小占用字节数的和，填写到上一部分中就是13



**update操作对应的undo日志**

在unpate的时候有两种处理方式，分别是**更新主键**还有**不更新主键**

* **不更新主键**：说明当前修改的值是主键之外的键

那么这种情况又会分为两种分别是：

1. 就地更新：就是更新后的列与更新前的列字段占用的存储空间**一样大**，这种情况就可以直接进行更新
2. 原地更新不了了，说明前后的字段大小不一致，那么就把**这个记录直接删除（阶段二）**，然后在根据更新的列插入，再次强调这个**是直接删除，不是半删除**

对于以上了两种类型的话，产生的undo日志的类型为：**TRX_UNDO_UPD_EXIST_REC**的日志

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/update%20undo.jpg?raw=true)

值的注意的是：

* 这个n_update的意思是你的一条update语句到底有多少个列受到了影响，下面的部分则是记录的更新之前的字段的位置，大小，真实值
* 如果更新的列中包含了索引列的话，那么也要把更新前的索引列信息写上面

举个例子：

```mysql
update gun_demo set key1 = 'm249',col = '机枪' where id = 2;
```

那么这个语句对应的undo日志就是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/undo%E4%BE%8B%E5%AD%90.jpg?raw=true)

注意的点：

1. 这个undo日志的rool_pointer指向的是**上一次**对这条记录**进行改动**的undo日志，想一想上次对谁改动了这条日志？不是就插入进来的吗，所以指向的是第二次insert产生的undo日志

2. 由于更新了两个索引列，所以得记录到倒数第二格上



**更新主键(比如把主键值1更新为10000)**：

这时innodb处理这种情况时会分为两步：

* 将就记录进行**delete mark**操作，就是在update所在事务提交前，对旧记录执行**半删除**操作，只是把他的删除标记为设置成1，没有真正的被别的线程删除加入到垃圾链表，等事务提交以后在进行删除操作
* 根据更新后各列的值**创建一条新记录**，并将它插入到聚簇索引中，这时就需要**重新从聚簇索引中定位**这条记录所在的位置，然后插进去

重要总结：这种情况不是产生的**TRX_UNDO_UPD_EXIST_REC**类型的日志，而是先创建一个TRX_UNDO_DEL_MARK_REC(执行删除时产生的记录)和TRX_UNDO_INSERT_REC(执行添加的操作)的undo日志，所以修改了主键就会产生两条undo日志



**通用链表结构**

主要讲的是这些undo日志会被**写到什么地方**，还有需要注意的问题，那么写入redo日志的过程中需要用到多个链表：

每个链表的节点示意图：前面两个组合指向的是前一个节点，后面两个指向后一个节点

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%93%BE%E8%A1%A8%E8%8A%82%E7%82%B9.jpg?raw=true)

链表头结点的示意图：List Length表示链表中有几个节点，下面两个指向的是**链表头结点**，后面指向的是**链表尾结点**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%93%BE%E8%A1%A8%E5%9F%BA%E8%8A%82%E7%82%B9.jpg?raw=true)

整个链表的大致示意图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%93%BE%E8%A1%A8%E6%95%B4%E4%BD%93.jpg?raw=true)



**FIL_PAGE_UNDO_LOG** 

答案来了，实际上存储到**表空间**中，表空间中有一个FIL_PAGE_UNDO_LOG的页面，大小为16kb，具体的结构如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/undo%E9%A1%B5%E9%80%9A%E7%94%A8%E7%BB%93%E6%9E%84.jpg?raw=true)

Uudo Page Header:

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/undo%E9%A1%B5header%E7%BB%93%E6%9E%84.jpg?raw=true)

属性的意思如下：

* **TRX_UNDO_PAGE_TYPE**：存储什么类型的undo日志，一般undo日志被分为两大类：

1. TRX_UNDO_INSERT:

   由于insert操作，或者是在unpate时有**更新主键**的情况下，具体类型为：TRX_UNDO_INSERT_REC

2. TRX_UNDO_UPDATE:

   由于删除更新产生的undo日志

* TRX_UNDO_PAGE_START：在当前页面中从什么位置开始存储Undo日志，就是第一条undo日志的位置
* TRX_UNDO_PAGE_FREE：就是页面中最后一条undo日志的位置
* TRX_UNDO_PAGE_NODE：代表一个**链表节点**结构



**Undo页面链表**：

实际上由于事务产生的Undo日志存放在专门的undo页中，并且**在undo页中的Header部分通过TRX_UNDO_PAGE_NODE结构连接**

**单个事务的Undo页面链表**

由于在一个事务的执行过程中可能会产生很多的undo日志，并且在一个页面中是放不下的，那么就用链表连接起来：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/undo%E9%A1%B5%E9%9D%A2%E9%93%BE%E8%A1%A8.jpg?raw=true)

由于这个undo页中只能存储一种类型的undo日志，所以我们**还要为每一种类型的页面都创建出一个链表**，并且还规定，**临时表**产生的undo日志和**用户表**产生的undo日志是分开来的，那么一共就有4个链表：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/4%E4%B8%AAundo%E6%97%A5%E5%BF%97.jpg?raw=true)

按需分配，根据你的事务来说明



**多个事务中的Undo页面链表**

如果有两个事务为1和2，事务1对**普通表**执行删除操作，对**临时表**进行添加和修改操作，事务2对普通表进行添加，修改，删除操作，那么在内存中，会有5个undo链表产生：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/5%E4%B8%AAundo%E9%93%BE%E8%A1%A8.jpg?raw=true)



**Undo**日志具体的写入过程

**每一个undo页面链表对应一个段**，称为Undo log segment，说明**这些链表的页面都是从这个段中申请的**

作为undo日志链表的第一个节点是很特殊的，他多了一个Undo log Segment Header的部分：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%AC%AC%E4%B8%80%E4%B8%AA%E8%8A%82%E7%82%B9undo.jpg?raw=true)

这个多出来的具体部分细分是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%AC%AC%E4%B8%80%E4%B8%AA%E8%8A%82%E7%82%B9%E7%BB%93%E6%9E%84.jpg?raw=true)

* TRX_UNDO_STATE:表示本页链表处于什么状态

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%93%BE%E8%A1%A8%E7%8A%B6%E6%80%81.jpg?raw=true)

* TRX_UNDO_LAST_LOG:本页链表中最后一个Undo log header位置
* TRX_UNDO_FSEG_HEADER:通过这个属性可以找到改段对应的inode entry
* TRX_UNDO_PAGE_LIST:Undo页面链表的**基节点**



**Undo Log Header：**

实际上，这个第一个页面的内容还要加点东西:就是Undo Log Header，详细示意图如下

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%BF%BD%E5%8A%A0%E9%83%A8%E5%88%86.jpg?raw=true)

这个undo log header的具体部分以及解释：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%BF%BD%E5%8A%A0%E4%BA%8C.jpg?raw=true)

解释：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%BF%BD%E5%8A%A0%E4%B8%89.jpg?raw=true)

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%BF%BD%E5%8A%A04.jpg?raw=true)

最后一个属性是：一个名为history链表的节点

总结就是在真正的写入undo日之前，第一个页面会填充三个部分，剩下的页面会填充一个部分：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%A1%AB%E5%85%85.jpg?raw=true)



**重用undo页面**

当我们执行事务，但是这个事务就修改了几条记录，那么undo页面链表只产生了少量的undo日志，当我们每次开启一个事务就要创建一个undo链表就太浪费了，那么**当我们的事务提交后**，能不能复用这个已经提交的事务的undo链表呢？其实是可以的

* 该链表只包含一个undo页面
* Undo页面使用不到1/4

针对两种不同的Undo链表重用的方式：

1. insert undo链表在提交后基本就没有用了

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/insert%E9%87%8D%E7%94%A8.jpg?raw=true)

2. undate undo不能覆盖老的部分，因为MVCC

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/unpate%E9%87%8D%E7%94%A8.jpg?raw=true)



**回滚段** 

一个事务最多可以有4个undo链表，在同一时刻不同事物拥有的undo链表是不一样的，那么为了**管理这些链表**，设计了一个叫做**Rollback Segment Header**,这个里面存放的是**各个Undo页面链表头页面页号**，这个页号叫undo slot

类比：undo页面链表是一个班，first undo page是班长undo slot是班长编号，后面的页面是同学，年级里面给班长开会就是Rollback Segment Header，那么它的结构是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/rsh%E9%A1%B5%E9%9D%A2.jpg?raw=true)

每个“会议室”页面有对应着一个**段**，这个段就是回滚段，

从上到下的解释是：

1. 回滚段中管理的所有Undo页面中页面**数量之和的最大值**
2. History链表占用的页面数量
3. History链表的基节点
4. 回滚段对应的10字节大小的Segment Header结构，可以找回这个回滚段对应的Inode Entry
5. undo slot集合


**从回滚段中申请undo页面链表**

在最初阶段，对于“会议室页”Rollback Segment Header来说，最初阶段他并**没有任何班长编号（undo slot）**，所以就是空的：FIL_NULL,但是执行语句后就会有事务发生，那么就要记录undo日志，分配undo页面链表，于是就开始找(遍历)这个班长编号，那么会出现两种情况：

* 如果发现班长编号是空的，那么就从段中申请一个页面（创建出一个班长），来领取这个编号，说明由这个事务产生的undo log就由这个班长负责了
* 如果不是空的，说明这个班长已经领取了编号，所以只能往后遍历，遇到没有没有被领取的编号

一个会议室(Rollback Segment Header)最多能容纳1024个班长编号(undo slot)如果满了，说明新事物无法获得新的undo页面链表了，就给用户端报错

当一个事务提交的时候，这个班长编号有两种命运：

1. 如果班长编号指向的undo页面符合**被重用**的标准，那么这个undo页面的类型就变成可缓存的，之后这个班长编号会被加入到一个链表中，inset类型的undo链表被加入到insert undo cached链表中，undate类型的undo链表被加入到uodate undo cached链表中
2. 不符合重用标准

* 如果编号对应的是insert类型的页面，那么这个页面的state属性被置为TRX_UNDO_TO_FREE，然后这个班长undo页面对应的段会被释放，班长编号**置为空**
* 对应的是update这个页面的state会被置为TRX_UNDO_TO_PURGE，然后把班长编号置为fil_null,然后把本次事务产生的undo日志放到History链表中，不会直接释放因为得进行MVCC



**多个回滚段**

一个回滚段太少了，一次只能容纳1024个班长，多弄几个会议室就行了，在哪里弄？在系统表空间第五号页面里弄就行了弄128个，如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%B3%BB%E7%BB%9F%E8%A1%A8%E7%A9%BA%E9%97%B4.jpg?raw=true)



**回滚段的分类**

因为有128个回滚段，那么我们就可以对这些个滚滚段进行分类了

* 第0,33-127为编号的回滚段为一类
* 1-32回滚段属于一类

为什么要这样呢？就是在修改针对普通表的回滚段中的undo页面时，需要记录对应的redo日志，而修改针对临时表的回滚段中的undo页面时，不需要记录对应的redo日志

原来记录undo日志为了保证一致性，也需要记录redo日志



**为事务分配Undo页面链表的详细过程**

举个例子：用事务对普通表的记录进行改动

1. 在事务执行过程中，对普通表的事务进行首次改动之前，得先去**认领一个会议室**，也就是**系统表空间的**第5号页面中分配一个回滚段，一旦事务被分配给了这些**“会议室”** 那么以后就不会重新分配
2. 在认领后，查看一下这个回滚段中的两个cache链表，看看不用新分配undo页面链表的情况下有没有可重用的undo页面链表，根据不同的事务操作（insert,delete）去对应的缓存链表中查找，有的话就把这个**“班长编号”**分配给这个事务
3. 如果没有可重用的undo链表，那么就去对应的**“会议室”**中看一下有没有空的没人认领的**“班长编号”**，在分配
4. 分配**班长编号（undo slot）**看一下这个编号是不是从可缓存链表中获取的，如果是说明这个undo log segment就已经被认领了，不是的话就重新分配一个undo log segment，并配置班长(first undo page)，并让这个班长编号认领班长
5. 把事务生成的undo页面写入到上面的undo页面链表中



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



* 不可重复度：一个事务修改了另一个**未提交**事务**读取**的数据，**在同一个事务的多次查询中，查询的结果不一样**

```mysql
r1[x=0]w2[x=1]w2[y=1]c2r1[y=1]c1
```

事务1先读取x的值，然后事务2修改了x,y的值，事务2提交，事务1在读取y的值，这时候x,y的值不一致导致一致性问题

更符合常理的解释：就是事务1读取了x的值，但是事务2修改了未提交事务1读取的x的值，这时事务2提交，这时事务1在**读取x的值就会不一样**，即两次读取的值不一样



* 幻读：就是事务1读取了符合搜索条件的记录，之后事务2添加了几条符合搜索条件的记录，之后事务1在读，那么就会出现两次不一致的情况，破坏了一致性,虽然最后的数据没有变但是这样的结果也不应该暴露给用户



**SQL标准中的4中隔离级别**

破坏一致性的严重程度大致排序是：

脏写>脏读>不可重复读>幻读，因为存在这样的排序，所以给存储引擎设立了不同的隔离级别，分别是：

* 读未提交（read uncommitted）
* 读已提交（read committed）
* 可重复读（repeatable read）
* 可串行化（serializable）

以上的这些隔离级别对应的破坏一致性的程度分别的对应关系分别是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB.jpg?raw=true)

脏写由于太逆天了，对一致性约束破坏的太严重，所以就不带他玩了



设置隔离界别的语法与作用域：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB%E8%AF%AD%E6%B3%95.jpg?raw=true)



**MVCC原理**

**版本链**

在说明版本链之前，先来回忆一下，在innodb作为存储引擎下的一行行记录中，有两个隐藏列就是

* trx_id:就是一个事务在对**聚簇索引记录**进行影响的时候，都会把这个事务的id赋给这个属性，就是那个事务把我搞成这样的
* roll_pointer:指向包含旧版本数据的undo日志

一条插入语句大概对应的图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/insert%E6%8F%92%E5%85%A5.jpg?raw=true)

如果有两个事务id分别为100,200的事务对一个数据进行update操作的话大致的流程图为：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/undate%E6%8F%92%E5%85%A5.jpg?raw=true)

值的注意的是：两个事务不能对同一条事务进行**更新**操作，这样就形成了**脏写**，极度破坏事务的特性，innodb使用锁来保证，保证在同一时间内一个事务进行写操作，保证串行化

这时候就要说明版本链了：他是什么呢？我们知道，每次更新记录后，都会把**旧值**放到对应的undo日志中，**如果更新的次数多了，那么所有的版本都会被roll_pointer属性连接成一个链表**，这个链表就是版本链，头结点就是当前记录的最新值，并包含当前版本对应的事务id，我们会用这个链子，来**控制并发事务访问相同记录时的行为**这种机制即使大名鼎鼎的**多版本并发控制(MVCC)**

版本链：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/undo%E7%89%88%E6%9C%AC%E9%93%BE.jpg?raw=true)

**ReadView：**

先来回顾一下几种不同的隔离级别吧

* 对于读未提交(read uncommitted)，是最低级的事务隔离级别，可以发生脏读，那么就直接读取记录的最新版本就行了
* 对于串行化(serializable)来说，什么特殊的状况都不许发生，只允许事务串行化执行，这种情况就使用加锁就可以了
* 对于读已提交(read committed)和可重复读(repeatable read)的情况来说，都必须保证读到**已经提交过**的事务修改过的记录，那么就是一个事务修改过了事务，但是没有提交，另一个事务是读取不到的

核心逻辑就是：**需要判断版本链中的那个版本是当前事务可见的**于是就设计出了这个**read view**(一致性视图)的概念，他有四个核心的属性分别是：

1. m_ids:在生成ReadView时，当前系统中活跃的**读写事务的事务id列表**，比作你手里牌，就是目前系统中活跃的事务

2. min_trx_id:在生成ReadView时，当前系统中活跃的读写事务的事务id列表中**最小的值**，你手里牌中最小的牌

3. max_trx_id:在生成ReadView时，系统应该分配给**下一个事务的事务id**，你透支未来的牌

   比如现在有1，2，3这三个事务，3提交了，那么min_trx_id就是1，max_trx_id就是4

4. creator_trx_id:生成readview的事务id，你的专属本命牌

有了这个readview之后，按照下面的步骤判断记录是否可见就行了：

把这个比作两个人出牌比大小就行了

1. 如果一个事务访问一行记录，如果这个记录的trx_id**等于**由这个事务生成的readview中的**creator_trx_id**说明**这个事务在访问他自己修改过的记录**，这个版本（当前记录）可以被当前事务访问
2. 如果被访问版本的trx_id属性值**小于**readview中的**min_trx_id**，说明**生成该版本的事务**在**当前事务生成ReadView之前已经提交**了，那么该版本可以被当前事务访问，因为当前系统中已经不存在该版本的事务了，他已经提交了，也就是生成该版本的事务在当前事务**之前发生**，并且已经不存在了（提交了），这个事务的readview拿出最小的一张牌都比当前版本的事务id大了，这不轻松拿捏，你最小的牌都比对方的大
3. 如果被访问版本的trx_id属性值**大于等于**readview中的**max_trx_id**，说明**生成该版本的事务**在**当前事务生成ReadView之后才开启**，那么这个版本就不能被当前事务访问，也就是生成该版本的事务在当前事务**之后发生**，因为这个事务的readview已经够尽力了，**把它未来下一个事务的id都拿来用，然而依旧够不到当前版本的事务**所以就知难而退了，你手里最大的一张牌都比对方出的小
4. 如果被访问版本的trx_id属性在readview的min_trx_id和max_trx_id之间，对方出的牌在你手里最小牌和透支牌中间，那么就得看你手里有没有对方的牌，这个牌就像一个炸弹，你有的话就不能访问，没有就可以访问，存在于活跃的事务就不行

如果当前的版本不能访问的话，那么就**顺着版本链找下一个版本的数据**，并且继续上述过程，看看是否可见，如果遍历到最后都不可见的话，那么就说明**当前记录对当前事务完全不可见**



**对比read committed和repeatable read生成readview的时机不同**

记住只有事务第一次真正修改数据（记录）的时候才会为这个事务分配事务id

在hero表中由事务Id为**80**的事务插入一条记录如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%8F%92%E5%85%A5%E8%AE%B0%E5%BD%95.jpg?raw=true)

* 在**读已提交**事务隔离级别下——**每次读取数据前都生成一个ReadView**

有两个事务id分别为100,200的事务正在执行：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/100%20200.jpg?raw=true)

由于反复的对number值为1的行记录做修改，那么肯定都形成了对应的版本链：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/insert%20%E7%89%88%E6%9C%AC%E9%93%BE.jpg?raw=true)

现在有另外一个事务，使用read committed隔离级别，这是一个**只读事务**，他读取事务100,200未提交的事务：

```mysql
begin;
#事务100,200没有提交
select * from hero where number = 1;#读到的是刘备，因为在read committed的事务隔离级别下发生不了脏读
```

这个select的执行过程：

1. 执行select语句时生成一个ReadView，这个ReadView里面的活跃的事务Id也就是m_ids列表的内容是：[100,200],min_trx_id:是100，max_trx_id:为201，creator_trx_id为0（因为没有对记录行进行实质性的修改，默认分配的就是0）
2. 然后开始去版本链中寻找可见的记录：最新的记录是张飞但是这个版本记录的trx_id为100，这个炸弹在我的手牌中，不能访问，然后遍历下一个版本链
3. 这个版本的trx_id关羽这个炸弹也在我的手牌中，也不可见，之后继续遍历
4. 直到下一个版本刘备的trx_id小于我最小的手牌，比我最小的牌还小，那么我就可以访问了

然后我们把事务Id为100的事务进行提交：

```mysql
#事务Id为100
begin;
update hero name = '关羽' where number = 1;
update hero name = '张飞' where number = 1;
commit;
```

然后调用事务id为200的事务更新hero表中number为1的记录，不同的事务修改相同的记录

```mysql
begin;
#更新一些其他表的记录
update hero set name = '赵云' where number = 1;
update hero set name = '诸葛亮' where number = 1;
```

这时number 1对应的版本链是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/200%E4%BA%8B%E5%8A%A1%E4%BF%AE%E6%94%B9.jpg?raw=true)

然后在开一个读事务进行读取：

```mysql
begin;
#事务100,200均未提交，select1
select * from hero where number = 1;#读到的还是刘备
#事务200未提交,select2
select * from hero where number = 1;#读到的是张飞
```

这个select2查询执行的过程是：

1. 执行这个查询的时候会简单生成一个ReadView，他之中的属性是：m_ids列表包含200，因为100提交了，min_trx_id是200，max_trx_id是201，creater_trx_id是0
2. 在版本链中查找，会发现诸葛亮和赵云的trx_id都不行，这两个炸弹都在我的手牌中，之后根据roll_pointer查找下一个版本
3. 发现张飞的trx_id小于我的最小手牌，那么他就可以访问

总结就是：**使用READ COMMITTED**隔离级别的事务在**每次查询开始时**都会生成一个独立的ReadView



* 在**可重复读**事务隔离级别下——**第一次读取数据时生成一个ReadView**

这个就比较的懒，**就在第一次执行查询语句**的时候生成一个ReadView

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%B8%80%E6%A8%A1%E4%B8%80%E6%A0%B7.jpg?raw=true)

然后用可重复读的隔离级别开始执行：

```mysql
begin;
#100 200未提交，select 1
select * from hero where number = 1;#得到的是刘备
```

这个select1的执行过程是：

1. 在这个select1执行的时候会生成一个ReadView，他的m_ids列表的内容是[100,200],min_trx_id是100，max_trx_id是201，creator_trx_id是0
2. 然后从版本链中挑选可见的记录，由于前两条的trx_id(炸弹)都在我的手牌中，那么我就访问不了
3. 只有第三条记录的trx_id为80，比我手牌最小的那种还小，我可以访问，就是刘备这个记录

之后把事务Id为200的事务进行提交：

```mysql
#事务Id为100
begin;
update hero name = '关羽' where number = 1;
update hero name = '张飞' where number = 1;
commit;
```

然后调用事务id为200的事务更新hero表中number为1的记录，不同的事务修改相同的记录

```mysql
begin;
#更新一些其他表的记录
update hero set name = '赵云' where number = 1;
update hero set name = '诸葛亮' where number = 1;
```

形成的版本链是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/200%E4%BA%8B%E5%8A%A1%E4%BF%AE%E6%94%B9.jpg?raw=true)

然后开启一个读事务进行读取：

```mysql
begin;
#事务100,200均未提交，select1
select * from hero where number = 1;#读到的还是刘备
#事务200未提交,select2
select * from hero where number = 1;#读到的还是刘备
```

select2的步骤是：

1. 因为只生成一个ReadView那么就复用之前的就行了，他的m_ids列表的内容是[100,200],min_trx_id是100，max_trx_id是201，creator_trx_id是0
2. 这样一下可以发现，前四条的这（四个trx_id）四个炸弹都在我的手牌中，那么就都不能访问
3. 就最后一个可以访问

 以上了例子就表明了一个事：**在同一事务查询下**，也可能出现数据不一致的情况，这就是在读未提交情况下出现的**不可重复读**的情况，由于**可重复读**和**读未提交**这两种隔离级别针对查询出现的**readview**时机的不同来避免不可重复情况的发生

在执行DELETE语句和更新键值的UPDATE情况下，不会吧聚簇索引对应的记录**完全删除**，而是进行**半删除**，举个例子就是，有两个事务T1,T2并发执行，隔离级别为可重复读，如果T1根据条件查询了出某些记录，T2删除，这时候T1又重新查，如果T2把他删了，那么T1就不可能查到了，这就是软删除的好处

**MVCC只是针对**普通的查询时才生效，非普通查询不生效！！

在当前系统中，如果最早生成的ReadView**不在访问undo日志**以及**打了删除标记的记录**，就可以通过**purge**操作将他们删除



# 第22章 工作面试老大难——锁

锁都是事务带来的，并且与后序的事务围绕这个锁进行周旋，每一个事务都会生成一把锁，然后进行**true或者false的修改**，表示事务获取到了锁，并指向记录，说明记录加上了锁。事务-->锁结构-->指向记录，**事务生成锁结构并指向记录，说明记录被上锁，等上一个事务提交，他的锁结构释放，别的事务锁的状态从true变为false说明事务获取到了对应的锁**

我们都知道，并发事务会产生一致性的问题，那么并发事务基本上可以分为三大类：

1. 读读并发事务，因为读取操作不会对记录有任何的影响，所以这种情况允许发生

2. 写写情况：并发的事务相继对自己的并发事务进行修改，也就是说两个事务对同一个数据源进行了改动

3. 读写或者写读情况



* 写写情况：

这种情况下，如果不采用**加锁**的形式的话，就会出现**脏写**的现象发生，这是不被允许的，那么就需要在多个事务进行并发执行的情况下，为了保障安全性，我们就需要让他们排队来执行，通过给记录加锁实现：

加锁后的结构：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BA%8B%E5%8A%A1%E5%8A%A0%E9%94%81.jpg?raw=true)

在事务T1改动这条记录之前，先看一下内存里面有没有**与这条记录关联的锁结构**，如果没有的话就生成一个，在在事务提交以后，这个对应的锁结构也会消失：

trx:表示这个锁结构**是与那个事务关联的**

is_waiting：表示当前事务**是否在等待**



加锁失败的场景，就是当一个事务T1还没有提交时，另一个事务T2也想要修改这一条记录，发现内存中**已经有了一个对应的锁结构**，这时这个t2也会产生一个对应的锁结构，只不过这个结构中的is_waiting为true表示他得乖乖的排队：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E8%8E%B7%E5%8F%96%E9%94%81%E5%A4%B1%E8%B4%A5.jpg?raw=true)

当事务T1提交了以后，这个事务T1也要干一件事就是检查还有没有与这个记录对应的锁结构，发现正好有一个，所以就把T2对应的锁结构中的is_waiting改成false，表示获取到锁成功

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/T2%E8%8E%B7%E5%8F%96%E9%94%81.jpg?raw=true)



* 读写或者写读情况：

这两种情况主要是会发生类似于脏读，不可重复读，还有幻读的情况，为了解决这两种情况，会有两种解决方案

1. 采用MVCC进行解决(**读写操作不冲突，性能更高**)

2. 读、写的操作都采用加锁的方式(**排队执行性能比较低**)

   这样做的目的是保证**强烈的一致性**，读也要加锁，**连别的事务读取现在事务修改完记录的旧版本记录都不行了**，类似于银行账户的事务，那么读写的操作都必须加上锁了，进行排队，解决了脏读，不可重复读：

   **解决脏读：在修改的时候加锁，那么另一个事务就无法读取了**

   **解决不可重复读：在读取的时候加锁，那么另一个事务就无法修改了**

在大部分的情况下，使用MVCC这种读写不冲突的形式性能更高，但是安全性有一点损耗，使用加锁的方法，安全性高，但是性能低



**一致性读**：就是用MVCC进行读取的方式，也叫快照度，一致性无锁读

**锁定读**：事务给记录加锁，别的事务以特殊select语句访问时获取这个事务加上的锁

1. 共享锁和独占锁：

Mysql给锁分为了两个类：

* 共享锁：**S锁**，在事务要读取一条记录时，要**先获取记录的S锁**
* 独占锁：**X锁**，在事务要改动一条记录的时候，要先获取记录的**X锁**

这两个锁之间的关系是：同一时刻**S锁能存在很多把，X锁只可以存在1把，存在X锁就不能存在S锁**，只有S与S锁能兼容

2. 锁定读的语句：

   * 对读取记录加S锁（**发一把锁给这个记录行**）

     如果事务执行了这个语句，那么他就**会为读取到的记录加上S锁**，这样别的事务也用类似于这种的手段读取的时候**S锁**，别的事务就可以获取到这把锁了 

     ```mysql
     select ..... lock in share mode;
     ```

   * 对读取记录加X锁（**发一把锁给这个记录行**）

     如果事务执行了这个语句，那么他就**会为读取到的记录加上X锁**，这样只要这个记录的X,S锁都不能获取，直到当前的事务提交，释放X锁

     ```mysql
     select ... for update;
     ```

     

**写操作**

写操作分为三种：

1. Delete:先定位记录在B+树中的位置，然后获取这条记录的X锁（锁定读），最后在执行**半删除**操作
2. update：
   * 主键不修改：
     1. 修改的元素后存储空间与之前大小一致：先定位位置，然后获取X锁，在原记录上修改
     2. 修改的元素后存储空间与之前大小不一致：先定位位置，然后获取X锁，把记录彻底删除，然后加入一条新记录
   * 主键修改：直接删除然后添加
3. insert：插入一条记录**受隐式锁保护**



**给表加锁**

对一个表加锁的话，也可以分为S锁或者是X锁，那么对于给表加了不同的锁来说：

* 给表加S锁

  别的事务可以获取某些记录或者表的S锁，但是任何记录或者表的X锁不能获得

* 给表加X锁

  那么无论是表的还是表中的某个字段的，别的事务都不能继续获得任何锁

把表级别的S锁和X锁做个类比吧：

1. **行级别的S锁**：一个同学就相当于一个事务，教室就相当于表中的字段，每个同学都可以去教室上自习，就相当于很多事务访问记录，那么就相当于给门口放了一把锁，很多的同学都来上自习，那么这些同学都可以获得这个S锁

2. **行级别的X锁**：如果教室进行装修的话，先换灯泡、换地板之类的，每次只能干一样，并且同学不能进去，一个修理工就相当于一个事务，进来装修的时候在门口放了一把X锁，谁来都不行，要想访问的话，只能让我完工

3. **表级别的S锁**：领导视察教学楼，但是不能影响学生上自习（行级别S锁），而且又不允许有施工的教室出现（行级别X锁），那么就会在教学楼前面加上S锁（表级别的S锁）

   当学生看到门口有S锁直接就进去学习，但是维修工看到后就进不去，等领导走了才能进去

4. **表级别的X锁**：学校要占用教学楼进行考试，那么学生还有修理工都不能进去

有个问题：我们在整体对表上表锁的时候，怎么知道表里面的字段是不是加上了锁呢？于是就想出来了两把别的锁

​	意向共享锁**IS**：当事务准备在某条记录上加S锁的时候，会在**表级别**加上IS锁

​	意向独占锁**IX**：当事务准备在某条记录上加X锁的时候，会在**表级别**加上IX锁

​	注意：这两个锁的作用是：为了后续在加表级的S,X锁的时候，判断表中是否有被加锁的记录，**避免了遍历查看有没有上锁的记录**

那么有学生上自习的话，那么就在教学楼门口加上IS锁，然后到教室门口放S锁，修理工也是如此，如果要加表级别S锁的话，要看一下门口有没有IX锁，如果要加标级别X锁的话，要看一下有没有IS和IX，兼容性如下：不过有个疑问就是加IS锁时，如果教学楼门口有IX锁，那么别说IS了表级别的S都加不了.....

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%94%81%E5%85%BC%E5%AE%B9%E6%80%A7.jpg?raw=true)



**Innodb存储引擎中的锁**

对于其他存储引擎的锁来说：比如MyIsam、memory等他们的锁都是表级锁，对于Inndob存储引擎的锁来说，他支持表级锁，还有行级锁，表级锁粒度粗，但是占用的资源比较少，行级锁占用的资源比较多，但是粒度比较少，由于表级的锁S锁,X锁比较的鸡肋，就了解一下就行了

1. **Innodb中的表级锁**：

* **表级别**的IS锁，IX锁：为了后续在加表级的S,X锁的时候，判断表中是否有被加锁的记录，避免了遍历查看有没有上锁的记录
* **表级别的AUTO-INC锁**：当我我们为某个表中的列添加AUTO_INCREMENT修饰的时候，这样就可以使一个列递增，通过下面的两个实现方式进行递增的操作
  1. 采用AUTO-INC锁：执行插入语句的时候就添加一个表级别的**AUTO-INC锁**，然后为这个属性修饰的列进行自增的操作，在事务进行插入的操作中，由于有这个锁在，那么其他的插入事务就会**阻塞**，这个锁的作用范围是**单个插入的语句**，在这个语句执行完后就释放了
  2. 采用一个轻量级锁，在生成这个自增语句的值的时候获取这个轻量级锁，生成之后就释放

2. **Innodb中的行级锁**：

   行级锁就是在一条记录行上加锁，值得注意的是：如果**记录的类型**不同的话，起到的功效也是不同的

   **行级锁的类型**：

   * **Record Lock(正经记录锁)**:就是仅仅把**一条记录锁上**，注意正经记录锁是有S型和X型区分的，就和上面说的一样，当一个事务获取了记录的S型正经记录锁后，其他事务也可以获得，反之X型正经记录锁就不行，比如在B+树中的聚簇索引上添加一个锁记录：

     ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%AD%A3%E7%BB%8F%E8%AE%B0%E5%BD%95%E9%94%81.jpg?raw=true)

   * **Gap Lock(间隙锁)**：在可重复读的隔离级别下，会很大的程度上解决**幻读**的问题，但是并不能完全的解决，怎么解决的呢？用MVCC或者是加锁，加什么锁？加间隙锁，比如在主键值为8的记录加上间隙锁后那么**前一个记录和后一个记录中间的部分是不允许添加记录的**，有了这个锁后，添加记录加不进去，就解决了幻读问题。但是这个锁**不能保护该条记录**

     ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E9%97%B4%E9%9A%99%E9%94%81.jpg?raw=true)

     如果拥有这个gap锁的事务**提交了**，那么这个gap锁就没了，关键是这个gap锁是为了防止**幻影记录的产生的**，那么有个问题就是不知道给那条记录加上间隙锁才能阻止其他事务插入number值在区间**（20，正无穷）**的新记录呢？

     这是就要用到之前数据页中的两个指向最大值，最小值的指针了也就是：Infimum，和Supremum,为了防止在区间(20,正无穷)后面加上记录，就如同下图的样子：

     ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/S%E9%97%B4%E9%9A%99.jpg?raw=true)

   * **Next-Key-Lock(next-key锁):**相当于是正经记录锁和间隙锁的合体，既能**保护这条记录**又可以**防止**前面插入记录：

     ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/nextkey%E9%94%81.jpg?raw=true)

   * **Insert Intention Lock(插入意向锁)** ：如果一个事务发现插入字段中有间隙锁等，那么这个事务就会进行等待插入，但是在这个**等待的过程**中也需要生成一个锁的结构，表明**有事务想在某个间隙中插入新记录，但是现在正在等待**，如下图所示：

     ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%8F%92%E5%85%A5%E6%84%8F%E5%90%91%E9%94%81.jpg?raw=true)

     比如事务T1获取了间隙锁，然后T2,T3想在8之前插入两条记录，显然是不行的，那么就需要进行等待然后生成锁，等T1提交后，gap锁释放，事务T2,T3的插入意向锁true变为false，说明事务获取到了间隙锁，并且事务T2,T3之间**不相互阻塞**，并且**不会阻止别的事务继续获取该记录上任何类型的锁**

   * **隐式锁**：一个事务在进行插入的时候可以不显示的加锁，由于**事务Id**存在，就相当于加了一个隐式锁，别的事务对这条记录加S锁或者X锁的时候，由于隐式锁的存在，会帮助当前事务生成一个锁结构，然后自己在生成一个锁结构，起到延时生成锁结构的作用，添加事务暂时不生成锁，如果其他事务想要获取x或者s（这两个就是与隐式锁相冲突的锁），那么才会显示生成锁结构，要不不生成



**Innodb锁的内存结构**

​	对一个记录进行加锁就是在内存中创建一个锁结构与这个记录进行关联，那么一个事务对应多条记录的时候，只要满足以下的几点就能让记录锁放到一个锁结构中：

* 在同一个事务中进行加锁操作
* 被加锁的记录在同一个页面中
* 加锁的类型是一样的
* 等待状态是一样的

如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BA%8B%E5%8A%A1%E9%94%81%E7%BB%93%E6%9E%84.jpg?raw=true)

1. 锁所在的事务信息：一个锁对应一个事务，这里放的是锁对应的事务信息，是一个指针，指向对应的事务信息

2. 索引信息：需要记录一下**加锁的记录属于哪个索引**，是一个指针

3. 表锁、行锁的信息：

   * 表级锁：这是对哪个表加的锁，以及一些其他的信息
   * 行级锁：记录了三个重要的属性：
     1. space ID:记录所在的表空间
     2. Page Number:记录所在的页号
     3. n_bits:一个页面中包含了多条记录，用不同的bit区分到底是为了那一条记录加锁，这个属性表示使用了多少bit

4. type_mode:是一个32比特的数，被分为lock_mode,lock_type,rec_lock_type三种：

   ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/type_mode.jpg?raw=true)

   * lock_mode(锁模式):占用4bit

     ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/lock_mode.jpg?raw=true)

   * lock_type(锁类型):占用5~8bit

     ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/lock_type.jpg?raw=true)

   * rec_lock_type:

     ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/rec.jpg?raw=true)

   * 其他信息：设计了各种的hash表和链表来管理系统运行过程中产生的各种锁结构
   * 一堆bit位：一个bit位映射到页内的一条记录



举个例子就是有T1和T2这两个事务想对hero表中的记录加锁，这个表中的记录存放在表空间为67，页号为3的页面上，那么就会产生以下生成锁结构的步骤：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E4%BA%8B%E5%8A%A1%E9%94%81%E7%BB%93%E6%9E%84.jpg?raw=true)

T1对记录为15添加正经记录锁

1. 事务T1进行加锁，那么锁结构中的**锁所在的事务信息就是**T1，当然这是个指针

2. 因为是对**聚簇索引**加的锁，所以索引的信息就是**PRIMARY**索引

3. 因为是行级锁，所以要记录行锁的信息：
   * Space ID：67表空间号
   * Page Number:页号为3
   * n_bits:表示使用了多少bit，由一个公式得出：n_bits = (1 + (n_recs + LOCK_PAGE_BITMAP_MARGIN) / 8)) * 8,    n_recs表示数据量 后面的那个参数是64固定值
   
4. type_mode：是由3部分组成：
   * lock_mode:这是对记录加S锁，值为LOCK_S
   * lock_type:是行级锁，值为：LOCK_REC
   * rec_lock_type：这是对记录加正经记录锁，类型为LOCK_REC_NOT_GAP，这个事务获取到了锁那么LOCK_WAIT就是0，经过计算对应的bit位的值加起来就是2+32+1024+0 =  1058，获取到锁加0，获取不到就加256
   
   那么事务T1为**number值为15**的记录加锁时，生成的锁结构是：
   
   ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/T1%20number.jpg?raw=true)



T2对number值为3.8.15的记录加X型的next_key锁，由于3,8这两条记录没有别的事务加锁，那么他们就当然可以回去到锁，也就是在内存中生成的锁结构中**is_waiting的属性为fasle(这个属性为fasle就代表事务已经获取到锁了)**，但是对于15的记录来说，他已经有S锁了，那么现在这个事务如果想在获取对应的X锁那么只能等那个事务把S锁给释放了，所以现在这个事务只能**阻塞**，也就是对应的**is_waiting属性为True**，于是就会生成两个对应的锁结构，那么他们**相同**的地方是：

1. 事务T2进行加锁，那么锁所在的事务信息就是T2

2. 然后索引信息指的就是主键信息

3. 因为是行级锁，那么要记录三个重要的信息：

   * space ID：67，表空间号
   * Page Number:页号3
   * n_bits:72这里就直接说了
   
4. type_mode:

   * lock_mode:因为是X锁所以值为LOCK_X

   * lock_type:是行级锁，值为LOCK_REC

   * rec_lock_type:具体的类型是next_key，所以就是LOCK_ORDINARY



不同的地方就是：这两种生成的Type_mode值不一样：

 1. 一个获取到了锁：type_mode = lock_x | lock_rec | lock_ordinary | Lock_wait 值为：3+32+0+0 = 35(为什么是0呢？因为已经获取到了锁)	

    ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/T2%203%208.jpg?raw=true)

 2. 一个没有获取到锁：type_mode = lock_x | lock_rec | lock_ordinary | Lock_wait 值为:3+32+0+256 = 291

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/T2%2015.jpg?raw=true)

  

**语句加锁分析：**

我们把语句大致分为四类分别是：

* 普通的select语句
* 锁定读语句
* 半一致性读
* INSERT语句



**普通的select语句**：

在不同的隔离级别下，最普通的select语句有不同的表现：

* 在**读未提交**隔离级别下是**不加锁的**直接读取版本链中的最新版本，当然了这个也是最低级的会出现很多的问题，比如：脏读，不可重复读，幻读等问题

* 在**读已提交**隔离级别下是**不加锁的**，然后他每一次读取都会生成一个对应的**ReadView**,这样只是避免了脏读，但是没有避免不可重复读，幻读现象

* 在**可重复读**隔离级别下**不加锁**，只是在第一次读取的时候生成一个**ReadView**,这样这三种一致性问题的情况都不可能发生了，但是其实这里面有个坑：
  
  在可重复读隔离级别下，innodb存储引擎会**最大程度的解决幻读**的问题，那么这个就分为了两种情况：
  
  * T1先读取，然后T2添加后提交，之后T1在读，这种情况下，innodb存储引擎会使用间隙锁,mvcc等手段尽量解决幻读的问题
  
    ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%B2%A1%E6%9C%89%E5%87%BA%E7%8E%B0%E5%B9%BB%E8%AF%BB.png?raw=true)
  
  * T1先读取，然后T2添加后提交，之后T1先修改T2提交的数据，之后T1读，这种情况下幻读就发生了
  
    ![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%B7%B2%E7%BB%8F%E5%87%BA%E7%8E%B0%E5%B9%BB%E8%AF%BB.png?raw=true)
  
  以上的例子可以说明白：在可重复读隔离级别下，可以很大程度的避免幻读，而不是完全避免幻读



**锁定读语句**

四种语句一起来讨论：

1. SELECT...LOCK IN SHARE MODE
2. SELECT... FOR UPDATE
3. UPDATE
4. DELETE

**匹配模式**：

举个例子就是：现在有一个联合索引：idx_a_b(a,b)

* 如果搜索条件是a = 1，那么对应的扫描区间就是[1,1]，如果查询优化器扫描这个区间的记录查询的话，这个模式就是**精确匹配**
* 如果搜索条件是a = 1 and b = 1，那么对应的扫描区间就是[(1,1),(1,1)]，如果查询优化器扫描这个区间的记录查询的话，这个模式就是**精确匹配**
* 如果搜索条件是a = 1 and b >= 1，那么对应的扫描区间就是[(1,1),(1,正无穷)]，如果查询优化器扫描这个区间的记录查询的话，这个模式就是**不是精确匹配**

**唯一性搜索**

如果在某个扫描区间的记录，就确定扫描区间内**最多只包含一条记录的话**，这种情况就是唯一性搜索

判定扫描区间中最多只包含一条记录的方法：

1. 匹配模式为**精确匹配**
2. 使用的索引是**主键或者唯一二级索引**
3. 如果使用唯一二级索引的话，那么搜索条件不能是索引类是null的情况，因为这种索引可以有多个Null值的存在
4. 如果索引中包含多个列，那么每个列都被用到idx_a_b(a,b) a = 1 and b = 1



**一般情况下，读取某个扫描区间中记录的过程是**

1. 快速在b+树中定位到**该扫描区间**中的**第一条记录**，并把这个记录作为当前记录

2. 为当前记录加锁

   一般情况下对于**锁定读**的语句:在读未提交，读已提交这两种隔离级别中，会为**当前记录**加上**正经记录锁**，在另外的两种隔离级别中，为当前记录加上next_key锁也就是**正经记录锁+间隙锁**，如果直接读的是聚簇索引的记录的话

3. 判断索引条件下推的条件是否成立

   **索引条件下推**：把查询中与**被使用索引有关的搜索条件下推到存储引擎中**判断，而不是**返回到server层在判断**，这样做的目的是**减少IO操作**，只适用于二级索引，适用于select语句，不适用于update、delete

4. 执行回表

   如果读取的是二级索引的话，那么执行回表，获取到聚簇索引记录之后在**聚簇索引记录**加**正经记录锁**

5. 判断边界条件是否成立

   成立的话：执行步骤6

   不成立的话：在隔离级别为前两级下，**释放**该记录上的锁，如果隔离级别是后两级的情况下，**不释放**记录上的锁，然后向server层返回一个查询完毕的信息

6. server层判断其余搜索条件是否成立

   除了索引下推的条件以外，server层还得判断**其他搜索条件**是否成立

   成立的话：将该记录发送到客户端

   不成立的话：在隔离级别为前两级下，**释放**该记录上的锁，如果隔离级别是后两级的情况下，**不释放**记录上的锁

7. 获取当前记录所在单项链表的下一条记录，将他作为新的当前记录，并**跳回步骤二**




![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/hero.jpg?raw=true)

**实例1**：下面举一个具体的例子来进行这个加锁过程的说明：

```mysql
select * from hero where number > 1 and number <= 15 and country = '魏' lock in share mode
```

由上面可知，这个语句形成的扫描区间是：(1,15]，那么我们即将展示在**前两个隔离级别中**对number 值为3聚簇索引记录的加锁过程：

1. 在这个扫描区间中找到number值为3的聚簇索引记录
2. 因为是前两种的隔离级别，那么只是加上**S型正经记录锁就行了**
3. 由于是聚簇索引记录，所以**没有索引下推的条件**
4. 不需要执行回表
5. number值是3符合这个区间的取值
6. 因为还剩下一个搜索条件，就是country = '魏' ，所以返回server层的时候记录判断这个搜索条件成不成立，很显然是不成立的，那么就**释放掉加载该记录上的锁**
7. 获取number值为3的聚簇索引的下一条记录，就是number值为8的记录



对number值为8的聚簇索引记录加锁的流程，直接从第二步开始：

2. 对这个值为8的聚簇索引记录加上S型正经记录锁

3. 没有索引下推的条件
4. 不需要执行回表
5. 这个记录满足搜索条件
6. 继续判断其他条件是否满足，发现满足，发送到**客户端**，**不释放加在该记录上的锁**
7. 获取下一条记录，就是15



对number值为15的聚簇索引记录级锁的流程

2. 给number值为15的聚簇索引加上S正经记录锁
3. 没有索引下推
4. 没有回表
5. 符合形成的搜索条件
6. server层继续判断其他搜索条件，发现符合，发送到客户端，**不释放**该记录上的锁
7. 获取下一条记录，也就是20的



对number值为20的聚簇索引记录级锁的流程

2. 在这个记录上加S正经记录锁
3. 没有索引下推
4. 没有回表
5. 20超出搜索条件，**释放该记录上的锁**，给server层返回一个查询完毕的信息
6. server层收到信息，结束查询

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-22.jpg?raw=true)



展示在**后两个隔离级别中**的加锁过程，还是number为3的记录，扫描区间是(1,15]

1. 读取在**扫描区间中**的第一条记录，也就是number值为3的记录
2. 给这条记录加上**S型的next-key**锁
3. 没有索引下推
4. 没有回表
5. 符合扫描区间
6. 在server层继续判断，发现不符合其他的搜索条件，但是由于是**后两个隔离界别**所以**不释放上面的锁**
7. 获取number值为8的记录



number值为8的记录加锁流程

2. 为这个记录加上next-key锁
3. 没有条件下推
4. 没有回表
5. 符合扫描区间
6. server层也符合其他搜索条件，发送到客户端，**不释放**该记录上的锁
7. 获取number值为15的记录



number值为15的记录加锁流程

2. 给这个15记录加上next-key锁
3. 没有条件下推
4. 没有回表
5. 符合扫描区间
6. server层也符合其他搜索条件，发送到客户端，**不释放**该记录上的锁
7. 获取下一条记录20



number值为15的记录加锁流程

2. 给这个15记录加上next-key锁
3. 没有条件下推
4. 没有回表
5. 不符合扫描区间，由于**隔离级别是后两个**，**不会释放**该记录上的锁，给server返回一个查询完毕的信息
6. server层收到信息，结束查询

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-23.jpg?raw=true)

可以看到只要涉及，无一幸免，都加了锁，并不会释放



**实例2**：

```mysql
select * from force index(idx_name) where name >'c曹操' and name <= 'x荀彧' and country !='吴' lock in share mode
```

这个force index是强制使用索引，扫描区间是**('c曹操','x荀彧'];**中的所有二级索引记录，这个查询使用到了索引下推

**在前两个隔离级别中**

1. 读取扫描区间的第一条记录就是'1刘备'的**二级索引**记录
2. 为这个二级索引记录加上S型正经记录锁
3. 索引下推的条件是：name >'c曹操' and name <= 'x荀彧'，是符合的
4. 执行回表，找到对应的聚簇索引记录为1，然后为这个**聚簇索引记录加上一个S正经记录锁**
5. 这个二级索记录符合扫描区间
6. 在server层判断其余的搜索条件，聚簇索引对应的国家不是吴符合，那么把这个记录发送到客户端，**不释放记录上的锁**
7. 获取下一条记录's孙权'



给's孙权'这个记录加锁

2. 为这个二级索引记录加上S型正经记录锁

3. 索引下推的条件是：name >'c曹操' and name <= 'x荀彧'，是符合的

4. 执行回表，找到对应的聚簇索引记录为20，然后为这个**聚簇索引记录加上一个S正经记录锁**

5. 这个二级索记录符合扫描区间

6. 在server层判断其余的搜索条件，聚簇索引对应的国家是吴不符合，**释放记录上的锁**

7. 获取下一条记录'x荀彧'



给'x荀彧'这个记录加锁

2. 为这个二级索引记录加上S型正经记录锁

3. 索引下推的条件是：name >'c曹操' and name <= 'x荀彧'，是符合的

4. 执行回表，找到对应的聚簇索引记录为15，然后为这个**聚簇索引记录加上一个S正经记录锁**

5. 这个二级索记录符合扫描区间

6. 在server层判断其余的搜索条件，聚簇索引对应的国家不是吴符合，把这个记录发送到客户端**不释放记录上的锁**

7. 获取下一条记录'z诸葛亮'



给'z诸葛亮'这个记录加锁

2. 给这个记录加上S型正经记录锁
3. 不符合索引下推条件和边界条件，跳过步骤4,5直接向server层报告查询完毕信息
4. 跳
5. 跳
6. server层接收到信息，查询结束

整体如图所示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-24.jpg?raw=true)

对于孙权来说因为是前两个隔离级别，所以孙权不满足条件(**不满足推到server层的条件**)就释放了，但是对于也同样不满足的诸葛亮(**在判断索引下推的时候就不满足**)就直接跳过了返回了查询完毕的信息，那么加在二级索引上的记录就不释放



**后两个隔离界别**加锁过程:

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-25.jpg?raw=true)

由于是后两个隔离级别，那么就算孙权不满足（**不满足推到server层的条件**）条件也不会释放，加了就是加了不释放，诸葛亮由于在判断索引下推的时候就不满足条件直接跳过了也不释放，注意**只要你的记录不符合索引下推那么就不释放锁，无论你的隔离级别是什么**



**对于update进行的加锁**

举个栗子：扫描区间是(1,15]

对于update语句来说：加锁方式是加X锁，所有被更新二级索引记录在更新之前都需要加上X型正经记录锁

```mysql
update hero set name = 'cao曹操' where number >1 and number <= 15 and country = '魏';
```

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-26.jpg?raw=true)

由于update在本质上也是在二级索引或者聚簇索引上进行查找相应的信息，之后在进行修改，本质上也是读取一个信息，由于是**前两个隔离级别**,由于诸葛亮满足number的查询条件，但是不满足后面国家的搜索条件，于是就先加锁后释放锁，对于孙权来说，number就不满足，先加锁在释放，对于两个魏国的武将来说，由于他们的二级索引记录也会被更新，所以也会加锁，注意：**先找的是number聚簇索引**，看标号就知道了



对于**后两个**隔离级别来说：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-27.jpg?raw=true)

只要读取就给加锁，不满足条件还不释放，delete情况与update情况差不多，加X锁



**特殊情况**

上面都是一般的情况，下面说明一些特殊情况

在**前两个隔离级别中**，匹配模式为**精确匹配的话**，就不会为扫描区间的**下一条记录加锁**

```mysql
select * from hero where name = 'c曹操' for undate
```

这个就是直接扫描二级索引定位到曹操，加上锁，之后获取下一条记录的时候直接就发现不合适（在存储引擎层就判断出了），然后直接向server层返回查询完毕，不给后面的这个记录加锁，然后在到server层看看合不合适是不是要释放如图所示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E7%B2%BE%E7%A1%AE%E5%8C%B9%E9%85%8D.jpg?raw=true)



在**后两个**隔离级别中：

前面都一样，但是发现后面一个不符合单点扫描的条件的时候，这次会加上一个**间隙锁**，如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-29.jpg?raw=true)



当隔离级别是**后两个**，**不是精确匹配**对于在相应的扫描区间查找，**找不到**相应的记录的话，为扫描区间的后面一条记录加**next-key**锁

```mysql
select * from hero where name > 'd' and name < '1' for update;
```

可以确定的是扫描区间为：('d','1'),但是找不到，那么只能给'1'的记录加next-key锁了，如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-31.jpg?raw=true)



当隔离级别是**后两个**，如果扫描的是聚簇索引的话，并且扫描区间是**左闭区间**的话，并且表中正好有这个闭合的值的话，那么就会给这个记录加上**正经记录锁**，然后后面的加上next-key锁

```mysql
select * from hero where number >= 8 for update;
```

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-32.jpg?raw=true)



无论哪个隔离级别的话，只要是唯一性搜索的话（就是可以确定**唯一的**一条记录）并且这个记录**不存在半删除**

的情况，那么就给这个记录加**正经记录锁**

```mysql
select * from hero where number = 8 for update
```

如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-33.jpg?raw=true)



如果对记录**从右到左**进行扫描的话，会给**匹配到的第一条记录加上间隙锁gap锁**

```mysql
select * from hero force index(idx_name) where name > 'c曹操' and name <= 'x荀彧' and country != '吴' order by name DESC for update;
```

由于有后面的限制条件：order by name DESC，所以在扫描的时候直接定位到**最右面**的那条记录，然后给他下一条记录加上一个**间隙锁gap锁**,目的是为了避免在下一条记录之前插入**名字相同**的记录：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22-34.jpg?raw=true)



**半一致性读语句**

在**前两个**隔离级别并且执行update语句的时候采用这种方式读取，就是当update读取**已经别其他事务加了X锁的记录的时候**，Innodb会将该记录的**最新版本**读取出来，然后在带入update语句中的**搜索条件**进行判定，不匹配的话不加锁，跳到下一条记录，匹配的话加锁，我理解就是减少了非必要条件的加锁，别动不动就加锁，比如：

```mysql
##T1对Number值为8的记录加X锁
select * from hero where number = 8 for update
##T2
update hero set name = 'cao曹操' where number >= 8 and number <20 and country != '魏‘;
```

存储引擎会获取number值为8的聚簇索引记录**最新提交的版本**，并返回给server层，但是该版本的值不符合匹配的条件，就不加锁了



**insert**

**主键重复**：当我们在表中插入数据的时候，如果反复的插入主键的话，innodb就会报错，如下：

```mysql
insert into hero values(20,'g关羽','蜀');
```

很显然主键为20的记录已经存在了，那么在**生成报错信息前**，会对**聚簇索引中**number值为20的记录加锁，加的是S锁：

在前两个隔离级别中：加的是S型的正经记录锁

在后两个隔离级别中：加的是S型的next-key锁



**唯一二级索引列重复**

这种情况也是给**二级索引**加上S锁，无论是那个隔离级别，加上的都是next-key锁



**死锁**

开启两个事务：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%AD%BB%E9%94%81.png?raw=true)

上面的例子说明：

* 首先T1给number1加X正经记录锁
* 然后T2给number3加X正经记录锁
* T1想给number3加X锁但是加不了，因为已经存在了
* T2想给number1加X锁但是加不了，因为已经存在了

这样就导致这两个事务都不能顺利的进行，也就是：Tl和T2都在**等待对方先释放掉**与**自己需要的锁相冲突**

**的锁**，这种情况就是死锁

怎么修改呢？其实就是用**SHOW ENGINE INNODB STATUS**命令来进行死锁问题的日志展示，然后进行：通过**优化语句**来**改变加锁顺序**或者**建立合适的索引**以**改变加锁过程**

mysql还有一个死锁的检测机制就是：当它检测到死锁发生时 会选择个**较小的事务进行回滚**，较小的事务指的是：在事务执行过程中**插入、更新或删除**的记录条数较少的事务，然后向客户端发送一条信息，当事务回滚之后那么在他上面的锁就释放了，从而不会发生死锁的现象