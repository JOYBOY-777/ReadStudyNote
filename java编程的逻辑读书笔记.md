# 脑图

### java基础

java 基础之**继承**：https://www.processon.com/view/link/6173e2b71e08537415f8672b







### java集合

ArrayList:https://www.processon.com/view/link/61376e1ce401fd1fb6afe32f

LinkedList:https://www.processon.com/view/link/6139c33d1efad40d93a5e396

ArrayDeque:https://www.processon.com/view/link/61dd6b1e1efad4259c5bbfaf

HashMap:https://www.processon.com/view/link/61519138079129511c908cd3

TreeMap:https://www.processon.com/view/link/61dd6b551efad4259c5bc015

HashSet:https://www.processon.com/view/link/61dd6b84f346fb06cb913d91

LinkedHashMap:https://www.processon.com/view/link/61dd6ba01efad4259c5bc09a







### IO流

二进制文件和字节流：https://www.processon.com/view/link/616c10057d9c08665140b91f

java文件概述：https://www.processon.com/view/link/61dd6be80e3e74415778474f







### 并发

并发基础知识：https://www.processon.com/view/link/617ab42f1efad4489404d11b

并发包的基石：https://www.processon.com/view/link/618d1c811efad41bf2c26971







### 堆与优先队列

PriorityQueue:https://www.processon.com/view/link/61dd6cd1e401fd06a8c52580

堆与优先级队列：https://www.processon.com/view/link/6162e1567d9c0866512e589e





# 笔记

## 第一章 编程基础

### 1.1 数据类型和变量

在java中有一下四大种**数据类型**：

1. 整数类型：有4种整型byte/short/int/long,这四种对应的取值范围是不同的
2. 小数类型：有两种类型ﬂoat/double，有不同的取值范围和精度
3. 字符类型：char,表示单个字符
4. 真假类型：boolean，表示真假

**变量**：为了操作数据,需要把数据存放到内存中。所谓内存在程序看来就是一块有**地址编号的连续的空间**,数据放到内存中的某个位置后,为了方便地找到和操作这个数据,需要给这个位置起一个名字，这个名字就是变量。

比如 int a:内存中分配了一块空间,这块空间存放int数据类型, a指向这块内存空间所在的位置,通过对a操作即可操作**a指向的内存空间**,那么a = 5这个操作即可将a指向的内存空间的值改变为5，那么变量的含义就是：**变量就是给数据起名字,方便找不同的数据,它的值可以变,但含义不应变。**



### 1.2.1 赋值与基本类型

**赋值：**声明变量之后,就在内存分配了一块位置，赋值就是把这块位置的内容设为一个**确定的值**。

**基本类型**：

1.整数类型：整数类型有byte、 short、int和long,分别占1、 2、4、 8个字节，他们的取值范围如下

![](https://raw.githubusercontent.com/JOYBOY-777/ReadStudyNote/main/javaimg/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B.png)

不过要注意的是在给long类型赋值的时候,**如果常量超过了int的表示范围**,要在后面加上L

比如：

``` java
long a = 3232343433L;
```



2.小数类型：小数类型有ﬂoat和double,占用的内存空间分别是4和8字节,有不同的取值范围和精度, double表示的范围更大,精度更高。他们的取值范围如下：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/double.png?raw=true)

在给double赋值的时候就直接赋值就行

```java
double d = 333.33;
```

但是由于小数类型的话默认为double类型那么在赋值float的时候就需要在后面写上f

```java
float f = 333.33f;
```

也可以把小数直接赋值给float,double

```java
float f = 33;
double d = 3333333333333L;
```



3.真假类型：直接赋值就行了

```java
boolean b = true;
b = false;
```



4.字符类型：字符类型用于表示一个字符，可以是**中文字符**，也可以是**英文字符**，char占用两个字节，赋值的时候用''括起来

```java
char c = 'A';
char z = '马 ';
```



### 1.2.2 数组类型

在java中给数据赋值的形式有三种：

```java
1. int[] arr = {1,2,3};
2. int[] arr = new int[]{1,2,3};
3. int[] arr = new int[3];      arr[0]=1; arr[1]=2; arr[2]=3;
```

第三种赋值方式，如果没有给数组赋值那么也会有**默认值**。这个默认值跟数组的类型有关

注意：不能在给定初始值的同时给定长度,这样是不被允许的

```java
int[] arr = new int[3]{1,2,3}
```



数组类型和基本类型是有明显不同的：

1. 一个基本类型变量,内存中只会有一块对应的内存空间
2. 数组有两块内存空间：一块用于存储**数组内容本身**，另一块存储**内容的位置** 

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E6%95%B0%E7%BB%84%E4%B8%8E%E5%8F%98%E9%87%8F%E7%9A%84%E5%86%85%E5%AD%98%E5%8C%BA%E5%88%AB.png?raw=true)

比如数组arr的内存地址是2000，这 个位置存储的值是**一个位置**3000 ，3000开始的位置存储的才是**实际的数据**1 , 2 , 3

为什么这样呢？例子：

```java
int[] arrA = {1,2,3};
int[] arrB = {4,5,6,7};
arrA = arrB;
```

如果arrA对应的内存空间是直接存储的数组内容,那么它将没有足够的空间去容纳arrB的所有元素。

arrA = arrB;其实是体现的如下过程

```java
arrA     {1,2,3}
      \
        \
arrB  -> {4,5,6,7}
```

所以数组变量赋值则会让变量指向一个不同的位置



### 1.3 基本运算

java中的基本类型数据的主要运算包括：

1. 算术运算：主要是日常的加减乘除
2. 比较运算：主要是日常的大小比较
3. 逻辑运算：针对布尔值进行运算



### 1.3.1 算数运算

算数运算包括：加、减、乘、除，符号为：\+ 、 - 、 * 、 /，以及取模运算符（求余）%，以及自增 (++ )和

自减 (-- )运算符

1.加、减、乘、除注意事项

```java
int a = 2147483647*2; //2147483647是int能表示的最大值   a的结果是2
```

运算时要注意结果的范围,**使用恰当的数据类型**,两个正数都可以用int表示,但**相乘的结果可能就会超出**,结果也会让人疑惑，我们应该使用long来接收a，但是但只改为 long也是不够的,因为运算还是**默认按照int类型进行**,需要将**至少一个**数据表示为long形式,即在后面加 L或l

```java
long a = 2147483647*2L;
```

整数想出的时候**不是四舍五入**，而是**直接舍去小数位**

```java
double d = 10/4;
```

如果要想让结果正确的话，也需要将其中之一的数变为小数

```java
a) double d = 10/4.0;
b) double d = 10/(double)4;
```



2.小数的计算结果也不算精确比如：

```java
float f = 0.1f*0.1f;
System.out.println(f); //实际的输出是0.010000001
```



3.自增自减 ++ --

自增/自减是对自己做加1或减1操作,但每个都有两种形式,一种是放在变量后,例如a++、 a-- ,另一 种是放在变量前,例如++a、 --a

这里需要注意：

1. 如果只是对自己操作,这两种形式也没什么差别
2.  当后面还有其他操作时，放在变量后  (a++ )是先**用原来的值**进行其他操作,然后**再对自己做修改**
3. 放在变量前 (++a )是先**对自己做修改**,再用**修改后的值**进行其他操作       可参考下表：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E8%87%AA%E5%A2%9E%E6%93%8D%E4%BD%9C.png?raw=true)



### 1.3.2  比较运算

比较运算就是计算两个值之间的关系,结果是一个**布尔类型** (boolean)的值

比较操作符有大于(>)、大于等于 (>= )、小于(<)、小于等于 (<= )、等于 (== )、不等于 (!= )，一个=表示的是赋值的操作



### 1.3.3 逻辑运算

逻辑运算根据**数据的逻辑关系**,生成一个布尔值true或者false 

逻辑运算符包括：

1. 与 (&):**两个都为true**才是true ,只要有一个是false就是false
2. 或 (|):**只要有一个为true**就是true ,都是false才是false
3. 非(!):针对一个变量, true会变成false , false会变成true
4. 异或 (^):**两个相同为false,两个不相同为true**
5. 短路与 (&&):和&类似
6. 短路或 (||):与|类似

但是&和&&,以及|和||的区别要注意一下：

```java
boolean a = true;
int b = 0;
boolean flag = a | b++>0;
```

|的话就要前后都看，尽管a的值已经是true了，但是也要看一下b，此时b为1，|后面的式子也会进行运算，而||则不同，比如

```java
boolean flag = a || b++>0;
```

则b的值还是0,因为||会“**短路**”,即在看到||前面部分就可以判定结果的情况下,忽略||后面的运算



### 1.4 条件执行

流程控制中最基本的就是条件执行,也就是说,一些操作只能在某些条件满足的情况下才执行,在  一些条件下执行某种操作,在另外一些条件下执行另外的操作。

### 1.4.1 语法和陷阱

if语句：只在条件语句为真的情况下,才执行后面的代码,为假就不执行了。

```java
if(条件语句){
代码块
}
或者
if(条件语句) 代码 ;    
```

具体来说,条件语句必须为布尔值,可以是一个**直接的布尔变量**,也可以是**变量运算后的结果**。建议所有的if后面都加上括号



if else语句：满足的时候执行某种逻辑,而 不满足的时候执行另一种逻辑

```java
if(判断条件){
代码块1
}else{
代码块2
}
```



三元运算符：三元运算符会得到一个结果,判断条件为真的时候就返回表达式1的值,否则就返回表达式2的值

```java
判断条件  ? 表达式  1 :   表达式2
int max = x > y ? x : y;
```



if/else if/else:如果有多个判断条件,而且**需要根据这些判断条件的组合**执行某些操作,容易理解成else if其实不是

```java
if(条件1){
代码块1
}else if(条件2){
代码块2
} …
else if(条件n){
代码块n
}else{
代码块n+1
}
```

条件1满足则执行代码块1,不满足则检查条件2 , …… ,最后如果没有条件满足,且有else语句,则执行else里面的代码最后的else语句不是必需的,**没有就什么都不执行**。



switch case：针对于判断的条件基于的是同一个变量,只是根据变量值的不同而有不同的分支, 如果值比较多，如根据星期几进行判断

```java
switch(表达式 ){
case 值1:
代码1; break;
case 值2:
代码2; break;
…
case 值n:
代码n; break;
default: 代码n+1
}
```

根据表达式的值执行不同的分支,具体来说,根据表达式的值找匹配的case ,找到后执行后面的代码,**碰到break时结束**,如果没有找到匹配的值则执行default后的语句。表达式值的数据 类型只能是**byte、 short、int、 char、枚举和String**  。

需要注意的是：每条case语句后面都应该跟break语句,否则会继 续执行后面case 中的代码直到碰到break语句或switch结束。否则就会发生case穿透



### 1.4.2 实现原理（理解）

程序最终都是一条条的**指令**, CPU有一个**指令指示器**,指向下一条要 执行的指令, CPU根据指示器的指示加载指令并且执行。

有一些特殊的指令,称为**跳转指令**：修改指令指示器的值,**让CPU跳到一个指定的地方执行**。

跳转指令分为两种：

1. 条件跳转：检查某个条件,满足则进行跳转
2. 无条件跳转：直接进行跳转

以if else举例：会转换为这些跳转指令

```java
1 int a=10;
2 if(a%2==0)
3 {
4    System.out.println("偶数");
5 }
6 //其他代码
    
转换到的转移指令可能是:
1 int a=10;
2 条件跳转:如果a%2==0,跳转到第4行
3 无条件跳转:跳转到第7行
4 {
5    System.out.println("偶数");
6 }
7 //其他代码
```

在单一if的情况下可能不用无条件跳 转指令,但稍微复杂一些的情况都需要。 **if、if/else、if/else if/else、三元运算符**都会转换为条件跳转和无条件跳转



关于switch的跳转：如果分支比较少,可能会转换为跳转指令。如果分支比较多,使 用条件跳转会进行很多次的比较运算,效率比较低,可能会使用一种更为高效的方式,叫**跳转表**。

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E8%B7%B3%E8%BD%AC%E8%A1%A8.png?raw=true)

关于switch为什么不能用long作为条件式类型？

因为：**跳转表值的存储空间一般为32位,容纳不下long**



### 1.5 循环

所谓循环,就是多次重复执行某些类似的操作,这个操作一般不是完全一样的操作,而是类似的操作。



### 1.5.1 循环的四种形式

在Java中,循环有4种形式,分别是**while、 do/while、for和foreach**（增强for循环）

1.while

语法：

```java
while(条件语句){
代码块
}
或者
while(条件语句) 代码块    
```

只要条件语句为真,就一直 执行后面的代码,为假就停止不做了



2.do/while

语法：

```java
do{
代码块;
}while(条件语句)
```

不管条件语句是什么,代码块都会至少执行一次,先执行代码块,然后再判断条件语句,如果成立,则继续循环,否则退出循环。也就是说,不管条件语句是什么,**代码块都会至少执行一次**。



3.for

语法:

```java
for(初始化语句 ; 循环条件; 步进操作){
循环体
}
```

除了循环条件必须返回一个boolean类型外,**其他语句没有什么要求**,但通常情况下第一条语句用于初始化,尤其是循环的索引变量,第三条语句修改循环 变量,**一般是步进,即递增或递减索引变量**,循环体是在循环中执行的语句

for循环的执行流程：

1. 执行初始化指令
2. 检查循环条件是否为true ,如果为false,则跳转到第6步
3. 循环条件为真,执行循环体
4. 执行步进操作
5. 步进操作执行完后,跳转到第2步,即继续检查循环条件
6. for循环后面的语句



在for循环中每条语句可以是空的

```java
for(;;){}
```

这是个死循环,一直在空转,和while (true) {}的效果是一样的



4.foreach

