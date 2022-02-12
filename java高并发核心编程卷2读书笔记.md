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
4. 偏向于通过实现Runnable接口来实现线程执行目标类，这样能使代码更加简洁明了



**线程创建方法三：使用Callable和FutureTask创建线程**

这种方式可以**获取异步执行的结果**

**Callable接口**：可以用call方法来获取异步的执行结果，允许方法的实现版本直接抛出异常，并且Callable接口的call()**有返回值**,但是**不能**作为Thread线程实例的的target来使用

```java
@FunctionalInterface
 public interface Callable<V> {
 V call() throws Exception;
 }
```



**RunnableFuture接口**

可以作为Thread线程实例的**target实例**，二是可以**获取异步执行的结果**，因为继承了Runnable接口

```java
public interface RunnableFuture<V> extends Runnable,Future<V> {
	 void run();
 }
```



**Future接口**

强大的功能：

1. 能够**取消**异步执行中的任务
2. 判断异步任务**是否执行完成**
3. **获取**异步任务完成后的**执行结果**

```java
public interface Future<V> {
 boolean cancel(boolean mayInterruptRunning); //取消异步执行
 boolean isCancelled();
 boolean isDone();//判断异步任务是否执行完成
 //获取异步任务完成后的执行结果
 V get() throws InterruptedException,
ExecutionException;
 //设置时限，获取异步任务完成后的执行结果
 V get(long timeout, TimeUnit unit) throws
InterruptedException, 
 
ExecutionException, TimeoutException;
 ...
 }
```

重要方法：

1. **V get()**：获取异步任务执行的结果，这个方法的调用是阻塞性的。如果异步任务没有执行完成，异步结果获取线程（调用线程）会一直被阻塞，一直阻塞到异步任务执行完成
2. **V get(Long timeout,TimeUnit unit)**：设置时限，调用线程就等待你形参里面的时间，超过时间将抛出异常

​             

**FutureTask类**：

这个类实现了RunnableFuture接口，既能作为一个Runnable类型的**target**执行目标直接**被Thread执行**，又能作为Future异步任务来获取**Callable的计算结果**

FutureTask在类内部用—**callable实例属性**来保存并发执行的Callable<V>类型的任务，在其**run()方法**的实现版本中会执行callable成员的**call()方法**，并且用 **outcome**来保存callable成员call()方法的**异步执行结果**



创建线程步骤：

1. 创建一个Callable接口的实现类，并实现其call()方法，编写好异步执行的具体逻辑，可以有返回值
2. 使用Callable实现类的实例构造一个FutureTask实例
3. 使用FutureTask实例作为Thread构造器的target入参，构造新的Thread线程实例
4. 调用Thread实例的start()方法启动新线程，启动新线程的run()方法并发执行
5. 内部执行过程：启动Thread实例的run()方法并发执行后，会执行FutureTask实例的run()方法，最终会并发执行Callable实现类的call()方法
6. 调用FutureTask对象的**get()方法**阻塞性地获得并发线程的执行结果



在main线程中在开启一个FutureTask线程的执行流程：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/FK%E7%BA%BF%E7%A8%8B%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B.png?raw=true)



**线程创建方法四：通过线程池创建线程**

通过线程池可以做到**复用**线程实例

通过**Executors工厂类**创建线程池：

```java
//创建一个包含三个线程的线程池
 private static ExecutorService pool =Executors.newFixedThreadPool(3);
```

有**三个**执行的方法：

```java
//方法一：执行一个 Runnable类型的target执行目标实例，无返回
 void execute(Runnable command);
//方法二：提交一个 Callable类型的target执行目标实例, 返回一个Future异步任务实例
 <T> Future<T> submit(Callable<T> task);
//方法三：提交一个 Runnable类型的target执行目标实例, 返回一个Future异步任务实例
 Future<?> submit(Runnable task);
```

submit和execute的区别是：

1. 接受的参数不一样，submit接收两种返回的参数一个是有返回值的Callable类型参数，另外一个是Runnable类型的无返回值参数
2. submit有返回值，execute无返回值

使用：

```java
//创建一个包含三个线程的线程池
 private static ExecutorService pool =Executors.newFixedThreadPool(3);
pool.execute(new DemoThread()); //执行线程实例，无返回
//提交Callable 执行目标实例，有返回
 Future future = pool.submit(newReturnableTask());
```



