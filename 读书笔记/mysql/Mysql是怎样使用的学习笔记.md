## 关键字要点

1. order by子句必须放在limit子句的前面
2. between,not between

```sql
a between b and c
a not between b and c

```

3. is null,is not null
4. and运算符的优先级高于or，在写sql的时候，最好把or左右两遍的条件用()括起来
5. like,not like 模糊查询
6. %代表任意的字符，_代表一个字符
7. 如果要查询的语句中本身就带类似于%，_的话，那么就用\来区分、
8. 对于mysql中的运算符/和DIV都是表示除法的意思，但是DIV表示的是取商的整数部分
9. where子句中可以放置任意的表达式，在读取否条记录时，只要where子句中表达式不为0或null，那么记录就添加到结果集
10. 分组group by就是针对某个列，将该列的值相同的记录分到一个组中
11. 在查询列表如果放置了分组列，以及作用域分组列的函数以外的额外列，那么就起不到预期
12. 非分组列不应该放在查询列表中
13. 可以把where放在group by之前，进行过滤，也可以把having语句放在group by语句之后进行过滤，两者最后的结果是差不多的
14. order by语句放在group by的语句后面
15. 多个分组列：就是先根据那个列分组，然后在进行细化，在根据那个列进行分组，group by a,b 在a分组之后再根据b分组
16. 如果分组列表里面有null的话那么也会单独成为一个分组，group by后面也可以跟上一个表达式
17. 顺序：where-->group by-->having-->order by-->limit
18. AVG等函数要放在group by语句的后面，要是单独的使用会导致失败，只有一个分组



## 连接查询要点

1. 标量子查询：子查询的结果只有一个值,因此可以作为表达式等等使用
2. 列子查询就是子查询的结果有多个，而不是单一的一个结果
3. 行子查询就是子查询查出来就是一行的数据吗，包含多个列
4. 表子查询就是查询出来的数据包含多行多列
5. 两个表连接的本质就是把一个表中的记录和另一个表中改的记录**两两相互组合**
6. 在mysql中连接两个表就是在from语句后面跟多个用逗号隔开的表名就行
7. 内连接：就是驱动表中的记录在被驱动表之中找不到的话，那么就不会加入到结果集中
8. 外连接：就是驱动表中的记录在被驱动表之中找不到的话，那么也会加入到结果集中
9. 在内连接中where子句和on子句是等价的

```sql
select * from a join b
等价于
selelct * from a,b
```

10. 对于内连接或者外连接来说，其实笛卡尔积是一样的，只是把不符合条件的行过滤掉了
11. join后边必须是表原来的名字，不能直接跟别名，但是on后面可以用别名
12. 自连接：就是自己与自己进行连接



## 并集查询

1. 使用union来进行两个查询语句的合并，只要是查询的表达式数量一致就可以多个语句合并查询
2. union还可以联合从不同的表中查询出来的数据，但是注意最终的结果集中展示的是第一个查询中给定的列名
3. union默认合并的结果集做过**去重处理**，如果你想保留的话就用union All来写



## 数据的插入删除和更新

1. 插入完成的记录

```mysql
insert into 表名() values()
```

插入完整的记录的话表名后面的括号里面可以不写

2. 插入部分的记录，你想要插入那个字段就插入那个，但是你得指定的说出来，其余的字段是null
3. 批量插入的话就在后面的values里面加入多个就行了
4. 将某个查询的结果加入到表中：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/5c1b729b36403a596f606d81dffbbfa.png?raw=true)

5. insert ignore：就是避免主键后者有唯一去约束的列插入重复的值进而报错的手段，如果有的话就忽略这次插入
6. 用insert ...on duplicate key update来进行对重复的值的更新操作（作用在主键或者是唯一键上）
7. 删除数据：delect from 表名 where 表达式，不加where的话，所有的数据都会没
8. 更新数据：update 表名 set 列1=值1



## 视图

1. 视图是干啥的，就是为了方便我们复用这些查询的语句，相当于一个封装好的查询结果集
2. 语法：**create view 视图名 as 查询语句**
3. 视图也可以称之为虚拟表，可以像表那样对视图进行一些增删改查的操作
4. 在使用的层面，我们可以把视图当做一个表去使用,使用视图可以简化语句的书写
5. 视图可以套娃：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/7849296a4f337546082f4d89f22b7bf.jpg?raw=true)

6. 视图也可以进行增删改的操作



## 存储程序

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/45b410241875ade1bee839f04a088a0.jpg?raw=true)

1. 在mysql里面自定义变量：

```mysql
## 为变量赋值,记住要在a的前面加上@
set @a = 1;

## 也可以把字符串赋值给变量
set @a = '你好';

## 把一个变量赋值给一个变量
set @b = @a;

## 把一个查询的结果赋值给变量,注意这个查询的结果集必须是一行一列的
set @a = (select a from b limit 1)

## 赋值的另外一种写法：into
select a from b limit 1 into @a;

##当查询多个列的值结果集只有一行时，给这多个结果赋值
select a,b from m limit 1 into @a,@b;
```