语法：

```java
int[] arr = {1,2,3,4};
for(int element : arr){
System.out.println(element);
}
```

增强for，冒号前面是循环中的每个元素,包括数据类型和变量名   称,冒号后面是要遍历的数组或集合，每次循环element都会自动更新。对于不需要使用索 引变量,只是简单遍历的情况, foreach语法上更为简洁



### 1.5.2 循环控制

在循环的时候,会以循环条件作为是否结束的依据,但有时可能会需要根据别的条件提前结束循环 或跳过一些代码，这时候就可以使用break或continue

1.break：break用于提前结束循环

```java
int[] arr = … ; //在该数组中查找元素
int toSearch = 100; //要查找的元素
int i = 0;
for(; i<arr.length; i++){
if(arr[i]==toSearch){
      break; //直接跳出循环
    }
}
if(i!=arr.length){
System.out.println("found");
}else{
System.out.println("not found");
}
```



2.continue：跳过这一轮循环,continue语句会跳过循环体中剩下的代码,然后**执行步进操作**

```java
int[] arr = …     //在该数组中查找元素
int toSearch = 2;         //要查找的元素
int count = 0;
for(int i=0; i<arr.length; i++){
if(arr[i]!=toSearch){
    continue;
 }
count++;
}
System.out.println("found count "+count);
```

如果值不等于toSearch,则跳过剩下的循环代码,**执行i++**



### 1.5.3 实现原理

循环内部也是靠条件转移和无条件转移指令实现的

```java
int[] arr = {1,2,3,4};
for(int i=0; i<arr.length; i++){
System.out.println(arr[i]);
}

对应的跳转过程可能为
1 int[] arr = {1,2,3,4};
2 int i=0;
3 条件跳转:如果i>=arr.length,跳转到第7行
4 System.out.println(arr[i]);
5 i++
6 无条件跳转,跳转到第3行
7 其他代码    
    
```

虽然循环看起来只是重复执行一些类似的操作而已,但它其实是**计算机程序解决问题的一种基本思维方式**



### 1.6 函数的用法

计算机程序使用函数这个概念来解决这个问题,即使用函数来减少重复代码和分解复杂操作

### 1.6.1 基本概念

语法：

```java
修饰符  返回值类型    函数名字(参数类型  参数名字 , …) {
              操作
         return 返回值;
}
```

主要包含：

1. 函数名字:名字是不可或缺的,表示函数的功能
2. 参数:参数有0个到多个,每个参数由参数的数据类型和参数名字组成
3. 操作:函数的具体操作代码
4. 返回值:函数可以没有返回值,如果没有返回值则类型写成void,如果有则在函数代码中必须使用return语句返回一个值,**这个值的类型需要和声明的返回值类型一致**
5. 修饰符: Java中函数有很多修饰符,分别表示不同的目的



### 1.6.2 进一步理解函数

1.参数传递

(1)数组：在函数内修改数组中的元素会修改调用者中的数组内容，因为数组有两块存储空间

(2)可变长度的参数

```java
public static int max(int min, int ... a){
      int max = min;
      for(int i=0;i<a.length;i++){
        if(max<a[i]){
         max = a[i];
     }
}
    return max;
}
public static void main(String[] args) {
System.out.println(max(0));
System.out.println(max(0,2));
System.out.println(max(0,2,4));
System.out.println(max(0,2,4,5));
}
```

可变长度参数的语法 是在数据类型后面加三个点“...”,在函数内,可变长度参数可以看作是**数组**。可变长度参数必须是**参数列表中的最后一个**,一个函数也只能有**一个**可变长度的参数



2.理解返回

1. 函数返回值类型为void时, return不是必需的,在没有return的情 况下,会执行到函数结尾自动返回
2.  return用于显式结束函数执行,返回调用方
3. return可以用于函数内的任意地方,可以在函数结尾,也可以在中间,可以在if语句内,可以在for循 环内,用于提前结束函数执行,返回调用方
4. 函数返回值类型为void也可以使用return,即“return; ”,不用带值,含义是返回调用方,只是没有返 回值而已。
5. 函数的返回值最多只能有一个



3.重复的命名

1. 函数的名字可以重复
2. 同一个类里,函数可以重名,但是参数不能完全一样
3. 同一个类中函数名相同但参数不同的现象,一般称为**函数重载**



4.调用的匹配过程

1. 参数传递实际上是给**参数赋值**
2. 调用者传递的数据需要与函数声明的参数类型是匹配的
3. 在只有一个函数的情况下,即没有重载,**只要可以进行类型转换,就会调用该函数**,在**有函数重载**的情况下,会调用**最匹配的函数**

```java
char a = 'a';
char b = 'b';
System.out.println(Math.max(a,b));
```

Java会自动将char转换为int,然后调用Math.max (int a , int b),如果Math中没有定义针对int类型的max函数呢?调用也会成功,会调用long类型的max函数。如果  long也没有呢?会调用ﬂoat型的max函数。如果ﬂoat也没有,会调用double型的。 Java编译器会自动寻找**最匹配**的。



5.递归函数

阶乘示例：

```java
public static long factorial(int n){
       if(n==0){
              return 1;
       }else{
             return n*factorial(n-1);
      }
}
```

但是在调用System.out.println(factorial(100000));的时候系统会抛出异常，java.lang.StackOverﬂowError，表示栈溢出错误

函数是计算机程序的一种重要结构, **通过函数来减少重复代码、分解复杂操作是计算机程序的一种重要思维方式。**



### 1.7 函数调用的基本原理

函数调用的实现机制跟栈有关系

### 1.7.1  栈的概念

调用函数过程中存在的问题i:

1. 参数如何传递
2. 函数如何知道返回到什么地方
3. 函数结果如何传给调用方

答案是：使用内存来存放这些数据（栈）

1. 栈是一块内存,但它的使用有特别的约定,一般是先进后出,类似于一个桶,往栈里放数据称为入  栈,最下面的称为栈底,最上面的称为栈顶,从栈顶拿出数据通常称为出栈。
2. 栈一般是从高位地址向低位 地址扩展,换句话说,栈底的内存地址是最高的,栈顶的是最低的



函数的返回值存放在一个专门的返回值存储器中，main函数的相关数据放在栈的最下面,每调用一次函数,都会将**相关函数的数据入栈**,**调用结束会出栈**



### 1.7.2 函数执行的基本原理

```java
1 public class Sum { 2
3     public static int sum(int a, int b) {
4            int c = a + b;
5            return c;
6     }
7
8     public static void main(String[] args) {
9          int d = Sum.sum(1, 2);
10        System.out.println(d);
11     }
12 }
```

当程序在**main**函数调用Sum.sum之前,栈的情况,栈中主要存放了两个变量args和d

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/sum%E4%B9%8B%E5%89%8D.png?raw=true)

在程序执行到Sum.sum的函数内部,准备返回之前,即第5行,栈的情况:

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E5%87%BD%E6%95%B0%E5%86%85%E9%83%A8.png?raw=true)

以上的过程可以简单的描述为

1. main函数调用Sum.sum之前，将main方法的参数arg和d入栈
2. 在main函数调用Sum.sum时，首先将参数1和2入栈
3. 将**调用函数结束后**要执行的**指令地址**，简单来说就是调用函数结束后，要跳转到哪个地址上去
4. 接着跳转到sum函数，为局部变量c分配一个空间
5. 在返回之前,返回值保存到了专门的返回值存储器中
6. 在调用return后,程序会跳转到栈中保存的返回地址（主程序具体的哪一行上）
7. sum函数相关的 数据会出栈

函数中的参数和函数内定义的变量,都分配在栈中,这些变量  只有在**函数被调用的时候才分配**,而且**在调用结束后就被释放了**。但这个说法主要针对**基本数据类型**



### 1.7.3  数组和对象的内存分配

数组和对象类型，它们都有两块内存,一块存放实际的内容,一块存放实际内容的地址，实际的内容（对象，或者数组的真实值等）空间一般不是分配在栈上的,而是分配在**堆中**，但存放地址的空间（对象引用，数组引用）是分配在**栈上**的

```java
public class ArrayMax {
    public static int max(int min, int[] arr) {
       int max = min;
       for(int a : arr){
          if(a>max){
            max = a;
      }
}
      return max;
}
public static void main(String[] args) {
    int[] arr = new int[]{2,3,4};
     int ret = max(0, arr);
     System.out.println(ret);
}

```

程序执行到max函数的return语句之前的时候,内存中**栈和堆**的情况

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E6%95%B0%E7%BB%84%E6%A0%88%E5%92%8C%E5%A0%86.png?raw=true)

对于数组arr ,在栈中存放的是实际内容的地址0x1000,存放地址的栈空间会随着入栈分配出栈释,但存放实际内容的堆空间不受影响



### 1.7.4 递归调用的原理

```java
public static int factorial(int n){
       if(n==0){
          return 1;
       }else{
          return n*factorial(n-1);
       }
}
public static void main(String[] args) {
      int ret = factorial(4);
      System.out.println(ret);
}
```

在factorial第一次被调用的时候, n是4,在执行到n*factorial (n-1),即4*factorial  (3)之前的时候, 栈的情况，这时只有基本的几个变量入栈

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E6%A0%881.4.png?raw=true)





这时的返回值存储器是没有值的,在调用factorial (3)后,栈的情况，

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E6%A0%881.5.png?raw=true)

栈的深度增加了,返回值存储器依然为空,就这样,每递归调用一次,栈的深度就增加一层,每次  调用都会分配对应的参数和局部变量,也都会保存调用的返回地址，这里的**factorial(4)的下一条指令地址记录的是n*f(3)执行完要跳转到f(4)的什么位置的地址** 





在调用到n等于0的时候,栈的情况：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/1.6%E6%A0%88.png?raw=true)

这个时候,终于有返回值了,我们将factorial简写为f。f (0)的返回值为1 ; f (0)返回到f (1), f (1)执行1乘f (0),结果也是1;然后返回到f (2), f (2)执行2乘f (1),结果是2;接着返回到  f (3), f (3)执行3乘f (2),结果是6;然后返回到f (4)，执行4乘f (3),然后跳转到main函数相应的代码位置，在进行后续的出栈操作

**之前的情况是先执行函数，在记录要跳转的地方，后来出栈，就变成了先跳转在执行**，以上就是递归函数的执行过程,函数代码虽然只有一份,但在执行的过程中,每调用一次,就会有 一次入栈,生成一份不同的参数、局部变量和返回地址



函数调用主要是通过栈来存储相关的数据,系统就函数调用者和 函数如何使用栈做了约定,返回值可以简单认为是通过一个专门的返回值存储器存储的，另 外,栈的空间不是无限的,一般正常调用都是没有问题的,但如果栈空间过深,系统就会抛出错误   java.lang.StackOverﬂowError ,即栈溢出。



## 第二章 理解数据背后的二进制

### 2.1  整数的二进制表示与位运算

十进制：123表示1× (10^2) +2× (10^1) +3× (10^0) 注意：最低位从10的0次方开始计算

### 2.1.1 正整数的二进制表示

跟十进制相比，二进制就是把10换成2 比如二进制111 变为十进制就是 1x(2^2)+1x(2^1)+1x(2^0) = 4+2+1 = 7

可参照下表：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E4%BA%8C%E8%BF%9B%E5%88%B6.png?raw=true)

### 2.1.2  负整数的二进制表示

十进制：十进制的负数表示就是在前面加一个负数符号- ,例如-123

二进制：二进制使用最高位表示符号位,用**1表示负数**,用**0表示正数**，整数有四种类型：byte、 short、int、long,分别占1、 2、4、 8个字节,即分别占8、 16、 32、 64位，每种类型的符号位都是**其最左边的一位**

但是负数表示**不是简单地将最高位变为1** ：比如

1. byte a=-1,如果只是将最高位变为1,二进制应该是10000001,但实际上,它应该是11111111
2. byte a=-127,如果只是将最高位变为1,二进制应该是11111111,但实际上,它却应该是 10000001

这时候另一个概念**补码表示法**就出来了，以上的叫做**源码表示法**，这两种关系为：补码表示就是在**原码表示**的基础上**取反**然后加**1 **比如：

1. -1 : 1的原码表示是00000001,取反是11111110,然后再加1,就是11111111
2. -2 : 2的原码表示是00000010,取反是11111101,然后再加1,就是11111110
3. -127 : 127的原码表示是01111111,取反是10000000,然后再加1,就是10000001

比如一个负数的二进制表示想知道他的十进制位表示：10010010，取反,变为01101101，加1结果为01101110，它的十进制值为110，那么这个数就是-110，计算机只能做加法,而补码的一个良好特性就是,对**负数的补码表示做补码运算**就可以得到其**对应正数的原码**

对于**byte**类型,正数最大表示是01111111,即127,负数最小表示(绝对值最大)是10000000 , 即-128,表示范围就是**-128 ~127** 



为什么要采取补码呢？

只有这种形式,计算机才能实现正确的加减法，计算机其实只能做加法, 1-1其实是1+ (-1)，如果用源码计算结果是不对的：

```java
1  -> 00000001
-1 -> 10000001
+ ------------------
-2 -> 10000010
```

所以减去一个数等于加上这个数的补码：

```java
1  -> 00000001
-1 -> 11111111
+ ------------------
0  -> 00000000
```



为什么整数的运算可能出现负数呢？

因为：当计算结果超出表示 范围的时候,最高位往往是1,然后就会被看作负数。比如, 127+1

```java
127   -> 01111111
1     -> 00000001
+ ------------------
-128  -> 10000000
```



### 2.1.3 十六进制

二进制写起来太长,为了简化写法,可以将**4个二进制位简化为一个0~15的数**, **10~15用字符A~F表示**,这种表示方法称为十六进制

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E5%8D%81%E5%85%AD%E8%BF%9B%E5%88%B6.png?raw=true)