### 1.4 线程的核心原理

**线程的调度与时间片**

时间片：就是CPU在一段时间上的计算量非常的高，线程就是抢占这些cpu的时间片来执行相应的代码

线程的调度：基于CPU时间片方式进行线程调度，线程只有得到CPU时间片才能执行指令，处于执行

状态，没有得到时间片的线程处于就绪状态，等待系统分配下一个CPU时间片



线程的调度模型：

主要分为两种：

1. 分时调度模型，线程轮流占用cpu时间片

2. 抢占式调度模型：系统按照线程**优先级**分配CPU时间片，优先级高的线程获取的CPU时间片相

   对多一些



**线程的优先级**

Thread实例的priority属性默认是级别**5**，对应的类常量是NORM_PRIORITY。优先级最大值为**10**，最小值为**1**

priority实例属性的**优先级越高**，线程获得CPU时间片的**机会就越多**，但也**不是绝对的**



**线程的生命周期**

NEW状态：创建成功但是**没有调用start()方法**启动的Thread线程实例都处于NEW状态



RUNNABLE状态(就绪运行):注意这里面有两个状态，JVM的线程状态与其幕后的操作系统线程状态之间的转换关系：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%AD%E7%BA%BF%E7%A8%8B%E7%9A%84%E7%8A%B6%E6%80%81.png?raw=true)

就绪状态和运行状态都是操作系统中的线程状态。在Java语言中，并没有细分这两种状态，而是将这两种状态合并成同一种状态——RUNNABLE状态



TERMINATED状态：

在线程执行完run方法之后就会变为这个状态，如果在run()方法执行过程中发生了运行时异常而没有被捕获，run()方法将被异常终止，线程也会变成**TERMINATED**状态



TIMED_WAITING状态：

线程处于限时等待状态，比如说调用一些限定时间等待的API，Thread.sleep(int n)，在限定的时间内等待获取时间片，Thread.join()就属于WAITING状态



### 1.5 线程的基本操作

**线程的sleep操作**：

sleep的作用是让目前正在执行的线程休眠，让CPU去执行其他的任务。从线程状态来说，就是从执行状态变成限时阻塞状态

值得注意的是:当线程睡眠时间满后，线程不一定会立即得到执行，因为此时CPU可能正在执行其他的任务，线程首先进入**就绪状态**，等待分配CPU时间片以便有机会执行,线程的sleep操作不会让当前的线程释放锁



**线程的interrupt操作**：

由于调用stop()方法来终止线程可能会产生不可预料的结果，因此不推荐调用stop()方法，进而采用下Thread的interrupt()方法，注意：此方法本质**不是用来中断一个线程**，而是将线程设置为**中断状态**

当调用线程的interrupt()方法时会有如下两个作用：

1. 如果此线程处于**阻塞状态**,在调用interrupt方法的时候会**立马退出阻塞**，并且**抛出InterruptedException异常**，
2. 如果此线程正处于**运行之中**，线程就不受任何影响，继续运行，仅仅是线程的**中断标记**被设置为**true**，程序可以在适当的位置调用**isInterrupted**()方法，查看自己是否被中断

它只是一个表示位，让正在阻塞的线程调用之后推出阻塞抛出异常，不是让一个正在运行的线程中断，这样在危险了



线程是否停止执行，需要**用户程序去监视线程的isInterrupted**()状态，并进行相应的处理：

```java
public class InterruptDemo{
     //测试用例：获取异步调用的结果
     @Test
     public void testInterrupted2(){
     Thread thread = new Thread(){
     public void run(){
     Print.tco("线程启动了");
     //一直循环
     while (true){
     Print.tco(isInterrupted());
     sleepMilliSeconds(5000);
     //如果外面的操作设置了线程的中断标志位，线程捕获到自己的中断信号后，就退出死循环
     if (isInterrupted()) {
     Print.tco("线程结束了");
     return;
     }
     }
     }
     };
         
     thread.start();
     sleepSeconds(2); //等待2秒
     thread.interrupt(); //中断线程
     sleepSeconds(2); //等待2秒
     thread.interrupt();
     }
 }
```