2. 存储函数语法：注意存储函数有返回值，存储过程没有

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/22ff29c56862b441cb8fc2d8b0d5506.jpg?raw=true)

​        例子：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/ef5631fbb4e90c44433fdbc9c735b18.jpg?raw=true)

注意;会被客户端识别的情况 要用delimiter $来区分;



3. 存储过程调用：相当于调用了一个方法，这个方法里面把逻辑做了

```mysql
select avg_score('mysql是怎样运行的');
```

4. 查看存储函数：show create function 函数名
5. 删除存储函数：drop function 函数名
6. 在函数体中定义局部变量：在函数体中定义变量必须用declare关键字，注意局部变量在名不允许加@前缀,并且declare语句要放到其他的语句前面

```mysql
declare 变量名1，变量名2 [default 默认值]
```

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/105f45de85f8970e982ca8c9843beae.jpg?raw=true)

7. 在函数体中使用用户自定义变量：

```mysql
delimiter $
create function user()
return int 
begin
set @abc = 10;
return @abc;
end $
delimiter ;
```

8. 注意参数的问题，函数可以有多个参数，但是要注意参数的格式
9. if判断句语法：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/if%E8%AF%AD%E6%B3%95.jpg?raw=true)

例子：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/if%E8%AF%AD%E6%B3%95%E4%BE%8B%E5%AD%90.jpg?raw=true)

10. while循环：repeat循环和loop循环直接查阅工具书

```mysql
while 表达式 do
   语句列表
end while;   
```

例子：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%BE%AA%E7%8E%AF%E4%BE%8B%E5%AD%90.jpg?raw=true)

11. 存储过程：相比于存储函数，存储过程没有返回值

语法：

```mysql
create procedure 存储过程名称([参数列表])
begin 
     需要执行的语句
end     
```

例子：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E4%BE%8B%E5%AD%90.jpg?raw=true)

这个存储过程定义了两个参数，然后在执行语句汇总运用到了这两个参数，然后根据接受的参数把数据插入到表里面

用**call**关键字来进行存储过程的调用

```mysql
call 存储过程([参数列表])
```

查看删除存储过程：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E6%9F%A5%E7%9C%8B.jpg?raw=true)

12. 存储过程中的参数前缀：在定义的存储过程中有三个前缀的参数可以选择，分别是：in,out,inout

in参数（默认）：只能用于**读取**，对他赋值不会被调用者看到(**只能查**)

```mysql
delimiter $
create procedure p_in(
	in agr int
)
begin
	select arg;
	set arg = 123;
end $
delimiter;


set @a = 1;
CALL pin(@a); ##结果为1
select @a; ##a的值并没有因为存储过程中的赋值而改变
```

out参数：**查不了，只能赋值**

```mysql
delimiter $
create procedure p_in(
	out agr int
)
begin
	select arg;
	set arg = 123;
end $
delimiter;


set @b = 1;
CALL pin(@b); ##结果为null
select @b; ##b赋值了
```

inout：传入的必须是定义的变量，不能是直接写的值，注意与存储函数的不同，这个变量作用是机能读取，也能赋值，由于我认为这个例子举得并不好，所以就不写了

注意：使用select...into...语句将结果集中一条记录的各个列的值复制到多个变量中

存储过程和存储函数的异同点：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E5%BC%82%E5%90%8C%E7%82%B9.jpg?raw=true)

13. 游标简介：为了方便我们访问这些具有**多条记录**的结果集，mysql从而引入了游标，比如一个查询语句的结果集中有4条记录，那么游标起到标记这些记录的作用，就是将多条记录的值赋值给变量中
14. 可以根据这个游标获取它对应记录的信息，也可以移动游标
15. 游标的使用方式：创建游标、打开游标、获取游标记录、关闭游标（游标可以在存储函数，存储过程中）
16. 创建游标：创建游标的语句放在局部变量声明的后面

```mysql
declare 游标名称 cursor for 查询语句;

##在存储过程中定义游标：
create procedure cursor_demo()
begin
	declare t1_record_cursor cursor for select m1,n1 from t1;
end
```

17. 打开关闭游标：

```mysql
open 游标名称;
close 游标名称;
```

放在存储过程中：如果不显示的定义游标，那么在存储过程end结束后会自动关闭游标

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%89%93%E5%BC%80%E6%B8%B8%E6%A0%87.jpg?raw=true)

18. 获取游标记录：

```mysql
fetch 游标名 into 变量1,变量2...
```

例子：

无循环游标：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/Mysql%E6%98%AF%E6%80%8E%E6%A0%B7%E8%BF%90%E8%A1%8C%E7%9A%84%E5%9B%BE%E7%89%87/%E6%97%A0%E5%BE%AA%E7%8E%AF%E7%9A%84%E6%B8%B8%E6%A0%87.jpg?raw=true)

有循环游标：

![](C:\Users\17810\AppData\Roaming\Typora\typora-user-images\image-20220419122908641.png)

19. 对于触发器还有事件来说暂不总结











































