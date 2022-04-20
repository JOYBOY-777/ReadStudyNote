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

​		要保证下一个数据页中记录的主键值必须大于上一个页中的



​		

































