比如十进制的123：十六进制直接写常量数字,在数字前面加0x即可，123用16进制表示为0x7B，即123=7×16+11

Java中,可以方便地使用**Integer和Long**的方法查看**整数的二进制和十六进制**表示

```java
System.out.println(Integer.toBinaryString(a)); //二进制
System.out.println(Integer.toHexString(a)); //十六进制
System.out.println(Long.toBinaryString(a)); //二进制
System.out.println(Long.toHexString(a)); //十六进制
```



### 2.1.4 位运算

位运算有**移位运算**和**逻辑运算**

**移位运算**：

1. 左移：操作符为<< ,向左移动,右边的低位补0,高位的就舍弃掉了,将二进制看作整数,左移1 位就相当于乘以2。
2. 无符号右移：操作符为>>> ,向右移动,右边的舍弃掉,左边补0，注意：无论是正数还是负数，**右移的时候高位都统一补0**，没有符号限制
3. 有符号右移：操作符为>> ,向右移动,右边的舍弃掉,左边补什么取决于原来**最高位**是什么,原 来是1就补1,原来是0就补0,将二进制看作整数,右移1位相当于除以2

```java
int a = 4; //100
a = a >> 2; //001,等于1
a = a << 3 //1000,变为8
```

**逻辑运算**:

1. 按位与&:两位都为1才为1
2. 按位或|:只要有一位为1,就为1
3. 按位取反~ : 1变为0 , 0变为1
4. 按位异或^:相异为真,相同为假



### 2.2 小数的二进制表示

之前会发生不精确的例子：结果是0.01但是实际上是0.010000001

```java
float f = 0.1f*0.1f;
System.out.println(f);
```



### 2.2.1 小数计算为什么会出错

1. 实际上,不是运算本身会出错,而是计算机根本就不能精确地表示很多数,比如0.1这个数
2. 计算机 是用一种二进制格式存储小数的,这个二进制格式**不能精确表示0.1**,它只能表示一个**非常接近0.1但又不 等于0.1的一个数**

在十进制表示12.345的时候，**十进制**也只能表示那些可以表述为**10的多少次方和的数**，为1×10+2×1+3×0.1+4×0.01+5×0.001，但是表示1/3的话就不能用十进制表示，十进制表示是0.333,但无论后面 保留多少位小数,都是不精确的

二进制是类似的,但**二进制只能表示那些可以表述为2的多少次方和的数**比如：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/2%E7%9A%84%E6%AC%A1%E6%96%B9%E6%95%B0.png?raw=true)

可以精确表示为**2的某次方之和的数**可以精确表示,其他数则不能精确表示，在在Java中BigDecimal ,运算更准确



### 2.2.2  二进制表示

“小数”是在数学中用 的词,在计算机中,我们一般说的是**“浮点数”** 。ﬂoat和double被称为浮点数据类型,小数运算被称为浮点运算

如果想查看浮点数的具体二进制形式,在Java中,可以使用如下代码

```java
Integer.toBinaryString(Float.floatToIntBits(value))
Long.toBinaryString(Double.doubleToLongBits(value));
```



### 2.3 char的真正含义

char用于表示一个字符,这个字符可以是中文字符, 也可以是英文字符，赋值时把常量字符用单引号括起来

1. char本质上是一个固定占用**两个字节**的**无符号正整数**
2. 这个正整数对应于Unicode编号,用于表示那个Unicode编号对应的字符
3. 注意：char只能表示Unicode编号在**65536以内的字符**,而不能表示超出范围的字符，因为Unicode编号范围在**65536以内**的占**两个**字节
4. char本质上是一个整数,所以可以进行整数能做的一些运算
5. 在进行运算时会被看作**int**,但由于 char占两个字节,运算结果不能直接赋值给char类型,需要进行强制类型转换



## 第三章 类的基础

 java中定义了8中基本的数据类型，那么其他类型的数据都用**类**这个概念表达

### 3.1 类的基本概念

类更多表示是**自定义数据类型**，在java中也有很多函数容器类比如Math,Thread...



### 3.1.1 函数容器

比如Math类：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/math.png?raw=true)

还有Arrays类：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/arrays.png?raw=true)

将类看作函数的容器,更多的是从语言实现的角度看。从概念的角度看，Math和Arrays也可以看作**自定义数据类型**，分别表示**数学和数组类型**



### 3.1.2 自定义数据类型

一个数据类型由其包含的**属性**以及该类型可以**进行的操作**组成，属性又可以分为是**类型本身**（static）具有的属性，还是一个**具体实例**具有的属性。

一个数据类型就主要由4部分组成:

1. 类型本身具有的属性,通过**类变量**体现
2. 类型本身可以进行的操作，通过**类方法**体现
3. 类型实例具有的属性，通过**实例变量**体现
4. 类型实例可以进行的操作，通过**实例方法**体现

要注意名称：

1. **类变量**和**实例变量**都叫**成员变量**，就是类的成员
2. 类变量也叫**静态变量**或**静态成员变量**
3. **类方法**和**实例方法**都叫**成员方法**
4. 类方法也叫**静态方法**



1. **类方法**：就是类具有的方法，在类加载的时候就已经初始化，在调用的时候不用创造出类的实例，直接通过**类名**.方法名就可以访问
2. **类变量**：**类型本身**具有的属性通过类变量体现,经常用于表示一个类型中的**常量**，比如：

```java
public static final double E = 2.7182818284590452354;
public static final double PI = 3.14159265358979323846;
```

ﬁnal在修饰变量的时候表示**常量**,即变量赋值后就不能再修改了

3. **实例变量和实例方法**：实例变量表示具体的**实例**所具有的**属性**,实例方法表示具体的**实例**可以进行的**操作**



### 3.1.3 定义第一个类

定义一个简单的类,表示在平面坐标轴中的一个点

```java
class Point {
     public int x;
     public int y;
     public double distance(){
          return Math.sqrt(x*x+y*y);
      }
}
```

这其中：

```java
public class Point
```

类型的名字是Point,是可以被外部公开访问的,也可以不加Public修饰，这样就表示一种包级别的可见性即：同一个包下的类可见，子类是不可见的

```java
public int x;
public int y;
```

表示两个实例变量x和y,分别表示x坐标和y坐标

```java
 public double distance(){
          return Math.sqrt(x*x+y*y);
      }
```

定义了**实例方法**distance,表示该点到坐标原点的距离

这里注意：实例方法和类方法的区别：

1. **类方法**只能访问**类变量**,**不能**访问**实例变量**,**可以**调用其他的**类方法**,**不能**调用**实例方法**，简单理解就是类方法的能力不如实例方法
2. **实例方法**既能访问**实例变量**,也能访问**类变量**,既可以调用**实例方法**,也可以调用**类方法**



### 3.1.4 使用第一个类

定义了类本身和定义了一个函数类似,本身不会做什么事情,**不会分配内存**,也**不会执行代码**，方法要执行需要被**调用**,而实例方法被调用,首先需要一个**实例**，实例也称为对象（就是new关键字后面的部分）

```java
public static void main(String[] args) {
      Point p = new Point();
      p.x = 2;
      p.y = 3;
      System.out.println(p.distance());
}
```

其实这个new实例的语句可以分为两个部分

```java
1 Point p;
2 p = new Point();
```

1. Point p声明了一个变量,这个变量叫p,是Point类型的
2. 这个变量和数组变量是类似的,都有两块内存:一块存放**实际内容**,一块存放**实际内容的位置**
3. 声明变量本身**只会分配存放位置的内存空间**,这块空间还没有指向任何实际内容,这种变量和数组变量本身不存储数据,而只是存储**实际内容的位置**,它们也都称为**引用类型**的变量

```java
p=new Point  ();
```

它干的事：

1. 分配内存,以存储新对象的数据,对象数据包括这个**对象的属性**,具体包括其实例变量x和y
2. 给实例变量**设置默认值**, int类型默认值为0

注意：与方法内定义的局部变量不同,在创建对象的时候,所有的**实例变量**都会分配一个**默认值**，**数值类型变量**的默认值是**0** , boolean是false , **char是“\u0000”**,引用类型变量都是 null 



给对象的变量赋值,语法形式是: <对象变量名>.<成员名>

调用实例方法distance,并输出结果,语法形式是: <对象变量名>.<方法名> 如下面所示：

```java
p.x = 2;
p.y = 3;
System.out.println(p.distance());
```

对实例变量和实例方法:访问都通过对象进行,通过对象来访问和操作其内部:数据是我种基本:面向对象思维



### 3.1.5 变量默认值

我们如果想修改默认值的话，可以在定义变量的同时就赋值

```java
int x = 1;
int y;
{
    y = 2;
}
```

静态变量也可以这样初始化:

```java
static int STATIC_ONE = 1;
static int STATIC_TWO;
static
{
     STATIC_TWO = 2;
}
```

在代码块或者静态代码块中给变量进行初始化



### 3.1.6 private变量

 在定义private变量的时候一般会给外界提供get set方法来进行赋值的操作

```java
class Point {
   private int x;
   private int y;
public void setX(int x) {
    this.x = x;
}
public void setY(int y) {
    this.y = y;
}
public int getX() {
    return x;
}
public int getY() {
    return y;
}
public double distance() {
    return Math.sqrt(x * x + y * y);
 }      
}    

```

this: this表示当前实例, 在语句this.x=x;中, **this.x表示实例变量x** , 而右边的x表示方法参数中的x,没有歧义的情况下,可以直接访问实例变量,在这个例子中,两个变量名都叫x ,则需要通过加上this来消 除歧义。但是在构造方法中对变量进行初始化的时候就得加上this,要不不知道具体你要实例化那个变量



### 3.1.7 构造方法

在Point类定义中增加：

```java
public Point(){
   this(0,0);
}
public Point(int x, int y){
  this.x = x;
  this.y = y;
}
```

在构造方法中要注意的地方：

1. 名称是固定的,与类名相同。这也容易理解,靠这个用户和Java系统就都能容易地知道哪些是构 造方法
2. 没有返回值,也不能有返回值。构造方法隐含的返回值就是实例本身
3. 与普通方法一样,构造方法也可以重载
4. this (0 , 0)的意思是调用第二个构造方法,并传递参数“0 , 0”

可以这样使用构造方法：

```java
Point p = new Point(2,3)
```



关于默认构造方法和私有构造方法：

**默认构造方法**：

1. 每个类都至少要有一个构造方法,在通过new创建对象的过程中会被调用。但构造方法如果没什么操作要做,**可以省略**
2. Java编译器会自动生成一个默认构造方法,也没有具体操作
3. 一旦定义了构造方法, Java就**不会**再自动生成默认的



**私有构造方法**

1. 不能创建类的实例,类只能被静态访问,如Math和Arrays类,它们的构造方法就是私有的
2. 能创建类的实例,但只能被类的**静态方法**调用
3. 有一种常见的场景:类的对象有但是只能有一个,即单例(单个实例)。在这种场景中,对象是通过静态方法获取的,而静态方法调用**私有构造方法**创建一个对象,如果对象已经创建过了,就**重用**这个对象



### 3.1.8 类和对象的生命周期（important）

当第一次通过**new创建一个类的对象**时,或者**直接通过类名访问类变量和类方法**时

1. Java会将类加载进内存,为这个类分配一块空间
2. 这个空间会包括**类的定义**、它的**变量**和**方法信息**, 同时还有类的**静态变量**,并对静态变量**赋初始值**
3. 类加载进内存后,一般不会释放,直到程序结束一般情况下,类只会加载一次,所以**静态变量**在 内存中**只有一份**
4. 通过new创建一个对象的时候,对象产生,在内存中,会存储这个对象的**实例变量值**,每做new操作一次,就会产生**一个对象**,就会有一份独立的**实例变量**
5. 每个对象除了保存**实例变量**的值外,可以理解为还保存着对应类型即**类的地址**,通过对象能知道它的类,访问到类的变量和方法代码
6. 对象的释放是被Java用垃圾回收机制管理的,大部分情况下,我们不用太操心,当对象不再被使用的时候会被**自动释放**
7. 对象和数组一样,有两块内存,保存地址的部分**分配在栈中**,而保存实际内容的部分**分配在堆中**
8. 栈中的内存是自动管理的,函数调用入栈就会分配,而出栈就会释放
9. 堆中的内存是被垃圾回收机制管理的,当没有**活跃变量**指向对象的时候,对应的堆空间就可能被释 放,具体释放时间是Java虚拟机自己决定的
10. 活跃变量就是**已加载的类**的类变量,以及**栈中所有的变量**



**小结**：

1. public : 可以修饰类、类方法、类变量、实例变量、实例方法、构造方法,表示可被外部访问
2. private : 可以修饰类、类方法、类变量、实例变量、实例方法、构造方法,表示不可以被外部访 问,只能在类内部被使用
3.  static : 修饰类变量和类方法,它也可以修饰内部类
4. this : 表示当前实例,可以用于调用其他构造方法,访问实例变量,访问实例方法
5. ﬁnal : 修饰类变量、实例变量,表示只能被赋值一次,也可以修饰实例方法和局部变量



### 3.2 类的组合

将一些现实概念和问题通过类以及类的组合来表示和处理



### 3.2.1 String和Date

String是JavaAPI中的一个类,表示多个字符,即一段文本或字符串,它内部是一个**char的数组**,提供 了若干方法用于操作字符串。

String可以用一个字符串常量初始化：比如

```java
String name = "老马说编程";
```

Date也是Java API中的一个类,表示日期和时间,它内部是一个long类型的值,用于操作日期和时间

用无参的构造方法新建一个Date对象,这个对象就表示当前时间：

```java
Date now = new Date();
```



### 3.2.2 用类描述图形类，血缘关系以及内存中相应的类图

