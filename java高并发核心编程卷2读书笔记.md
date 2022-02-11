# java高并发核心编程

## 第一章 多线程原理与实战

### 1.2 无处不在的进程和线程

### 1.2.1 进程的基本概念

**进程的概念**：

什么是进程？所谓的进程就是**一次程序的启动或者执行**，比如连续打开10个谷歌浏览器，同时开启十个游戏等等...，是操作系统将程序**加入到内存**，然后给程序**分配**必要的**系统资源**，并且开始**运行程序的指令**



那么什么是java程序的进程呢？每一次启动java的程序就会启动一个**JVM进程**，在这个JVM进程的内部，所有的java程序代码都是以**线程**的方式运行的，那么线程的入口是什么呢？就是main方法，我们称为**主线程**，在main方法结束后主程序运行完成，JVM进程也就结束了



### 1.2.2 线程的基本概念

要明确两个概念：

进程：是**程序执行**和系统进行**并发调度**，**资源分配**的**最小单位**

线程：是指程序代码段的一次执行流程（参考JVM进程的例子），是CPU进行调度的最小单位，并且一个进程可以有**一个或者多个线程**，并且共享进程的资源

在java程序启动的时候会至少产生两个线程1.main线程 2.GC垃圾回收线程，但是实际上还会有很多的线程会启动



线程的构成：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/%E7%BA%BF%E7%A8%8B%E7%9A%84%E6%9E%84%E6%88%90.png?raw=true)

可以看到他们分为三个部分分别是：

（1）程序计数器：记录着线程**下一条指令**的**地址**，比如程序阻塞之后，在获取到cpu的时间片之后记得线程进行到哪里了

（2）栈内存：是代码段中**局部变量**的存储空间，线程每进入一个方法，方法的局部变量就会进去到栈内存中，并且栈内存是**独立**的，线程之间不共享，JDK1.8线程在创建的时候会分配大概**1M**的栈内存大小，并且**不受**垃圾回收器管理

（3）线程的基本信息：

1. 线程ID：线程的唯一标识
2. 线程名称：线程的名字，用户不自己分配的话系统会自动分配一个名字
3. 线程优先级：表示线程调度的优先级，优先级越高，线程获取CPU时间片的几率就越大
4. 线程状态：标识线程执行的基本状态，比如新建，就绪，运行，阻塞，结束等转状态
5. 其他：是否是守护线程等



在程序执行时，栈内存的结构：

**执行程序流程的重要单位是“方法”，而栈内存的分配单位是“栈帧”（或者叫“方法帧”）**

比如以下有一场景在main线程中有不同的方法，在main线程开启之后线程进入方法后main线程的栈帧在进入方法后会JVM会为这个main线程的栈帧分配空间，如下图所示：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/%E7%BA%BF%E7%A8%8B%E6%A0%88%E5%B8%A7.png?raw=true)



可以看到Main线程每一次进入一个方法（执行流程）后JVM都会为线程的栈帧分配空间，里面保存的是**方法的局部变量**，注意不是进栈一个出一个而是都进去之后再出，先入后出



**线程与进程的区别**：

1. 线程是“进程代码段”的一次顺序执行流程。一个进程由一个或多个线程组成，一个进程至少有一个线程
2. 线程是CPU调度的最小单位，进程是操作系统分配资源的最小单位。线程的划分尺度小于进程，使得多线程程序的并发性高
3. 线程是出于高并发的调度诉求从进程内部演进而来的。线程的出现既充分发挥了CPU的计算性能，又弥补了进程调度过于笨重的问题
4. 进程之间是相互独立的，但进程内部的各个线程之间并不完全独立。各个线程之间共享进程的方法区内存、堆内存、系统资源（文件句柄、系统信号等）
5. 切换速度不同：线程上下文切换比进程上下文切换要快得多。所以，有的时候，线程也称为轻量级进程



### 1.3 创建线程的4种方法

Java进程中每一个线程都对应着一个**Thread实例**

**Thread类**：

可以通过线程的构造方法：**Thread(String threadName)**来给指定的线程一个名字

可以通过**public final void setDaemon(boolean on)**方法来将一个**线程的实例**标记为**守护线程**（比如垃圾回收线程）

**线程的状态**：

