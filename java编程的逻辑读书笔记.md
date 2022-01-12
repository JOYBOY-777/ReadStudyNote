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
2. 数组有两块内存空间：一块用于存储**数组内容本身**，另一块存储内容的位置

































































































































