Point类：计算到另一个点的距离

```java
public double distance(Point p){
   return Math.sqrt(Math.pow(x-p.getX(), 2)+Math.pow(y-p.getY(), 2));
}
```

两点成一线：所以用Line表示线的类，一个类有两个点组成

```java
public class Line {
    private Point start;
    private Point end;
    public Line(Point start, Point end){
        this.start= start;
        this.end = end;
}
public double length(){
    return start.distance(end);
  }
}
```

这里就用到了组合的思想，把start引用组合到Line中，在构造方法中初始化

对Line的使用：

```java
public static void main(String[] args) {
    Point start = new Point(2,3);
    Point end = new Point(3,4);
    Line line = new Line(start, end);
    System.out.println(line.length());
}
```

内存布局：line的两个实例成员都是引用类型,引用实际的point

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E5%9B%BE%E5%BD%A2%E7%B1%BB%E5%86%85%E5%AD%98.png?raw=true)

start、end、line三个引用型变量分配在栈中,保存的是实际内容的地址,实际内容保存在堆中, line的两个实例变量**line.start和line.end还是引用**,同样保存的是**实际内容的地址**。



person类：

```java
public class Person {
    //姓名
    private String name;
    //父亲
    private Person father;
    //母亲
    private Person mother;
    //孩子数组
    private Person[] children;
    public Person(String name) {
    this.name = name;
  }
}
```

使用：

```java
public static void main(String[] args){
   Person laoma = new Person("老马");
   Person xiaoma = new Person("小马");
   xiaoma.setFather(laoma);
   laoma.setChildren(new Person[]{xiaoma});
   System.out.println(xiaoma.getFather().getName());
}
```

Person类对象的内存布局：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/fa.png?raw=true)

只要类里面有引用的话，那么在堆上其实存放的还是地址



### 3.3 代码的组织

包、 jar包、程序的编译与链接到底有什么关系



### 3.3.1 包的概念

带完整包名的类名称为其完全限定名:String类 的完全限定名为java.lang.String

**通过包使用类**

同一个包下的类之间互相引用是不需要包名的,可以直接使用,但如果类不在同一个包内,则必须要知道其所在的包,那么有两种方式：

1. 通过类的完全限定名
2. 将用到的类引入当前类
3. java.lang包下的类可以直接使用,不需要引入,也不需要使用完全限定名,比如String    类、  System类,其他包内的类则不行

使用Arrays类中的sort方法,通过**完全限定名**：

```java
int[] arr = new int[]{1,4,2,3};
java.util.Arrays.sort(arr);
System.out.println(java.util.Arrays.toString(arr));
```

使用import:

```java
package shuo.laoma;
import java.util.Arrays;

public class Hello {
public static void main(String[] args) {
   int[] arr = new int[]{1,4,2,3};
   Arrays.sort(arr);
   System.out.println(Arrays.toString(arr));
  }
}
```



有一种特殊类型的导入,称为静态导入,它有一个static关键字,可以直接导入类的公开静态方法和成员:

```java
import static java.lang.System.out; //导入静态变量out
```



**包范围可见性**: private<默认(包) <protected<public



### 3.3.2 jar 包

为方便使用第三方代码,也为了方便我们写的代码给其他人使用,各种程序语言大多有打包的概念，但是打包的一般不是源代码,而是**编译后**的代码，打包将多个编译后的文件**打包为一个文件**,方便其他程 序调用

在Java中,编译后的一个或多个包的Java class文件可以打包为一个文件, Java中打包命令为jar,打包 后的文件扩展名为.jar,一般称之为jar包

可以使用如下方式打包：

```java
jar -cvf <包名>.jar <最上层包名>
```

如果Hello.class位于E: \bin\shuo\laoma\Hello.class,则可以到目录E: \bin下,然后运行:

```java
jar -cvf hello.jar shuo
```



### 3.3.3 程序的编译与链接

从Java源代码到运行的程序,有编译和链接两个步骤:

1. 编译是将源代码文件变成扩展名是.class的一种 字节码,这个工作一般是由javac命令完成的
2.  .class文件不能直接运行,运 行的是Java虚拟机,它执行的就是Java命令,这个命令解析.class文件,转换为机器能识别的二进制代码,然后运行
3. 链接是在运行时动态执行的，是根据引用到的类加载相应的字节码并执行



## 第四章 类的继承

​        计算机程序经常使用类之间的继承关系来表示对象之间的分类关系。在继承关系中,有父类和子 类,比如动物类Animal和狗类Dog , Animal是父类, Dog是子类。父类也叫基类,子类也叫派生类。父类、子类是相对的,一个类B可能是类A的子类,但又是类C的父类

继承的一些特点：

1. 子类继承了父类的属性和行为,父类有的属性和行为**子类都有**
2. 子类可以**增加**子类特有的属性和行为,某些父类有的行为,子类的实现方式可能与父类也不完全一样
3. 使用继承一方面可以复用代码,公共的属性和行为可以放到父类中,而子类只需要关注子类特有的就可以了
4. 不同子类的对象可以更为方便地被统一处理(多态)



### 4.1 基本概念

在Java中,所有类都有一个父类**Object**



### 4.1.1 根父类Object

Object中的方法：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/object.png?raw=true)



### 4.1.2 方法重写

我们可以重写Object类中的toString()方法来进行自定义的输出：

```java
@Override
public String toString() {
  return "("+x+","+y+")";
}
```

这样的话在调用toString()的时候就变成了自定义的输出



###  4.1.3 图形类继承体系

此书中运用了四个类来表示图形的继承关系：

1. 父类Shape,表示图形
2. 类Circle,表示圆
3. 类Line,表示直线
4. 类ArrowLine,表示带箭头的直线

并通过**extends**关键字来表示他们之间的父子关系

说明：

1. Java使用extends关键字表示继承关系,一个类最多只能有**一个**父类
2. 子类不能直接访问父类的私有属性和方法
3. 除了私有的外,子类继承了父类的其他属性和方法
4. 在new的过程中,父类的**构造方法**也会执行,且会优先于**子类**执行，就是父类比子类先初始化
5. super用于指代父类,可用于调用**父类构造方法**,访问父类**方法和变量**，注意在子类构造器中调用父类构造器时super要显示的放在**第一行**



一个继承的妙用体现：

```java
public static void main(String[] args) {
    ShapeManager manager = new ShapeManager();
    manager.addShape(new Circle(new Point(4,4),3));
    manager.addShape(new Line(new Point(2,3), new Point(3,4),"green"));
    manager.addShape(new ArrowLine(new Point(1,2),
    new Point(5,5),"black",false,true));
    manager.draw();
}
```

因为在父类中shape是总的父类，其他的图形是他的实现类，函数的形参声明的是Shape shape，但是由于**向上转型**的原因，我们就可以给他赋值成他的子类，即**父类的引用可以有多种子类的表现形态**，这实际上就是**多态**：一种类型的变量,可引用**多种实际类型对象**



这里面又引出了两个很重要的概念：

1. 静态类型：Shape就是引用shape的静态类型
2. 动态类型：后面的多种表现形式比如new Circle，new Line等就是引用的动态类型
3. 在调用方法的时候shapes[i].draw () 就是**动态绑定**的，实际执行的是不同的动态类型中的draw方法
4. 为什么需要多态和动态绑定呢？ 实际上就是我们操作对象的代码往往知道对象是**某种父类型**就行了，做到统一的，清晰的管理



继承和多态的基本概念：

1. 每个类有且只有一个父类,没有声明父类的,其父类为Object,子类继承了父类非private的属性和 方法,可以增加自己的属性和方法,以及重写父类的方法实现
2. new过程中,父类先进行初始化,可通过super调用父类相应的构造方法,没有使用super的情况 下,调用父类的默认构造方法
3. 子类变量和方法与父类重名的情况下,可通过super强制访问父类的变量和方法
4. 子类对象可以赋值给父类引用变量,这叫多态;实际执行调用的是子类实现,这叫动态绑定



### 4.2 继承的细节

对继承中类的其他成员进行说明



### 4.2.1 构造方法

1. 在子类中通过super调用父类的构造方法，如果子类没有通过super调用的话，就会**自动调用**父类的默认构造方法
2. 在父类没有默认的构造方法之后，子类必须在它的构造器中用super显示调用父类的有参构造：

```java
public class Child extends Base {
   public Child(String member) {
   super(member);
  }
}
```

注意：在父类构造方法中调用了可被重写的方法,则可能会出现意想不到的结果

```java
public class Base {
   public Base(){
    test();
}
    public void test(){
  }
}


public class Child extends Base {
     private int a = 123;
     public Child(){
  }
     public void test(){
      System.out.println(a);
   }
}
```

那么在输出的时候a先是0，因为先初始化父类的构造方法，但是test被子类重写了，但是这时候子类的a 还没有被赋值所以是0



### 4.2.2 重名与静态绑定

重名的private变量和方法：在子类中访问:是子类:;在父类中访问:是父类，因为是私有的

重名的public变量和方法：变量的访问看静态类型（引用左边的），方法的访问看动态类型（引用的右边）

重要结论：**实例变量、静态变量、静态方法、 private方法,都是静态绑定的**，实例方法是动态绑定的



### 4.2.3  重载和重写

重载就是只有方法名字一样，其他的都可以不一样

重写是只有具体的实现不一样

注意：当有**多个重名函数**的时候在决定要调用哪个函数的过程中首先是按照**参数类型**进行匹配的，比如long与int如果匹配的话就是重载方法形参有int类型的占优势



### 4.2.4 父子类型转换

多态就是父类的引用指向子类对象，但是可以把父类的引用赋值给子类吗？其实看情况就是能不能赋值取决于这个父类变量的**动态类型**是不是这个**子类**，或者是这个**子类的子类**

在java中用**instanceof**关键字来判断类型转换的问题



### 4.2.5 继承访问权限protected

就是在protected修饰的情况下，同包的或者是子类的情况下可以访问到，然后引出了一个设计模式：

模板方法：父类定义了实现的模板但是具体的实现子类来解决，最后子类调用模板方法



### 4.2.6 4.2.7 可见性重写，防止继承final

子类继承的的时候，可以提升父类的访问修饰符，不能降低，final可以加载**类上还有方法上**，表示不希望被继承的变量，独一份的意思



### 4.3 继承实现的基本原理（以下重要）

### 4.3.1 示例

书中列举了一个很好的例子来说明了类加载的顺序，以及静态绑定，子类重写的综合例子：

Base类:

```java
public class Base {
    public static int s;
    private int a;
    static {
        System.out.println("基类静态代码块 , s: "+s);
        s = 1;
    }
    {
        System.out.println("基类实例代码块 , a: "+a);
        a = 1;
    }
    public Base(){
        System.out.println("基类构造方法 , a: "+a);
        a = 2;
    }
    protected void step(){
        System.out.println("base s: " + s +", a: "+a);
    }
    public void action(){
       System.out.println("start");
       step();
       System.out.println("end");
    }
}
```

Child类：

```java
public class Child extends Base {
    public static int s;
    private int a;
    static {
       System.out.println("子类静态代码块 , s: "+s);
       s = 10;
    }
    {
       System.out.println("子类实例代码块 , a: "+a);
       a = 10;
    }
     public Child(){
       System.out.println("子类构造方法 , a: "+a);
       a = 20;
    }
    protected void step(){
       System.out.println("child s: " + s +", a: "+a);
    }
}
```

测试：

```java
public static void main(String[] args) {
    System.out.println("---- new Child()");
    Child c = new Child();
    System.out.println("\n---- c.action()");
    c.action();
    Base b = c;
    System.out.println("\n---- b.action()");
    b.action();
    System.out.println("\n---- b.s: " + b.s);
    System.out.println("\n---- c.s: " + c.s);
}
```

**输出结果**：

```java
---- new Child()
基类静态代码块 , s: 0
子类静态代码块 , s: 0
基类实例代码块 , a: 0
基类构造方法 , a: 1
子类实例代码块 , a: 0
子类构造方法 , a: 10
    
---- c.action()
start
child s: 10, a: 20
end
    
---- b.action()
start
child s: 10, a: 20
end
    
---- b.s: 1
---- c.s: 10
```



### 4.3.2  类加载过程

在Java中,类是动态加载的,当第一次使 用这个类的时候才会加载,加载一个类时,会查看其**父类**是否已加载,如果没有,则会**加载其父类**



类的信息包含：

1. 类变量(静态变量)
2. 类初始化代码
3. 类方法(静态方法)
4. 实例变量
5. 实例初始化代码
6. 实例方法
7. **父类信息引用**



类加载过程包括：

1. 分配内存保存类的信息
2. 给类变量赋默认值
3. **加载父类**
4. 设置父子关系
5. 执行类初始化代码



注意：

1. 类初始化代码,是**先执行父类**的,**再执行子类**的，在执行父类的时候子类静态变量的值是默认值
2. 内存分为**栈和堆**,栈存放**函数**的**局部变量**,而**堆**存放**动态分配的对象**，**方法区**存放**类的信息**



在加载完类后，方法区中的**内存布局**为：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E6%96%B9%E6%B3%95%E5%8C%BA%E5%86%85%E5%AD%98%E5%B8%83%E5%B1%80.png?raw=true)



### 4.3.3 对象创建的过程

new Child ()就是创建Child对象过程：

1. 分配内存(**本类**和**所有父类**的**实例变量**,但**不包括**任何**静态变量**)
2. 对所有实例变量赋默认值
3. 执行实例初始化代码(先从父类开始,再执行子类)
4. 每个对象除了保存类的**实例变量**之外,还保存着**实际类信息的引用**



在Child c=new Child (),Base b=c后的内存布局：在堆上c对象还保留着**c类的引用**，这个指向**方法区中的类信息**：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/%E5%BC%95%E7%94%A8%E6%96%B9%E6%B3%95%E5%8C%BA.png?raw=true)



