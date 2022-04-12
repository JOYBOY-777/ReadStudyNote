## 要点

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