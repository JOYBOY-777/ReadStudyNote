# 第1章——多线程原理与实战

**无处不在的进程与线程**

总的来说计算机**处理任务的调度单位**就是进程和线程，进程被分为**后台进程**和**应用进程**两大部分：

* 后台进程：在windows中在**系统开始运行**的时候就已经启动了，负责完成系统的基本功能
* 应用进程：大部分的应用进程由**用户手动启动**

一个进程就是一个程序的**一次启动和执行**，是操作系统给程序装入内存，分配系统资源，并且开始运行程序的指令，一个程序可以多次启动，对应多个进程



**进程的基本原理**

进程的组成部分：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-2.jpg?raw=true)

主要分为三个部分：

* 程序段：是进程的**程序指令**在**内存中的位置**，包括需要执行的**指令集合**
* 数据段：是进程的**操作数据**在内存中的位置，包含需要操作的**数据集合**
* 进程控制块：由四大部分组成：
  * 进程的描述信息：包括进程的Id,名称，状态，优先级等
  * 进程的调度信息：程序起始地址（程序第一行指令的内存地址）、通信信息（进程间通信的消息队列）
  * 进程的资源信息：内存信息，I/O设备信息、文件曲柄
  * 进程上下文：进程的环境

什么是java程序的进程呢？每当启动一个java程序的时候，就会启动一个**JVM进程**，在这个进程内部，所有的代码都是以**线程**来运行的，运行main方法就产生一个线程，被称为主线程，当主线程运行完成，JVM也就退出了



**线程的基本原理**

进程是**程序执行**和**系统并发调度**的最小单位，为了充分发挥CPU计算性能，弥补进程调度笨重的问题，进程内部演进出了并发调度的诉求，于是线程就出来了

线程是**进程代码段**的一次**顺序执行流程**是**CPU调度的最小单位**，一个进程可以有多个线程，线程之间共享进程的**内存空间**、**系统资源**也是操作系统**资源分配的最小单位**

线程的组成部分：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-3.jpg?raw=true)

* 线程描述信息：
  * 线程ID:线程的唯一标识
  * 线程名称：线程的名字，没有指定的话，jvm会自动分配
  * 线程优先级：表示线程调度的优先级，优先级越高，获得CPU的执行机会越大
  * 线程状态：表示当前线程的执行状态（新建、就绪、运行、阻塞、结束）的一种
  * 其他：是否为守护线程等
* 程序计数器：记录着线程**下一条指令的代码段内存地址**
* 栈内存：是代码中**局部变量**的存储空间，是线程独立拥有的，线程之间不共享，默认大小是1MB，并且不受**垃圾回收器管理**



在java中执行程序流程的重要**单位**是**方法**，**栈**内存的分配单位是**栈帧**，方法的每一次执行都要为其分配一个栈帧，栈帧保存方法的**局部变量**，**方法的返回地址等信息**，当线程进入方法的时候，JVM就会为这个方法分配一个栈帧并且把他压入到**栈内存**中，当方法执行完毕之后，这个栈帧就从栈内存中弹出，里面的变量之类的内存空间就会被**回收**

举个栗子就是：

```java
public class StackAreaDemo {
    public static void main(String args[]) throws InterruptedException {
        Print.cfo("当前线程名称：" + Thread.currentThread().getName());
        Print.cfo("当前线程ID：" + Thread.currentThread().getId());
        Print.cfo("当前线程状态：" + Thread.currentThread().getState());
        Print.cfo("当前线程优先级：" + Thread.currentThread().getPriority());
        int a = 1, b = 1;
        int c = a / b;
        anotherFun();
        Thread.sleep(10000000);
    }

    private static void anotherFun() {
        int a = 1, b = 1;
        int c = a / b;
        anotherFun2();
    }

    private static void anotherFun2() {
        int a = 1, b = 1;
        int c = a / b;
    }
}
```

在这个例子中，main方法调用了anotherFun()，然后anotherFun()又调用了anotherFun()2，这时候在**栈内存中**的大致结构就是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-4.jpg?raw=true)

可见：在线程进入main方法的时候jvm就已经给这个线程分配好了**栈帧**，进入main方法后里面存储的他的局部变量等信息，然后进入anotherFun()方法，每进入一个方法都分配对应的栈帧，然后存储变量，anotherFun()2方法也是，进入了三个方法，分配了三个栈帧，并**压入栈内存**，然后在方法结束后再**反过来释放**栈帧逐个从栈内存中**弹出**

