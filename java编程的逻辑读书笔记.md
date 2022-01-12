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

当程序在**main**函数调用Sum.sum之前,栈的情况







































































































































