如果外面的操作设置了线程的中断标志位，线程捕获到自己的中断信号后，就退出死循环，写在run方法里面



**线程的join操作**

线程合并，线程A需要将线程B的执行流程合并到自己的执行流程中：

```java
class ThreadA extends Thread{
 void run() {
 Thread threadb = new Thread("thread-b");
 threadb.join();
 }
 }
```

join的三个重载版本：

```java
//重载版本1：此方法会把当前线程变为TIMED_WAITING，直到被合并线程执行结束
 public final void join() throws InterruptedException：
 
 //重载版本2：此方法会把当前线程变为TIMED_WAITING，直到被合并线程执行结束，或者等待被合并线程执行millis的时间
 public final synchronized void join(long millis) throwsInterruptedException：
 
 //重载版本3：此方法会把当前线程变为TIMED_WAITING，直到被合并线程执行结束，或者等待被合并线程执行millis+nanos的时间
 public final synchroinzed void join(long millis, intnanos) throws InterruptedException：
```

注意：

1. 执行threadb.join()这行代码的当前线程为合并线程（甲方），进入TIMED_WAITING等待状态，让出CPU
2. 如果设置了被合并线程的执行时间millis（或者millis+nanos），并不能保证当前线程一定会在millis时间后变为RUNNABLE
3. 如果主动方合并线程在等待时被中断，就会抛出InterruptedException受检异常（因为本身就是阻塞状态）
4. join方法没有办法直接取得乙方线程的执行结果

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/join%E7%BA%BF%E7%A8%8B%E6%B5%81%E7%A8%8B.png?raw=true)



**线程的WAITING**状态:

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/%E7%BA%BF%E7%A8%8B%E7%9A%84waiting%E7%8A%B6%E6%80%81.png?raw=true)



**线程的yield(让步)操作**

让目前正在执行的线程放弃当前的执行，让出CPU的执行权限，使得CPU去执行其他的线程，该线程所对应的操作系统层面的线程从状态上来说会从执行状态变成就绪状态，在java层面是RUNNABLE

注意：

1. 它可以让当前正在执行的线程暂停，但它不会阻塞该线程，只是让线程转入就绪状态
2. yield只是让当前线程暂停一下，让系统的线程调度器重新调度一次
3. 线程调用yield之后，操作系统在重新进行线程调度时偏向于将执行机会让给优先级较高的线程
4. yield仅能使一个线程从运行状态转到就绪状态，而不是阻塞状态
5. yield不能保证使得当前正在运行的线程迅速转换到就绪状态



**线程的daemon操作**

Java中的线程分为两类：守护线程与用户线程

守护线程：在程序进程运行过程中，在后台提供某种通用服务的线程，比如**GC**（垃圾回收线程



可以通过调用Thread的实例方法setDaemon(…)来将线程设置为守护线程，或者用isDaemon()来判断线程是否为一个守护线程

守护线程与用户线程的关系：

用户线程和JVM进程是**主动关系**，如果用户线程全部终止，JVM虚拟机进程也随之终止；守护线程和JVM进程是**被动关系**，如果JVM进程终止，所有的守护线程也随之终止

注意：

1. 守护线程必须在启动前将其守护状态设置为true，启动之后不能再将用户线程设置为守护线程，否则JVM会抛出一个InterruptedException异常
2. 守护线程存在被JVM强行终止的风险，所以在守护线程中尽量不去访问系统资源，如文件句柄、数据库连接等。守护线程被强行终止时，可能会引发系统资源操作不负责任的中断，从而导致资源不可逆的损坏
3. 守护线程创建的线程也是守护线程



**线程状态总结**

1.NEW状态：通过new Thread(…)已经创建线程，但尚未调用start()启动线程，该线程处于NEW（新建）状态

2.RUNNABLE状态：Java把Ready（就绪）和Running（执行）两种状态合并为一种状态：RUNNABLE（可执行）状态（或者可运行状态）。调用了线程的start()实例方法后，线程就处于就绪状态。此线程获取到CPU时间片后，开始执行run()方法中的业务代码，线程处于执行状态

（1）就绪状态：就绪状态仅仅表示线程**具备运行资格**，如果没有被操作系统的调度程序挑选中，线程就永远处于就绪状态