总结：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/%E8%BF%9B%E7%A8%8B%E4%B8%8E%E7%BA%BF%E7%A8%8B%E7%9A%84%E5%8C%BA%E5%88%AB.jpg?raw=true)



**创建线程的四种方法**

**Thread类详解**

在thread类中比较重要的属性和方法有：

```java
//线程的id
private final long tid;
//线程的名字
private volatile String name;
//通过这个构造方法给线程命名
public Thread(String name)
//线程的优先级
private int priority;
//是否为守护线程
private boolean daemon = false;
```



在线程的优先级中最小为1，最大为10，默认是5：

```java
//最小
public static final int MIN_PRIORITY = 1;
//默认
public static final int NORM_PRIORITY = 5;
//最大
public static final int MAX_PRIORITY = 10;
```



**线程的状态**：

```java
private volatile int threadStatus = 0;
public enum State {
    	//新建状态
        NEW,
    	//就绪、运行
        RUNNABLE,
    	//阻塞
        BLOCKED,
    	//等待
        WAITING,
    	//计时等待
        TIMED_WAITING,
    	//结束
        TERMINATED;
    }
```

注意在java的线程状态中，就绪还有运行都用一个**runnable**来表示

* 就绪状态：线程具备运行条件，**正在等待获取CPU时间片**
* 运行状态：线程**已经获取CPU时间片**，它正在执行代码逻辑



**线程的启动和运行**

```java
//启动线程
public synchronized void start()
```

当线程的实例调用start方法后，JVM开启一个**新线程**来执行用户定义的**线程代码逻辑**，就是开启，启动一个新的线程

```java
//用户定义的线程逻辑入口方法
public void run()
```

当线程获得了CPU的时间片之后，让线程来执行用户代码逻辑的方法入口



**获取当前线程**

```java
public static native Thread currentThread();
```

通过Thread.currentThread()获取当前线程



**创建一个空的线程**

```java
public class EmptyThreadDemo {
    public static void main(String args[]) throws InterruptedException {
        //使用Thread类创建和启动线程
        Thread thread = new Thread();
        Print.cfo("线程名称：" + thread.getName());
        Print.cfo("线程ID：" + thread.getId());
        Print.cfo("线程状态：" + thread.getState());
        Print.cfo("线程优先级：" + thread.getPriority());
        thread.start();
        Print.cfo("线程状态：" + thread.getState());
        ThreadUtil.sleepMilliSeconds(10);
    }
}
```

因为这个空的线程什么也没有，所以关键的target属性就是空的



**线程创建方法一：继承Thread类创建线程**

```java
public class CreateDemo {
    public static final int MAX_TURN = 5;
    static int threadNo = 1;
    //继承thread类
    static class DemoThread extends Thread {
        //给线程添加名字
        public DemoThread() {
            super("Mall-" + threadNo++);
        }
        //重写run方法，实现用户自定义线程入口，每个线程获取自己名字5次
        public void run() {
            for (int i = 1; i < MAX_TURN; i++) {
                Print.cfo(getName() + ", 轮次：" + i);
            }
            Print.cfo(getName() + " 运行结束.");
        }
    }
    public static void main(String args[]) throws InterruptedException {
        //有点设计模式的味道，品味尼恩的代码
        Thread thread = null;
        //方法一：使用Thread子类创建和启动线程，开启两个线程
        for (int i = 0; i < 2; i++) {
            thread = new DemoThread();
            thread.start();
        }
        Print.cfo(getCurThreadName() + " 运行结束.");
    }
}
```

打印结果：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/%E7%BB%A7%E6%89%BFthread%E4%BE%8B%E5%AD%90.png?raw=true)

从上面的例子看出开启两个线程，每个线程打印自己的名字五次，当然是通过继承thread并重写run方法来自定义的程序逻辑



**线程创建方法二：实现Runnable接口创建线程目标类**

在说明这种手段进行线程的创建时，首先得了解一下Run方法的实现以及周边的代码：

```java
//Runnable接口
public interface Runnable {
    public abstract void run();
}
//这种方式创建线程需要的构造器
public Thread(Runnable target) {
    init(null, target, "Thread-" + nextThreadNum(), 0);
}
//run方法
@Override
    public void run() {
        if (target != null) {
            target.run();
        }
    }
```

当我们使用：就可以把target赋值

* public Thread(Runnable target);
* public Thread(Runnable target,String name);

通过这两种方法就可以传入target实例，然后通过这个实例就可以达到让线程运行我们自定义的run方法的逻辑了，当Runnable实例传入Thread实例的target属性后，run()的实现版本将被**异步调用**

例子：