### 4.3.4  方法调用的过程

**重要结论**：

1. 寻找要执行的实例方法的时候是从对象的**实际类型信息**开始查找的**找不到**的时候再**查找父类**类型信息
2. 动态绑定实现的机制就是根据**对象的实际类型**查找要执行的方法子类型中找不到的时候再**查找父类**



如果**继承**的层次**比较深**要调用的方法位于比较**上层**的父类，大多数系统使用一种称为**虚方法表**的方法来优化调用的效率：

**虚方法表**：

1. 类加载的时候为每个类创建一个表记录该类的对象**所有动态绑定**的方法
2. 包括**父类的方法**(及其地址)但一个方法只有一条记录
3. **子类重写了父类方法**后只会保留子类的
4. 虚方法表中存在：**所有动态绑定**的方法，**父类的方法**，**子类重写了父类方法**



如：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/65cfbe6cba3dd70a1c9fbf174db1efa.png?raw=true)



变量的访问过程就是动态绑定的



### 4.4  为什么说继承是把双刃剑

因为继承可能**破坏封装性**，没有反应is a的关系（是一个的关系）



### 4.4.1  继承破坏封装

封装的定义：就是隐藏实现细节,提供简化接口，使用者就学会怎么用就可以了，那么继承为什么会破坏封装性呢？因为子类和父类之间可能存在着**实现细节**的依赖，具体如下：

1. 子类在继承父类的时候,往往不得不**关注父类的实现细节**
2. 父类在**修改其内部实现**的时候,如果不考虑子类,也往往会影响到子 类



### 4.4.2 封装是如何被破坏的

base:提供了两个方法add和addAll，一个是添加一个元素到数组里面，addAll把一组元素加载到数组中，使用者来说就知道怎么用就可以了

child类：重写了两个方法，并且在添加数字的同时汇总数字,存储数字的**和**到实例变量**sum**中：

```java
public void add(int number) {
    super.add(number);
    sum+=number;
}
```

就是因为这个原因导致使用addAll添加1、 2、 3,期望的输出是1+2+3=6,实际输出为12，**动态绑定**使得add方法执行了两次，你只能这样修改才能使得他是正确的：

```java
@Override
public void addAll(int[] numbers) {
   super.addAll(numbers);
}
```

但是你需要知道父类的实现细节才能写出正确的代码，但是这就破坏了封装性，让使用者很迷茫



还有就是在基类中随意的添加方法：

```java
public void clear(){
   for(int i=0;i<count;i++){
      arr[i]=0;
   }
     count = 0;
}
```

这时候你的子类sum中的值在运行的时候并没有被清零，虽然你的数组里面的值清零了，比如下面的操作：

```java
public static void main(String[] args) {
   Child c = new Child();
   c.addAll(new int[]{1,2,3});
   c.clear();
   c.addAll(new int[]{1,2,3});
   System.out.println(c.getSum());
}
```

你需要在手动重写一遍clear才能让结果正确



### 4.4.4  如何应对继承的双面性

1. 避免使用继承
2. 正确使用继承



避免使用继承：

1. 使用final
2. 使用组合把父类的引用添加到子类中去，并且通过构造器初始化，但是这样也有弊端，就是因为没有了继承关系，那么**子类对象不能当作基类对象来统一处理了**



## 第五章 类的扩展

除了基本类型和类的概念，还有一些拓展概念：包括**接口**、**抽象类**、**内部类**和**枚举**，代替继承我们可以用**接口**，在**接口和类之间**还有一个**抽象类**的概念



### 5.1 接口的本质

在很多情况下，我们关注的其实不是对象的**类型**，而是对象的**能力**，对象类型并不重要，重要的是这个对象能给你带来什么能力，那么**接口**就是能正确的表现出对象的**能力**的一种方式



### 5.1.1 接口的概念

接口声明了**一组能力**,但它自己并没有**实现**这个能力，它只是做了一个约定



### 5.1.2 定义接口

接口声明的例子：

```java
public interface MyComparable {
  int compareTo(Object other);
}
```

1. Java使用**interface**这个关键字来声明接口,修饰符一般都是**public**
2. 接口定义里面,声明了一个方法compareTo,但没有定义方法体，Java 8之前,接口内不能实现方法，以后可以
3. 接口方法不需要加修饰符,加与不加相当于都是**public abstract**
4. 接口需要两个参与者：一个需要**实现接口**,另一个**使用接口**



### 5.1.3  实现接口

类可以实现接口,表示**类的对象**具有**接口**所表示的**能力**

实现接口的例子：

```java
public class Point implements MyComparable {
    private int x;
    private int y;
public Point(int x, int y) {
    this.x = x;
    this.y = y;
}
public double distance(){
    return Math.sqrt(x*x+y*y);
}
@Override
public int compareTo(Object other) {
    if(!(other instanceof Point)){
        throw new IllegalArgumentException();
}
    Point otherPoint = (Point)other;
    double delta = distance() - otherPoint.distance();
    if(delta<0){
    return -1;
    }else if(delta>0){
    return 1;
}else{
    return 0;
	}
}
@Override
public String toString() {
    return "("+x+","+y+")";
	}
}
```

1. Java使用implements这个关键字表示实现接口,前面是类名,后面是接口名
2. 实现接口必须要实现接口中声明的方法, Point实现了compareTo方法



一个类也可以实现多个接口：

```java
public class Test implements Interface1, Interface2 {
// 主体代码
}
```



### 5.1.4  使用接口

接口不能new但是可以声明**接口类型**的变量,**引用**实现了接口的类对象比如：

```java
MyComparable p1 = new Point(2,3);
MyComparable p2 = new Point(1,2);
System.out.println(p1.compareTo(p2));
```

如果一个类型实现了**多个接口**,那么这种类型的对象就可以被**赋值给任一接口类型**的变量

```java
Point[] points = new Point[]{
new Point(2,3), new Point(3,4), new Point(1,2)
};
System.out.println("max: " + CompUtil.max(points));
CompUtil.sort(points);
System.out.println("sort: "+ Arrays.toString(points));
```

只要任意一个类实现了MyComparable接口那么就可以使用CompUtil.max，针对**接口**而非**具体类型**进行编程,是计算机程序的一种重要思维方式，

针对MyComparable接口编程,它并不知道**具体的类型**是什么,也并不关心,但 却可以对**任意实现了**MyComparable接口的类型进行操作，并且接口更重要的是**降低了耦合**,**提高了灵活性**



### 5.1.5  接口的细节

接口中的变量：

```java
public interface Interface1 {
  public static final int a = 0;
}
```

在接口中变量的修饰符是：public static ﬁnal，就算你不写也是一样的，这个变量可以使用**接口名.变量名**的方式使用,如Interface1.a



接口的继承：

```java
public interface IBase1 {
	void method1();
}
public interface IBase2 {
	void method2();
}
public interface IChild extends IBase1, IBase2 {
    
}
```

接口的继承跟类的继承一样，并且接口可以有多个父接口



类的继承与接口：

```java
public class Child extends Base implements IChild {
//主体代码
}
```

类可以在继承基类的情况下,同时实现一个或多个接口，不过要注意：关键字extends要放在implements**之前**



 instanceof：

```java
Point p = new Point(2,3);
if(p instanceof MyComparable){
	System.out.println("comparable");
}
```

接口也可以使用instanceof关键字,用来判断一个对象**是否实现了某接口**



### 5.1.6  使用接口替代继承

 使用接口替代继承,针对接口编程,可以实现**统一处理不同类型**的对象，但是接口没有实现不能复用代码，那么将组合和接口结合起来替代继承就可以了：

```java
public interface IAdd {
    void add(int number);
    void addAll(int[] numbers);
}

public class Base implements IAdd {
	//主体代码,与代码清单4-10一样
}

public class Child implements IAdd {
	//主体代码,组合使用Base,与代码清单4-12一样
}
```



### 5.1.7  Java 8和Java 9对接口的增强

在Java 8之前,接口中的方法都是抽象方法,都没有实现体，Java 8允许在接口中定义两类新方法: **静态方法**和**默认方法**

```java
public interface IDemo {
	void hello();
	public static void test() {
    System.out.println("hello");
}
	default void hi() {
	System.out.println("hi");
	}
}
```

可以通过IDemo.test  ()调用,默认方法与抽象方法都是接口的方法,不同在于,默认方法**有默认的实现**,实现类可以改变它的实现,也可以不改变,为的是**函数式编程**用，

函数式数据处理需要给一些接口**增加一些新的方法**,所以就有了默认方法的概念,接口增加了新方法,而接口现有的实现类也**不需要必须实现**，比如list中的sort接口：

```java
default void sort(Comparator<? super E> c) {
	Object[] a = this.toArray();
	Arrays.sort(a, (Comparator) c);
	ListIterator<E> i = this.listIterator();
	for(Object e : a) {
		i.next ();
		i.set((E) e);
	}
}
```



### 5.2  抽象类

什么是抽象类？一般的类有直接对应的对象但是抽象类没有，比如动物还有狗的概念，他是对一个具体的类的一个抽象的表达



### 5.2.1 抽象方法和抽象类

我们一般把**只有子类才知道如何实现的**方法定义为抽象方法，具体方法有实现代码,而抽象方法**只有声明,没有实现**

抽象方法和抽象类都使用abstract这个关键字来声明：

```java
public abstract class Shape {
    //其他代码
    public abstract void draw();
}
```

抽象类和具体类一样,可以定义**具体方法**、**实例变量**等，抽象类与具体的类的核心区别是：**抽象类不能创建对象**

那么创建他的对象的话就必须通过他的子类，一个类在继承抽象类后,必须实现抽象类中定义的**所有抽象方法**

```java
public class Circle extends Shape {
    //其他代码
    @Override
    public void draw() {
    //主体代码
    }
}
```

抽象类的对象不能直接通过抽象类直接来new但是可以通过声明抽象类的变量,引用抽象类具体子类：

```java
Shape shape = new Circle();
shape.draw();
```



### 5.2.2 为什么需要抽象类

 为什么需要呢？

1. 引入抽象方法和抽象类,是Java提供的一种语法工具,对于一些类和方法,引导使用者正确使用它们,**减少误用**
2. 使用抽象方法而非空方法体,子类就知道它必须要实现该方法,而**不可能忽略**,若忽略 Java编译器会**提示错误**
3. 使用抽象类,类的使用者创建对象的时候,就知道必须要使用某个具体子类,而 不可能误用不完整的父类

总体来说是java语言本身的一种**保护机制**



### 5.2.3  抽象类和接口

相似处：

1. 都不能用于创建对象
2. 接口中的方法其实都是抽象方法

不同处：

1. 接口中不能定义实例变量，抽象类可以
2. 一个类可以实现多个接口,但只能继承一个类

之间的关系：

1. 抽象类和接口是**配合**而非**替代**关系
2. 它们经常一起使用,接口声明能力,抽象类提供**默认实现**，实现**全部**或**部分**方法
3. 一个接口经常有一个对应的抽象类：

```java
Collection接口和对应的AbstractCollection抽象类。
List接口和对应的AbstractList抽象类。
Map接口和对应的AbstractMap抽象类。
```



可以说对于**需要实现接口的具体类**而言,有两个选择：

1. 一个是**实现接口**,自己**实现全部方法**
2. **继承**抽象类,然后**根据需要**重写方法（好评）

一般的配合关系是：用接口规定功能，然后用抽象类选择性的实现需要实现的方法，然后用子类去继承这个抽象类，来进行代码的复用，或者子类直接继承接口也可以，选择的方式变多了



### 5.3 内部类的本质

基本概念：

1. 一个类还可以放在另一个类的内部,称之为内部类,包含它的类称之为外部类
2. 内部类与包含它的外部类有比较密切的关系,而与其他类关系不大
3. 定义在类内部,可 以实现对外部完全隐藏,可以有更好的封装性,代码实现上也往往更为简洁
4. 在jvm虚拟机编译的时候：每个内部类最后都会被编译为一个**独立的类**, 生成一个**独立的字节码文件**
5. 内部 类可以方便地访问**外部类的私有变量**,可以声明为private从而实现对外完全隐藏

内部类的分类：

1. 静态内部类
2. 成员内部类
3. 方法内部类
4. 匿名内部类



### 5.3.1  静态内部类

静态内部类与静态变量和静态方法定义的位置一样,也**带有static关键字**,只是它定义的是**类**

```java
public class Outer {
    private static int shared = 100;
    //内部类定义
    public static class StaticInner {
    public void innerMethod(){
    //内部类直接访问外部类的静态变量和方法    
    System.out.println("inner " + shared);
    }
    }
    public void test(){
    //直接new出内部类的对象直接调用    
    StaticInner si = new StaticInner();
        si.innerMethod();
    }
}
```

静态内部类除了位置放在其他 类内部外,它与一个**独立的类差别不大**,可以有**静态变量**、**静态方法**、**成员方法**、**成员变量**、**构造方法**等

注意：

1. 内部类直接访问外部类的静态变量和方法
2. **不**可以访问**实例变量**和**方法**
3. 在类内部,可以直接使用内部静态 类,如test ()方法所示
4. public静态内部类可以被**外部使用**,只是需要通过“**外部类.静态内部类**”的方式使用

```java
//相当于是Outer对象的一个静态属性
Outer.StaticInner si = new Outer.StaticInner();
si.innerMethod();
```

使用场景：如果它与外部类关系密切,且**不依赖**于外部类实例,则可以考虑  定义为静态内部类



### 5.3.2  成员内部类

与静态内部类相比,成员内部类**没有static修饰符**