调用线程的start()方法

当前线程的执行时间片用完

线程睡眠（Sleep）操作结束

对其他线程合入（Join）操作**结束**

等待用户输入结束

线程**争抢到**对象锁（Object Monitor）

当前线程调用了yield()方法出让CPU执行权限



3.BLOCKED状态：处于BLOCKED（阻塞）状态的线程并不会占用CPU资源

线程等待获取锁，IO阻塞



4.WAITING状态：

处于WAITING（**无限期等待**）状态的线程不会被分配CPU时间片，需要被其他线程显式地唤醒，才会进入就绪状态

Object.wait()方法，对应的唤醒方式为：Object.notify()/Object.notifyAll()。

Thread.join()方法，对应的唤醒方式为：被合入的线程执行完毕

LockSupport.park()方法，对应的唤醒方式为：LockSupport.unpark(Thread)



5.TIMED_WAITING状态:处于TIMED_WAITING（限时等待）状态的线程不会被分配CPU时间片，如果指定时间之内没有被唤醒，限时等待的线程会被系统自动唤醒，进入就绪状态

Thread.sleep(time)方法，对应的唤醒方式为：sleep睡眠时间结束

Object.wait(time)方法，对应的唤醒方式为：调用Object.notify()/Object.notifyAll()主动唤醒，或者限时结束

LockSupport.parkNanos(time)/parkUntil(time)方法，对应的唤醒方式为：线程调用配套的LockSupport.unpark(Thread)方法结束，或者线程停止（park）时限结束



**进入BLOCKED状态、WAITING状态、TIMED_WAITING状态的线程都会让出CPU的使用权；另外，等待或者阻塞状态的线程被唤醒后，进入Ready状态，需要重新获取时间片才能接着运行**



6.TERMINATED状态:

线程结束任务之后，将会正常进入TERMINATED（死亡）状态；或者说在线程执行过程中发生了异常（而没有被处理），也会导致线程进入死亡状态



### 1.6 线程池原理与实战

正是因为**Java线程的创建非常昂贵**他至少要进行如下的操作：

1. 必须为线程堆栈分配和初始化大量内存块，其中包含至少**1MB**的栈内存（为了方便线程进入方法后局部变量的保存）
2. 需要进行系统调用，以便在OS（操作系统）中**创建和注册**本地线程

为了降低Java线程的**创建成本**必须用到线程池：

1. 提升性能：线程池能独立负责线程的创建、维护和分配。在执行大量异步任务时，可以**不需要自己创建线程**，而是将任务交给线程池去**调度**。线程池能尽可能使用空闲的线程去执行异步任务，最大限度地对**已经创建的线程进行复用，使得性能提升明显**
2. 线程管理：每个Java线程池会保持一些**基本的线程统计信息**，例如完成的任务数量、空闲时间等，以便对线程进行有效管理，使得能对所接收到的异步任务进行**高效调度**



**JUC的线程池架构**

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/%E7%BA%BF%E7%A8%8B%E6%B1%A0%E6%9E%B6%E6%9E%84.png?raw=true)

Executor:

Executor是Java异步目标任务的“执行者”接口，其目标是执行目标任务。“执行者”Executor提供了execute()接口来执行已提交的Runnable执行目标实例

```java
void execute(Runnable command)
```



ExecutorService:

ExecutorService继承于Executor。它是Java异步目标任务的“执行者服务接”口，对**外提供异步任务的接收服务**

```java
//向线程池提交单个异步任务
 <T> Future<T> submit(Callable<T> task);
 //向线程池提交批量异步任务
 <T> List<Future<T>> invokeAll(Collection<? extendsCallable<T>> tasks)throws InterruptedException;
```



AbstractExecutorService:

AbstractExecutorService是一个抽象类，它实现了ExecutorService接口。AbstractExecutorService存在的目的是为ExecutorService中的接口提供默认实现



**ThreadPoolExecutor**:

是线程池的**实现类**，继承于AbstractExecutorService抽象类，线程池中预先提供了**指定数量的可重用线程**，所以使用线程池会**节省系统资源**，并且每个线程池都维护了一些**基础的数据统计**，方便线程的**管理和监控**



**Executors的4种快捷创建线程池的方法**

















































































































