```java
public class CreateDemo2 {
    public static final int MAX_TURN = 5;
    static int threadNo = 1;
    static class RunTarget implements Runnable  //① 实现Runnable接口
    {
        public void run()  //② 在这些写业务逻辑
        {
            for (int j = 1; j < MAX_TURN; j++) {
                Print.cfo(getCurThreadName() + ", 轮次：" + j);
            }

            Print.cfo(getCurThreadName() + " 运行结束.");
        }
    }
    public static void main(String args[]) throws InterruptedException {
        Thread thread = null;
        //方法2.1：使用实现Runnable的实现类创建和启动线程
        for (int i = 0; i < 2; i++) {
            Runnable target = new RunTarget();
            thread = new Thread(target, "RunnableThread" + threadNo++);
            thread.start();
        }
        //方法2.2：使用实现Runnable的匿名类创建和启动线程
        for (int i = 0; i < 2; i++) {
            thread = new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int j = 1; j < MAX_TURN; j++) {
                        Print.cfo(getCurThreadName() + ", 轮次：" + j);
                    }
                    Print.cfo(getCurThreadName() + " 运行结束.");
                }
            }, "RunnableThread" + threadNo++);
            thread.start();
        }
        //方法2.3：使用实现lambor表达式创建和启动线程
        for (int i = 0; i < 2; i++) {
            thread = new Thread(() ->
            {
                for (int j = 1; j < MAX_TURN; j++) {
                    Print.cfo(getCurThreadName() + ", 轮次：" + j);
                }
                Print.cfo(getCurThreadName() + " 运行结束.");
            }, "RunnableThread" + threadNo++);
            thread.start();
        }
        Print.cfo(getCurThreadName() + " 运行结束.");
    }
}
```

以上三种形式的代码都可以创建线程，只不过利用了语法糖

那么通过这种方式创建线程的优缺点是什么呢？

缺点：

* 创建的不是线程类
* 由于不是继承的关系，所以不能直接用Thread里面快捷的方法，比如getName()

优点：

* 不会受限于单继承
* 逻辑和数据的更好分离

为了透彻的体现出这个观点，书中给到了一个例子：

就是分别用这两种方式创建好了线程，然后启动：

```java
   public static void main(String args[]) throws InterruptedException {
        Print.hint("商店版本的销售");
        for (int i = 1; i <= 2; i++) {
            Thread thread = null;
            thread = new StoreGoods("店员-" + i);
            thread.start();
        }
        Thread.sleep(1000);
        Print.hint("商场的商品销售");
        MallGoods mallGoods = new MallGoods();
        for (int i = 1; i <= 2; i++) {
            Thread thread = null;
            thread = new Thread(mallGoods, "商场销售员-" + i);
            thread.start();
        }
        Print.cfo(getCurThreadName() + " 运行结束.");
    }
```

在上面的例子中这两种启动线程的方式，由于用runnable方法创建出来的线程**共用一个对象**，那么就共享一个资源，如果把**MallGoods mallGoods = new MallGoods();**写到了for循环里面的话，其实和继承的方式差不多的，我认为这种的方式更加的灵活一点，所以整体的好处就是：

* 继承Thread是一个比较“死板”的方法，每个线程“各忙各的”
* 然后实现Runnable的方式就比较的灵活，可以**控制访问共享的资源**，体现了面向对象的思想，需要使用原子变量来维护数据的一致性



**第三种创建的方式：使用Callable和FutureTask创建线程**

使用这种方式可以获取线程**异步执行结果**的**返回值**，可以采用**Callable**接口和**FutureTask**结合的方式来进行

* Callable:

```java
public interface Callable<V> {
    V call() throws Exception;
}
```

有一个call方法可以获取异步结果的返回值，并且允许方法的实现版本内部抛出异常，也就是相应异常的

* RunnableFuture:

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    /**
     * Sets this Future to the result of its computation
     * unless it has been cancelled.
     */
    void run();
}
```

很显然这个接口聚合了功能，可以作为target实例传入，又可以获取线程执行的返回结果

* future:

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

可以看到里面有5个主要的方法：

* boolean cancel(boolean mayInterruptIfRunning):取消异步任务的执行
* boolean isCancelled():判断是不是任务被取消了
* boolean isDone():判断任务是不是完成了
* V get() throws InterruptedException, ExecutionException:获取异步任务的结构，是**阻塞性的**如果异步任务没有执行完成，那么调用线程会**一直阻塞**
*  V get(long timeout, TimeUnit unit):设置等待的时限，不会一直阻塞



















