```java
public class Outer {
    private int a = 100;
    //成员内部类
    public class Inner {
    public void innerMethod(){
    System.out.println("outer a " +a);
    Outer.this.action();
    }
    }
    private void action(){
    System.out.println("action");
    }
    public void test(){
    //new出对象直接调用    
    Inner inner = new Inner();
    inner.innerMethod();
    }
}
```

注意：

1. 与静态内部类不同,除了静态变量和方法，成员内部类还可以直接访问**外部类**的**实例变量**和**方法**
2. 成员内部类还可以通过“外部类.this.xxx”（在重名的情况下）的方式引用外部类的实例变量和方法，不重名直接省略
3. 成员内部类对象总是与一个外部类对象相连的，在外部使用时,它不能直接通过new Outer.Inner ()的方式创建对象

```java
Outer.Inner inner = new Outer().new Inner();
inner.innerMethod();
```

4. **成员内部类**中**不可以**定义**静态变量和方法**
5. 方法内部类和匿名内部类也都不可以

使用场景：如果内部类与外部类关系密切,需要**访问外部类**的实例变量或方法,则可以考虑定义为成员内部类，比如：外部类的一些方法的返回值可能是某个接口,为了返回这个接口,外部类方法可能**使用内部类实现这个接口**,这个内部类可以被设为private,对外完全隐藏



### 5.3.3  方法内部类

内部类还可以定义在一个方法体中：

```java
public class Outer {
    private int a = 100;
    public void test(final int param){
    final String str = "hello";
    //方法内部类    
    class Inner {
    public void innerMethod(){
    System.out.println("outer a " +a);
    System.out.println("param " +param);
    System.out.println("local var " +str);
    }
    }
    Inner inner = new Inner();
    inner.innerMethod();
    }
}
```

注意：

1. 如果方法是**实例方法**,则除了静态变量和方法,内部类还可以**直接访问**外部类的实例变量和方法

2. 如果方法是**静态方法**,则方法内部类只能访问外部类的静态变量和方法

3. 如果方法是静态方法,则方法内部类只能访问外部类的静态变量和方法，这些变量必须被声明为**ﬁnal**，

   因为：方法内部类操作的并不是外部的变量,而是它自己的实例变量，只是这些变量的值和外部一 样,对这些变量赋值,并不会改变外部的值,为避免混淆,所以干脆强制规定必须声明为ﬁnal



### 5.3.4  匿名内部类

匿名内部类没有单独的类定义,它在创建对象的同时定义类：

```java
new 父类 (参数列表) {
//匿名内部类实现部分
}

或者：
    
new 父接口 () {
//匿名内部类实现部分
}    
    
```

如：

```java
public class Outer {
    public void test(final int x, final int y){
    Point p = new Point(2,3){
    @Override
    public double distance() {
    return distance(new Point(x,y));
    }
    };
    System.out.println(p.distance());
    }
}
```

1. 创建Point对象的时候定义了一个匿名内部类,**这个类的父类是Point**
2. 它没有名字,没有构造方法,但可以根据参数列表调用对应的父类构造方法
3. 它可以定义实例变量和方法可以有初始化代码块
4. 匿名内部类也可以访问外 部类的所有变量和方法
5. 可以访问方法中的ﬁnal参数和局部变量



### 5.4  枚举的本质

枚举是一种特殊的数据,它的取值是有限的,是可以枚举出来的



### 5.4.1  基础

可以用枚举来表示衣服的尺寸：

```java
public enum Size {
    SMALL, MEDIUM, LARGE
}
```

使用size:

```java
Size size = Size.MEDIUM
```

枚举变量的toString方法返回其字面值,所有枚举类型也都有一个name()方法,返回值与 toString()一样

```java
Size size = Size.SMALL;
System.out.println(size.toString());
System.out.println(size.name());
```

枚举变量可以使用**equals和==**进行比较,结果是一样的

枚举类 型都有一个方法int ordinal(),表示枚举值在声明时的顺序,从0开始

```java
Size size = Size.MEDIUM;
System.out.println(size.ordinal());
```

枚举变量可以用于和其他类型变量一样的地方,如**方法参数、类变量、实例变量**

```java
static void onChosen(Size size){
    switch(size){
    case SMALL:
    System.out.println("chosen small"); break;
    System.out.println("chosen medium"); break;
    System.out.println("chosen large"); break;    
    }
}
```

还可以给枚举值添加属性，以及构造器：

```java
public enum jj {
    SIZS(1),ZZ(2);
    public Integer size;

    jj(int i) {
        this.size = i;
    }
}

class te{
    public static void main(String[] args) {
        jj j = jj.SIZS;
        jj j1 = jj.ZZ;
        System.out.println(j.size);
    }
}
```



## 第六章 异常

非正常情况在Java中统一被认为是异常



### 6.1  初识异常

Java的默认异常处理机制是退出程序,异常**发生点后**的代码都不会执行

注意：

1. throw关键字可以与return关键字进行对比，**return**代表**正常退出**, **throw**代表**异常退出**
2. return的返回位 置是确定的,就是**上一级调用者**,而throw后执行哪行代码则经常是**不确定的**,由异常处理机制动态确定



对于try/catch的使用：

1. 使用try/catch捕获并处理了异常, try后面的花括号{}内包含可能抛出异常的代码
2. 括号后的 catch语句包含能捕获的异常和处理代码



### 6.2  异常类

所有异常类都有一个共同的父类 **Throwable**



### 6.2.2   异常类体系

以Throwable为根, Java定义了非常多的异常类,表示各种类型的异常：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E7%BC%96%E7%A8%8B%E7%9A%84%E9%80%BB%E8%BE%91%E5%9B%BE%E7%89%87/%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86%E4%BD%93%E7%B3%BB.png?raw=true)

Throwable是所有异常的基类,它有两个子类: **Error**和**Exception**

**Error**:表示系统错误或资源耗尽,由Java系统自己使用,应用程序不应抛出和处理,比如OutOfMemory-Error

**Exception**：应用程序错误,它有很多子类,应用程序也可以通过继承Exception或其子类创建自定义异常



**受检异常**：别在于Java如何处理这两种异常。对于受检异常, 

**未受检异常**：程序可以运行，不会强制进行处理



### 6.2.3  自定义异常

通过继承Exception或者它的某个子类来实现自定义异常，如果你继承的父类是受检异常那么你自定义的异常就是受检异常，父类是非受检异常同理



### 6.3  异常处理

Java语言对异常处理的支持,包括catch、throw、 ﬁnally、try-with-resources和throws



### 6.3.1  catch匹配

catch还可以 有多条,每条对应一种异常类型：

```java
try{
//可能触发异常的代码
}catch(NumberFormatException e){
	System.out.println("not valid number");
}catch(RuntimeException e){
	System.out.println("runtime exception "+e.getMessage());
}catch(Exception e){
    //e.getMessage  ()获取异常消息, e.printStackTrace  ()打印异 常栈到标准错误输出流
	e.printStackTrace();
}
```

注意：

1. 抛出的异常类型是catch中声明异常的**子类**也算匹配



如果多种异常处理的代码是类似的,可以用|来进行处理：

```java
try {
//可能抛出  ExceptionA和ExceptionB
} catch (ExceptionA | ExceptionB e) {
	e.printStackTrace();
}
```



### 6.3.2  重新抛出异常

在catch块内处理完后,可以重新抛出异常,异常可以是**原来的**,也可以是**新建的**:

```java
try{
//可能触发异常的代码
}catch(NumberFormatException e){
	System.out.println("not valid number");
    //对异常进行捕获以后在抛出
	throw new AppException("输入格式不正确", e);
}catch(Exception e){
	e.printStackTrace();
	throw e;
}
```



### 6.3.3  ﬁnally

catch后面可以跟ﬁnally语句：

```java
try{
//可能抛出异常
}catch(Exception e){
//捕获异常
}finally{
//不管有无异常都执行
}
```

ﬁnally内的代码不管有无异常发生,**都会执行**

注意：

1. 如果没有异常发生,在try内的代码执行结束后执行
2. 如果有异常发生且被catch捕获,在catch内的代码执行结束后执行
3. 如果有异常发生但没被捕获,则在异常**被抛给上层之前**执行
4. 如果在try或者catch语句内有**return**语句,则return语句在**ﬁnally语句执行结束后**才执行,但ﬁnally并**不能改变返回值**
5. 如果在ﬁnally中也有return语句, try和catch内的return**会丢失**,实际会返回**ﬁnally中的返回值**

```java
public static int test(){
	int ret = 0;
try{
	return ret;
}finally{
	ret = 2;
}
}
```

结果是0不是2



```java
public static int test(){
    int ret = 0;
    try{
    int a = 5/0;
    return ret;
    }finally{
    return 2;
    }
}
```

结果是2不是0



### 6.3.4  try-with-resources

没有try-with-resources时：

```java
public static void useResource() throws Exception {
    AutoCloseable r = new FileInputStream("hello"); //创建资源
    try {
    //使用资源
    } finally {
    r.close();
    }
}
```

使用try-with-resources语法:

```java
public static void useResource() throws Exception {
    try(AutoCloseable r = new FileInputStream("hello")) { //创建资源
    //使用资源
    }
}
```

资源r的声明和初始化放在**try语句内**,不用再调用ﬁnally,在语句执行完try语句后,会自动调用资源的close ()方法,相当于变得简洁了，直接写try就行了



### 6.3.5  throws

用于声明一个方法可能抛出的异常：

```java
public void test() throws AppException,SQLException, NumberFormatException {
	//主体代码
}
```

throws跟在方法的括号后面,可以声明多个异常,以逗号分隔,表示这个方法可能抛出这些异常，并且调用者必须对这些异常做出处理

注意：

1. **未受检异常**,是**不要求**使用throws进行声明的,但对于**受检异常**,则**必须进行声明**
2. 子类不能抛出父类方法中没有声明的受检异常
3. 如果一个方法内调用了另一个声明抛出受检异常的方法,则必须**处理这些受检异常**,处理的方式既可以是catch,也可以是**继续使用throws**



### 6.3.6  对比受检和未受检异常

受检异常必须出现在throws语句中,调 用者必须处理，未受检异常则没有这个要求



## 第七章 常用基础类

这章节主要是对常用的包装类进行说明，还有String类的使用



### 包装类

在java中的每一种基本类型都会有一个对用的包装类，其中会分为装箱还有拆箱的过程，每个包装类都有一个**valueOf**方法，表示把一个基本类型转换为包装类，建议使用这个静态方法，不通过包装类的构造方法，减少new对象的次数，节省资源。

**equals**:

在最原始的equals方法中，内部直接使用==来比较两个对象的地址，这个默认的实现一般是不合适的，子类需要**重写**该实现，这样进行比较的就是两个对象的实际内容



**hashCode**:

返回一个对象的哈希值,由对象中**一般不变**的属性映射得来,一个对象的哈希值不能改变

注意：

1. **相同对象**的哈希值**必须一样**
2. **不同**对象的哈希值**一般应不同**,但这不是必需的,可以有**对象不同**但**哈希值相同**的情况
3. 对两个对象,如果equals方法返回true,则hashCode也必须一样
4. equal方法返回false时, hashCode可以一样,也可以不一样,但应该尽量不一样
5.  hashCode的默 认实现一般是将对象的内存地址转换为整数
6. 子类如果重写了equals方法,也必须重写hashCode
7. 包装类都重写了hashcode



**Comparable**：每个包装类都实现了Java API中的Comparable接口

```java
public interface Comparable<T> {
	public int compareTo(T o);
}
```

接口只有一个方法 compareTo,**当前对象**与**参数对象**进行比较,在**小于、等于、大于**参数时,应分别返回-**1、0、 1**



**包装类和String**:

valueOf:根据字符串表示**返回包装类对象**,注意这个是返回的包装类对象，返回的是对象

```java
Boolean b = Boolean.valueOf("true");
Float f = Float.valueOf("123.45f");
```



静态的parseXXX:根据字符串表示返回**基本类型值**,这个是根据字符串，来返回基本类型，是返回的基本类型

```java
System.out.println(Boolean.toString(true));
System.out.println(Double.toString(123.45));
```



### String

可以通过常量定义String变量：

```java
String name = "老马说编程";
```

可以通过new创建String变量:

```java
String name = new String("老马说编程");
```



注意：

```java
String name1 = "老马说编程";
String name2 = "老马说编程";
System.out.println(name1==name2);
```

输出为true,在内存中,它们被放在一个共享的地方,这个地方称为**字符串常量池**，它保存所有的常量字符串,每个常量只会保存一份,被所有使用者共享，当通过**常量**的形式 使用一个字符串的时候,使用的就是**常量池**中的那个对应的String类型的对象，注意是通过常量的像是使用字符串，这时候只会产生**一个对象**就是在字符串的常量池中，相当于如下的代码：

```java
//这里只会产生一个对象，在常量池中存放，给别的使用者共享
String laoma = new String(new char[]{'老 ','马 ','说','编 ','程 '});
String name1 = laoma;
String name2 = laoma;
System.out.println(name1==name2);
```



通过new创建结果就不会为true：

```java
String name1 = new String("老马说编程");
String name2 = new String("老马说编程");
System.out.println(name1==name2);
```

相当于：

```java
String laoma = new String(new char[]{'老 ','马 ','说','编 ','程 '});
String name1 = new String(laoma);
String name2 = new String(laoma);
System.out.println(name1==name2);
```

这里相当与创建了三个对象，两个在堆中，一个在常量池中，位置不一样但是指向的value是一样的

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E7%BC%96%E7%A8%8B%E7%9A%84%E9%80%BB%E8%BE%91%E5%9B%BE%E7%89%87/String.png?raw=true)

这章其他的内容都是对一些java类的api进行说明，这里就不做赘述了



## 第八章 泛型

### 8.1  基本概念和原理

举例(泛型类)：