```java
public static enum State {
     NEW, //新建
     RUNNABLE, //就绪、运行
     BLOCKED, //阻塞
     WAITING, //等待
     TIMED_WAITING, //计时等待
     TERMINATED; //结束
 }
```

**线程的启动和运行:**

调用start方法来**启动一个线程**，run方法作为用户自定义异步方法的逻辑**执行入口**,新线程的执行会调用Thread的run方法

并且可以通过**public static Thread currentThread()**方法来获取一个线程的实例对象



**1.创建空线程**：

```java
public class EmptyThreadDemo {
 public static void main(String args[]) throwsInterruptedException {
     //使用Thread类创建和启动线程
     Thread thread = new Thread();
     Print.cfo("线程名称："+thread.getName());
     Print.cfo("线程ID："+thread.getId());
     Print.cfo("线程状态："+thread.getState());
     Print.cfo("线程优先级："+thread.getPriority());
     Print.cfo(getCurThreadName() + " 运行结束.");
     thread.start();
 }
 }
```

这是一个空线程，没有子类继承，所以默认的**线程target属性**是**空的** Run方法如下：

```java
public void run() {
     if(this.target != null) {
     this.target.run();
     }
 }
```

很显然上面的属性就是空的，当继承Thread时，已经重写run方法，所以jvm底层调用run时，其实直接调用子类的方法，而不是该方法。这种只是传入一个runnable实例的时候才调用run



**线程创建方法一：继承Thread类创建线程类**

注意：要新建一个类继承继承Thread类，然后重写他的run方法,通过构建子类的形式JVM会直接调用子类的run方法



**线程创建方法二：实现Runnable接口创建线程目标类**

这个实现形式的话就是通过public Thread(Runnable target)来传入一个target类型的实例，通过Thread类创建线程对象，将Runnable实例作为实际参数传递给Thread类的构造器，由Thread构造器将该Runnable实例赋值给自己的target执行目标属性，通过执行Target的run方法来执行你编写的run方法

```java
//伪代码
Runnable target = new RunTarget();
thread = new Thread(target, "RunnableThread"+ threadNo++);
thread.start();
```



**Runnable方式的优缺点**：

1. 所创建的类并**不是线程类**，而是线程的**target执行目标类**，需要将其实例作为参数传入线程类的构造器，才能创建真正的线程
2. 如果访问当前线程的属性（甚至控制当前线程），不能直接访问Thread的实例方法,因为不是Thread的实例，需要通过Thread.currentThread()来获取

好处：

1. 可以避免由于Java单继承带来的局限性。如果异步逻辑所在类已经继承了一个基类，就没有办法再继承Thread类。比如，当一个Dog类继承了Pet类，再要继承Thread类就不行了。所以在已经存在继承关系的情况下，只能使用实现Runnable接口的方式（源于java不能多继承）
2. **逻辑和数据更好分离**

就是因为通过继承的方式不可拆分实例，但是通过Runnable可以拆分一个实例：

```java
//一个循环new 两次
Print.hint("商店版本的销售");
 for (int i = 1; i <= 2; i++){
 Thread thread = null;
 thread = new StoreGoods("店员-" + i); //商店商品的
//销售线程
 thread.start();
 }

//Runnable拆分实例
Print.hint("商场版本的销售");
//循环共用一个实例
 MallGoods mallGoods = new MallGoods(); //创建了1个公用的
 for (int i = 1; i <= 2; i++){
 Thread thread = null;
 thread = new Thread(mallGoods, "商场销售员-" +i); //销售员线程
 thread.start();
 }
```

各自的好处：

1. 通过继承Thread类实现多线程能更好地做到多个线程并发地完成各自的任务，访问各自的数据资源
2. 通过实现Runnable接口实现多线程能更好地做到多个线程并发地完成同一个任务，**访问同一份数据资源**。多个线程的代码逻辑可以方便地访问和处理同一个共享数据资源（如例子中的MallGoods.goodsAmount），这样可以将线程逻辑和业务数据进行有效的分离，更好地体现了面向对象的设计思想
3. 通过实现Runnable接口实现多线程时，如果数据资源存在多线程共享的情况，那么数据共享资源需要使用原子类型（而不是普通数据类型），或者需要进行线程的同步控制，以保证对共享数据操作时不会出现线程安全问题
4. 

























































































































































































































