```java
public class Pair<T> {
    T first;
    T second;
    public Pair(T first, T second){
    this.first = first;
    this.second = second;
    }
    public T getFirst() {
    return first;
    }
    public T getSecond() {
    return second;
    }
}
```

在class的后面加上了<T>表示泛型类，这个类里面的成员可以使用这个T，关于这个T的实际内容是什么，需要在外部实例化的时候来进行具体的说明,可以作为参数的传入如：

```java
Pair<Integer> minmax = new Pair<Integer>(1,100);
Integer min = minmax.getFirst();
Integer max = minmax.getSecond();
```

类型参数可以有多个：

```java
public class Pair<U, V> {
    U first;
    V second;
    public Pair(U first, V second){
    this.first = first;
    this.second = second;
    }
    public U getFirst() {
    return first;
    }
    public V getSecond() {
    return second;
    }
}	
```

在外部使用的时候就可以传入两个参数：

```java
Pair<String,Integer> pair = new Pair<String,Integer>("老马",100);
```



**基本原理**：

可以在类里面直接用object进行接收：

```java
public class Pair {
    Object first;
    Object second;
    public Pair(Object first, Object second){
    this.first = first;
    this.second = second;
    }
    public Object getFirst() {
    return first;
    }
    public Object getSecond() {
    return second;
    }
}

```

使用：

```java
Pair minmax = new Pair(1,100);
Integer min = (Integer)minmax.getFirst();
Integer max = (Integer)minmax.getSecond();
Pair kv = new Pair("name","老马");
String key = (String)kv.getFirst();
String value = (String)kv.getSecond();
```

泛型的原理就是这样，对于泛型类, **Java编译器**会将泛型代码转换为普通的**非泛型代码**，将类型参数**T擦除**，这么设计是为了兼容性而不得已的一个选择



**好处**：

泛型大致有两个好处：更好的安全性,更好的可读性

因为object可以接收任意的类型，但是返回类型与传入的类型不符的话就会发生错误：

```java
Pair pair = new Pair("老马",1);
//接收值跟构造器传入的类型不符
Integer id = (Integer)pair.getFirst();
String name = (String)pair.getSecond();
```

但是编译时没有问题的，在程序运行的时候才会出现问题，如果使用泛型的话程序就会出现编译错误：

```java
Pair<String,Integer> pair = new Pair<>("老马",1);
Integer id = pair.getFirst(); //有编译错误
String name = pair.getSecond(); //有编译错误
```



**泛型方法**：

方法也可以是泛型的,而且,一个方法是不是泛型的,与它所在的类是不是泛型**没有什么关系**

```java
public static <T> int indexOf(T[] arr, T elm){
    for(int i=0; i<arr.length; i++){
    if(arr[i].equals(elm)){
    return i;
    }
    }
    return -1;
}

//多个泛型也是可以的
public static <U,V> Pair<U,V> makePair(U first, V second){
    Pair<U,V> pair = new Pair<>(first, second);
    return pair;
}	
```

T是一个泛型,放在**方法返回值前**，意味着这个方法可以使用这个T,一般形参也会加上与方法返回值之前的泛型一致的泛型



**泛型接口**：

接口也可以是泛型的：表示接口里面的返回值或者形参可以是泛型的

```java
public interface Comparable<T> {
    public int compareTo(T o);
    }
    public interface Comparator<T> {
    int compare(T o1, T o2);
    boolean equals(Object obj);
}
```



**类型参数的限定**：

> 上界为某个具体类：表示这个NumberPair的两个参数必须是Number的子类（必须继承Number才行）

```java
public class NumberPair<U extends Number, V extends Number>
    extends Pair<U, V> {
    public NumberPair(U first, V second) {
    super(first, second);
    }
}
```

使用：

```java
//Integer和Double是Number的子类
NumberPair<Integer, Double> pair = new NumberPair<>(10, 12.34);
double sum = pair.sum();
```



> 上界为某个接口，这个类必须实现这个接口才行

```java
public static <T extends Comparable> T max(T[] arr){
    T max = arr[0];
    for(int i=1; i<arr.length; i++){
    if(arr[i].compareTo(max)>0){
    max = arr[i];
    }
    }
    return max;
}

//合理版本， T表示一种数据类型,必须实现Comparable接口,且必须可以与相同类型的元素进行比较
public static <T extends Comparable<T>> T max(T[] arr){
//主体代码
}
```



> 上界为其他类型参数 

```java
//T必须是E的子类
public <T extends E> void addAll(DynamicArray<T> c) {
    for(int i=0; i<c.size; i++){
    add(c.get(i));
    }
}
```

使用：

```java
DynamicArray<Number> numbers = new DynamicArray<>();
DynamicArray<Integer> ints = new DynamicArray<>();
ints.add(100);
ints.add(34);
//要求ints的类型必须是number的子类，这里也确实是
numbers.addAll(ints);
```



### 8.2  解析通配符

例子：

```java
public void addAll(DynamicArray<? extends E> c) {
    for(int i=0; i<c.size; i++){
    add(c.get(i));
    }
}
```

c的类型是DynamicArray<? extends E>,匹配E或E的某个子类型,具体什么子类型是未知的



< T extends E>和< ? extends E>的区别：

1.  < T extends E>用于**定义类型参数**,它声明了一个类型参数T,可放在泛型类定义中类名后面、泛型方法返回值前面
2.  < ? extends E>用于实例化类型参数,它用于实例化泛型变量中的类型参数,只是这个具体类型是未知的,只知道它是**E或E的某个子类型**

相等写法：

```java
public void addAll(DynamicArray<? extends E> c)
public <T extends E> void addAll(DynamicArray<T> c)
```



通配符形式与类型参数的关系：

1. 通配符形式都可以用类型参数的形式来替代,通配符能做的,用类型参数都能做
2. 通配符形式可以减少类型参数,形式上往往更为简单,可读性也更好,所以,能用通配符的就用 通配符。
3. 如果类型参数之间有**依赖关系**,或者返回值依赖类型参数,或者需要**写操作**,则只能用类型参数。



 **超类型通配符**

```java
//表示是E的某个子类
public void copyTo(DynamicArray<? super E> dest){
    for(int i=0; i<size; i++){
    dest.add(get(i));
    }
}
```

匹配的是E的某一个父类



## 第九章 列表和队列

![](https://javaguide.cn/assets/img/java-collection-hierarchy.9733f6ea.png)

### 9.1  剖析ArrayList

ArrayList的具体分析请看脑图总结：https://www.processon.com/view/link/61376e1ce401fd1fb6afe32f

ArrayList继承的是List< E>，Collection< E>,Iterable< E>接口：



**Iterable< E>**：

只要对象实现了Iterable接口,就可以使用foreach语法,他是个接口，他里面有一个返回值类型为**Iterator< T>**类型的接口，这个接口里面有常见的next(),hasNext()等常见的方法



**Collection< E>**：

Collection表示一个数据集合，数据间**没有位置或顺序**的概念，里面有一些常见的add后者remove方法等



**List< E>**:

List：表示**有序**或位置的数据集合，扩展了它扩展了Collection



**在ArrayList中要关注的几个点**：

**1.关于迭代器**：**在迭代器中**，通过增强for循环里面**直接调用**容器的**remove**方法，这是会产生异常：java.util.ConcurrentModificationException

```java
public void remove(ArrayList<Integer> list){
    for(Integer a : list){
    if(a<=100){
    list.remove(a);
    }
    }
}
```

下面就不会抛出异常：

```java
public static void remove(ArrayList<Integer> list){
    Iterator<Integer> it = list.iterator();
    while(it.hasNext()){
        if(it.next()<=100){
    it.remove();
    }
    }
}
```

因为使用增强for循环调用迭代器的next方法，next方法检查modCount与expectedModCount是否一样，在前面add方法的时候会增加modecount，**因为expectedModCount只能在迭代器的remove方法中更新**，但是由于调用的是ArrayList中实现的remove()方法，导致两个值不一样，因为调用的是ArrayLIst的remove导致更新不了expectedModCount



**2.在调用迭代器的remove方法前没有调用next**：

```java
//错误
public static void removeAll(ArrayList<Integer> list){
    Iterator<Integer> it = list.iterator();
    while(it.hasNext()){
    it.remove();
    }
}

//正确：
public static void removeAll(ArrayList<Integer> list){
    Iterator<Integer> it = list.iterator();
    while(it.hasNext()){
    it.next ();
    it.remove();
    }
}
```

直接调用remove会导致，lastRet的值还是初始化状态，**只有调用迭代器的next方法才会对这个属性进行正确的更新**,不调用next方法更新不了lastRet的值



**3**.**ArrayList的add方法**

1. 首先会调用ensureCapacityInternal(size + 1)，这个方法直译过来就是**确保内部容量**
2. ensureCapacityInternal(size + 1)会调用ensureExplicitCapacity(calculateCapacity(elementData, minCapacity))直译过来就是**确保显示容量**，这个方法会判断要不要进行扩容操作
3. 确保显示容量把**计算容量**calculateCapacity的调用结果当做方法的参数，如果是空数据第一次调用的话，计算容量就是返回最小的10容量给这个数组中，否则其余的情况是添加数组的时候元素逐渐增一

其实核心的方法就是ensureCapacityInternal(size + 1)确保内部容量这个方法，里面会有扩容的操作，当你的最小容量逐渐增一的过程，比数组的长度多的时候就进行扩容：

```java
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

就是进行1.5倍扩容，调用**ArrayCopy方法**按照新的参数复制一下给ElementData数组元素



**4.ArrayList的remove方法**

```java
实现思路：
public E remove(int index) {
       //首先调用rangeCheck查看一下你要删除的位置合不合理，如果比size大的话就抛出异常说明删除位置不合理
        rangeCheck(index);
        //之后统计修改次数
        modCount++;
        //之后获取到在index的这个位置的元素
        E oldValue = elementData(index);
        //计算要移动的元素个数 比如有5个元素 你要删除第一个（index为0）那么你就需要移动4次（5-0-1）
        int numMoved = size - index - 1;
        //如果需要修改的个数大于0，就修改这个数组（修改elementData数组，把index+1这个位置开始的numMoved个元素，复制到elementData中的index位置）
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //把size-1同时释放引用把最后一个数组置空（因为整体前移剩最后一个置为Null（之前是有东西的）不在引用原来的对象）之后进行垃圾回收
        elementData[--size] = null; // clear to let GC do its work
        return oldValue;
    }
```

实际上关键的地方就是**System.arraycopy方法**很关键，调用这个API来进行所谓的元素的删除操作，就是移动一组连续的元素



### 9.2 剖析LinkedList

LinkedList的具体分析请查看脑图总结：https://www.processon.com/view/link/6139c33d1efad40d93a5e396

特点：有序，并且允许元素重复，内部实现是一个双向的联表，node节点里面有两哥指针，next和prev指向node节点的前一个，或后一个元素



## 第十章  Map和Set

![](https://javaguide.cn/assets/img/java-collection-hierarchy.9733f6ea.png)

里面重要的容器就是HashMap,HashSet,LinkedHashMap这三个

### 10.1 剖析HashMap

**HashMap原理讲解**：

**继承体系**：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E7%BC%96%E7%A8%8B%E7%9A%84%E9%80%BB%E8%BE%91%E5%9B%BE%E7%89%87/HashMap/01_HashMap.png?raw=true)

题外话：多态的作用是可以通过一个统一的引用去拥有**不同的实例**



**node节点**：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E7%BC%96%E7%A8%8B%E7%9A%84%E9%80%BB%E8%BE%91%E5%9B%BE%E7%89%87/HashMap/03_Node%E7%B1%BB%E5%9B%BE.png?raw=true)

注意这个hash值**不是hashcode**，是key的hash值经过一个**扰动函数**之后生层的hash值



**底层存储结构**：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E7%BC%96%E7%A8%8B%E7%9A%84%E9%80%BB%E8%BE%91%E5%9B%BE%E7%89%87/HashMap/02_HashMap%E5%BA%95%E5%B1%82%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84%20.png?raw=true)

当hash碰撞链表的元素**超过8达到9个**并且HashMap里面的所有元素的个数**超过64个**就会变成**红黑树**



**put过程**：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E7%BC%96%E7%A8%8B%E7%9A%84%E9%80%BB%E8%BE%91%E5%9B%BE%E7%89%87/HashMap/04_map-put%E8%BF%87%E7%A8%8B%E5%BD%A2%E8%B1%A1%E5%9B%BE.png?raw=true)

table的长度必须是**2的次方数**，规定0&0是0，hash碰撞：经过扰动算法得出来的hash值也是一样的，jdk8引入红黑树是为了解决hash碰撞之后链表链化很长的问题



**扩容机制**：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E7%BC%96%E7%A8%8B%E7%9A%84%E9%80%BB%E8%BE%91%E5%9B%BE%E7%89%87/HashMap/%E6%89%A9%E5%AE%B9-%E9%93%BE%E8%A1%A8%E5%A4%84%E7%90%86.jpg?raw=true)

 为什么会扩容？因为如果你的查找效率已经很低下了，在桶中存在着大量的hash冲突以及树化，为了提高查找的性能以及效率就开始扩容



**常量分析**：

```java
//默认数组大小（桶的大小），如果你调用无参的构造函数就会为桶创建默认的长度为16
1.static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

//table的最大容量
2.static final int MAXIMUM_CAPACITY = 1 << 30;

//默认负载因子
3.static final float DEFAULT_LOAD_FACTOR = 0.75f;

//树化阈值,;链表长度超过8以后，这个链表就可能升级成为一棵树（红黑树）
4.static final int TREEIFY_THRESHOLD = 8;

//树降级成为链表的阈值（你的红黑树里面的元素如果小于6个就变成链表）
5.static final int UNTREEIFY_THRESHOLD = 6;
    
//树化的另一个参数，当哈希表中的所有元素个数超过64时，才会允许树化
6.static final int MIN_TREEIFY_CAPACITY = 64;    
```



**属性分析**：

```java
//Hash表
1.transient Node<K,V>[] table;

//当前哈希表中元素个数
2.transient int size;
    
//当前哈希表结构修改次数
3.transient int modCount;    

//扩容阈值，当你的哈希表中的元素超过阈值时，触发扩容
4.int threshold;

//负载因子 threshold = capacity * loadFactor，用来计算扩容阈值
5.final float loadFactor;
```



**构造方法**：

```java
 public HashMap(int initialCapacity, float loadFactor) {
        //其实就是做了一些校验
        //capacity必须是大于0 ，最大值也就是 MAX_CAP
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                    initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        //loadFactor必须大于0
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                    loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }


//tableSizeFor方法计算用来计算扩容阈值
   /**
     * Returns a power of two size for the given target capacity.
     * 作用：返回一个大于等于当前值cap的一个数字，并且这个数字一定是2的次方数
     * cap = 10
     * n = 10 - 1 => 9
     *|的意思是只要有一个是1就是1（有1就是1）
     * 0b1001 | 0b0100 => 0b1101
     * 0b1101 | 0b0011 => 0b1111
     * 0b1111 | 0b0000 => 0b1111
     *
     * 0b1111 => 15
     * return 15 + 1;
     * cap = 16
     *
     * n = 16;
     * 0b10000 | 0b01000 =>0b11000
     * 0b11000 | 0b00110 =>0b11110
     * 0b11110 | 0b00001 =>0b11111
     * =>0b11111 => 31
     * return 31 + 1;
     *
     * 0001 1101 1100 => 0001 1111 1111 + 1 => 0010 0000 0000 一定是2的次方数
     */
    static final int tableSizeFor(int cap) {
        int n = cap-1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }

```

总的来说这个构造方法就是给hashMap的属性**负载因子**和**扩容阈值赋值**，并且保证扩容阈值一定是2的次方数，并且保证扩容阈值不是初始容量的**二倍**关系，保证扩容阈值比初值值大的刚刚好，保证扩容阈值是2的次方数后保证扩容以后的桶也是2的次方数，进而保证桶一直是2的次方数，无论你传入的初始容量是什么



**put方法/putVal方法**：

```java
 public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
```



让hash值更散列的**扰动hash函数**：

```java
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

当你向hashmap中put一个key为null的值的时候，默认放在桶中的第一个，为了尽可能的散列通过寻址算法的时候只有后四位有用，为了尽可能让计算出来的hash值的后四位也参与运算，就要无符号右移16位，**在最后寻址确定桶位的时候尽量的分散，不要出现一致的结果**



其实在内部调用的是**putVal**这个方法，这个方法是才是真正插入数据的方法

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
        //tab：引用当前hashMap的散列表
        //p：表示当前散列表的元素
        //n：表示散列表数组的长度
        //i：表示路由寻址 结果
        Node<K,V>[] tab; Node<K,V> p; int n, i;

        //延迟初始化逻辑，第一次调用putVal时会初始化hashMap对象中的最耗费内存的散列表
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;

        //最简单的一种情况：寻址找到的桶位 刚好是 null，这个时候，直接将当前k-v=>node 扔进去就可以了
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);

        else {
            //e：不为null的话，找到了一个与当前要插入的key-value一致的key的元素
            //k：表示临时的一个key
            Node<K,V> e; K k;

            //表示桶位中的该元素，与你当前插入的元素的key完全一致，表示后续需要进行替换操作
            if (p.hash == hash &&
                    ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;

            else if (p instanceof TreeNode)//红黑树，下期讲。进QQ群:865-373-238
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                //链表的情况，而且链表的头元素与我们要插入的key不一致。
                for (int binCount = 0; ; ++binCount) {
                    //条件成立的话，说明迭代到最后一个元素了，也没找到一个与你要插入的key一致的node
                    //说明需要加入到当前链表的末尾
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //条件成立的话，说明当前链表的长度，达到树化标准了，需要进行树化
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            //树化操作
                            treeifyBin(tab, hash);
                        break;
                    }
                    //条件成立的话，说明找到了相同key的node元素，需要进行替换操作
                    if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }

            //e不等于null，条件成立说明，找到了一个与你插入元素key完全一致的数据，需要进行替换
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }

        //modCount：表示散列表结构被修改的次数，替换Node元素的value不计数
        ++modCount;
        //插入新元素，size自增，如果自增后的值大于扩容阈值，则触发扩容。
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

当你**第一次**往你的hashMap中插入元素的时候，才会初始化散列表

**最简单的情况**：就是通过寻址找到的桶位刚好是**null**的话就直接封装一个node节点放进去

**另一种情况**：就是当前你要插入的元素的hash值与当前桶位的**hash值一样**，需要进行**替换操作**

**如果此时的节点已经树化**：就进行树化插入的逻辑

**剩下的情况就是链表的情况了**：你需要去遍历这个链表，看看这个链表中的元素的key有没有一样的，有就替换，没有的话就把这个元素放在链表的最后一个

**替换**：如果当前的桶位的key与你要插入元素的key一致，或者在遍历链表的时候遇到了一致的元素的key话就需要替换操作，并且把旧元素返回



**扩容方法**：为了解决哈希冲突导致的链化影响查询效率的问题，扩容会缓解该问题(比如长度为**8**的链表会被拆开成**两个长度为4**的链表)

扩容示意图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E7%BC%96%E7%A8%8B%E7%9A%84%E9%80%BB%E8%BE%91%E5%9B%BE%E7%89%87/HashMap/%E6%89%A9%E5%AE%B9-%E9%93%BE%E8%A1%A8%E5%A4%84%E7%90%86.jpg?raw=true)

寻址为15的hash结果为15，说明通过寻址算法：数组长度-1（15：1111）&扰动hash的结果为1111（15），但是你的扰动hash值只有后四位参与运算，但是通过新的一轮寻址算法的时候，数组长度的hash值多了一位（31:11111）那么你的扰动hash参与运算的位也多了一个，计算结果一定只有两种情况，利用这一点拆分出两个链表，在代码里面实际上是用**e.hash & oldCap**这个算法实现的，用**当前链表节点的hash值**&上**旧容量的大小**，这样的结果也就只有两种情况，拆分成高低位链表



```java
final Node<K,V>[] resize() {
        //oldTab：引用扩容前的哈希表
        Node<K,V>[] oldTab = table;
        //oldCap：表示扩容之前table数组的长度
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        //oldThr：表示扩容之前的扩容阈值，触发本次扩容的阈值
        int oldThr = threshold;
        //newCap：扩容之后table数组的大小
        //newThr：扩容之后，下次再次触发扩容的条件
        int newCap, newThr = 0;

        //条件如果成立说明 hashMap中的散列表已经初始化过了，这是一次正常扩容
        if (oldCap > 0) {
            //扩容之前的table数组大小已经达到 最大阈值后，则不扩容，且设置扩容条件为 int 最大值。
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }

            //oldCap左移一位实现数值翻倍，并且赋值给newCap， newCap 小于数组最大值限制且扩容之前的阈值 >= 16
            //这种情况下，则 下一次扩容的阈值 等于当前阈值翻倍
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                    oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }

        //oldCap == 0,说明hashMap中的散列表是null，扩容之前数组长度是0，就把他旧扩容阈值成为数组的长度
        //1.new HashMap(initCap, loadFactor);
        //2.new HashMap(initCap);
        //3.new HashMap(map); 并且这个map有数据
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;

        //oldCap == 0，oldThr == 0
        //new HashMap();
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;//16
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);//12
        }

        //newThr（新扩容阈值）为零时，通过newCap和loadFactor计算出一个newThr，然后给取整
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                    (int)ft : Integer.MAX_VALUE);
        }

        threshold = newThr;

        //创建出一个更长 更大的数组
        @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;

        //扩容之前的hash表不为null，说明，hashMap本次扩容之前，table不为null
        if (oldTab != null) {

            for (int j = 0; j < oldCap; ++j) {
                //当前node节点
                Node<K,V> e;
                //说明当前桶位中有数据，但是数据具体是 单个数据，还是链表 还是 红黑树 并不知道
                if ((e = oldTab[j]) != null) {
                    //方便JVM GC时回收内存
                    oldTab[j] = null;

                    //第一种情况：当前桶位只有一个元素，从未发生过碰撞，这情况 直接计算出当前元素应存放在 新数组中的位置，然后扔进去就可以了
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;

                    //第二种情况：当前节点已经树化，本期先不讲，下一期讲，红黑树。QQ群：865-373-238
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        //第三种情况：桶位已经形成链表
 
                        //低位链表：存放在扩容之后的数组的下标位置，与当前数组的下标位置一致。
                        Node<K,V> loHead = null, loTail = null;
                        //高位链表：存放在扩容之后的数组的下表位置为 当前数组下标位置 + 扩容之前数组的长度
                        Node<K,V> hiHead = null, hiTail = null;

                        Node<K,V> next;
                        do {
                            next = e.next;
                            //hash-> .... 1 1111
                            //hash-> .... 0 1111
                            // 0b 10000
							//通过上面的15桶位的例子可以知道结果只有两种情况0或者16
                            //放在低位链表上
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            //放在高位链表上
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }

                        } while ((e = next) != null);


                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }

                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }

                    }
                }
            }
        }
        return newTab;
    }
```

实际上扩容算法就是前面的部分会计算出下一次触发扩容的扩容的条件，和扩容之后的桶大小，后面代码的部分是根据节点的情况进行相应节点的放置，值得注意的是当前的桶位上是个链表的时候，会根据**当前的hash值&旧的容量大小**来进行链表的拆分操作



**get方法**：

```java
 public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }

final Node<K,V> getNode(int hash, Object key) {
        //tab：引用当前hashMap的散列表
        //first：桶位中的头元素
        //e：临时node元素
        //n：table数组长度
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;

        if ((tab = table) != null && (n = tab.length) > 0 &&
                (first = tab[(n - 1) & hash]) != null) {
            //第一种情况：定位出来的桶位元素 即为咱们要get的数据
            if (first.hash == hash && // always check first node
                    ((k = first.key) == key || (key != null && key.equals(k))))
                return first;

            //说明当前桶位不止一个元素，可能 是链表 也可能是 红黑树
            if ((e = first.next) != null) {
                //第二种情况：桶位升级成了 红黑树
                if (first instanceof TreeNode)//下一期说
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                //第三种情况：桶位形成链表
                do {
                    if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;

                } while ((e = e.next) != null);
            }
        }
        return null;
    }
```

取元素的时候**第一种情况**：当前的桶位上的元素就是你要找的元素，没准这个桶位上面的元素已经树化，或者链化但是都与所谓

在不是单个元素的情况下（防止当前桶位上就一个元素，并且你还给取走了），就是链表和树的情况，是树的话就通过树的方法取，是链表的话就遍历然后hash还有值都一样的情况下就返回



**remove方法**：

```java
 final Node<K,V> removeNode(int hash, Object key, Object value,
                               boolean matchValue, boolean movable) {
        //tab：引用当前hashMap中的散列表
        //p：当前node元素
        //n：表示散列表数组长度
        //index：表示寻址结果
        Node<K,V>[] tab; Node<K,V> p; int n, index;

        if ((tab = table) != null && (n = tab.length) > 0 &&
                (p = tab[index = (n - 1) & hash]) != null) {
            //说明路由的桶位是有数据的，需要进行查找操作，并且删除

            //node：查找到的结果
            //e：当前Node的下一个元素
            Node<K,V> node = null, e; K k; V v;

            //第一种情况：当前桶位中的元素 即为 你要删除的元素
            if (p.hash == hash &&
                    ((k = p.key) == key || (key != null && key.equals(k))))
                node = p;


            else if ((e = p.next) != null) {
                //说明，当前桶位 要么是 链表 要么 是红黑树

                if (p instanceof TreeNode)//判断当前桶位是否升级为 红黑树了
                    //第二种情况
                    //红黑树查找操作，下一期再说
                    node = ((TreeNode<K,V>)p).getTreeNode(hash, key);
                else {
                    //第三种情况
                    //链表的情况
                    do {
                        if (e.hash == hash &&
                                ((k = e.key) == key ||
                                        (key != null && key.equals(k)))) {
                            node = e;
                            break;
                        }
                        p = e;
                    } while ((e = e.next) != null);
                }
            }


            //判断node不为空的话，说明按照key查找到需要删除的数据了
            if (node != null && (!matchValue || (v = node.value) == value ||
                    (value != null && value.equals(v)))) {

                //第一种情况：node是树节点，说明需要进行树节点移除操作
                if (node instanceof TreeNode)
                    ((TreeNode<K,V>)node).removeTreeNode(this, tab, movable);

                //第二种情况：桶位元素即为查找结果，则将该元素的下一个元素放至桶位中
                else if (node == p)
                    tab[index] = node.next;

                else
                    //第三种情况：将当前元素p的下一个元素 设置成 要删除元素的 下一个元素。
                    p.next = node.next;

                ++modCount;
                --size;
                afterNodeRemoval(node);
                return node;
            }
        }
        return null;
    }
```

第一种情况：当前桶位就是你要删除的元素，剩下的情况就是可能是链表，也可能是树

当桶位是树的情况下：调用红黑树的查找操作，找到删除...

是链表的情况下：即使循环查找要删除的元素，找到了就把元素赋值给node，然后调用删除的方法



**replace方法**：就是调用getNode方法找到就进行替换操作

































































































































































































































































































**HashMap构造方法**：

**HashMap Put方法**：

**HashMap resize扩容方法**：

**HashMap get方法remove方法**：

**HashMap replace方法**：

