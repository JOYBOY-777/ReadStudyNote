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



**FutureTask:**

类图大致结构：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-8.jpg?raw=true)

在这个类的内部有一个Callable类型的成员变量，通过这个成员变量可以让FutureTask获取到异步任务的返回结果，在执行run方法的时候，其实就执行了call来获取结果，还有private Object **outcome**;来保存call方法的**异步执行结果**

具体例子：

```java
public class CreateDemo3 {
    public static final int MAX_TURN = 5;
    public static final int COMPUTE_TIMES = 100000000;
    static class ReturnableTask implements Callable<Long> {
        //返回并发执行的时间
        public Long call() throws Exception {
            long startTime = System.currentTimeMillis();
            Print.cfo(getCurThreadName() + " 线程运行开始.");
            Thread.sleep(1000);
            for (int i = 0; i < COMPUTE_TIMES; i++) {
                int j = i * 10000;
            }
            long used = System.currentTimeMillis() - startTime;
            Print.cfo(getCurThreadName() + " 线程运行结束.");
            return used;
        }
    }
    public static void main(String args[]) throws InterruptedException {
        //创造出callable实例
        ReturnableTask task = new ReturnableTask();
        //把这个实例放入FutureTake中，并构造出一个FutureTask实例
        FutureTask<Long> futureTask = new FutureTask<Long>(task);
        //在把这个FutureTake实例放入Thread的构造器，创建出一个线程实例
        Thread thread = new Thread(futureTask, "returnableThread");
        thread.start();
        Thread.sleep(500);
        Print.cfo(getCurThreadName() + " 让子弹飞一会儿.");
        Print.cfo(getCurThreadName() + " 做一点自己的事情.");
        for (int i = 0; i < COMPUTE_TIMES / 2; i++) {
            int j = i * 10000;
        }
        Print.cfo(getCurThreadName() + " 获取并发任务的执行结果.");
        try {
            Print.cfo(thread.getName() + "线程占用时间：" + futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
        Print.cfo(getCurThreadName() + " 运行结束.");
    }
}
```

在这个实例执行run方法的时候：

* 实际上执行的是FutureTask中的run方法，然后这个run方法执行的是callable中的call方法
* 这个异步的结果被放在了**outcome**这个属性中
* 然后在外部调用这个类的get属性后去outcome的结果
* 当outcome不为空并且call执行完成，那么调用get获取outcome
* 如果outcome为空，并且call方法还没有执行完，那么**结果获取线程会阻塞**，直到这个线程异步任务执行完成

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-9.jpg?raw=true)



**通过线程池创建线程**

在java中创建一个线程的代价其实还是比较的大的，那么为了能够更好地回收利用这些线程资源，就引出了线程池的概念，可以用一个**静态工厂**来创建不同的线程池，静态工厂名为：**Executors**

使用例子：

```java
public class CreateDemo4 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //创建一个包含三个线程的线程池
        ExecutorService pool = Executors.newFixedThreadPool(3);
        pool.execute(new DemoThread()); //执行线程实例
        //执行Runnable执行目标实例
        pool.execute(new Runnable() {
            @Override
            public void run() {
                for (int j = 1; j < MAX_TURN; j++) {
                    Print.cfo(getCurThreadName() + ", 轮次：" + j);
                    sleepMilliSeconds(10);
                }
            }
        });
        //提交Callable 执行目标实例
        Future future = pool.submit(new ReturnableTask());
        Long result = (Long) future.get();
        Print.cfo("异步任务的执行结果为：" + result);
        sleepSeconds(Integer.MAX_VALUE);
    }
    //无返回的线程实现
    static class DemoThread implements Runnable {
        @Override
        public void run() {
            for (int j = 1; j < MAX_TURN; j++) {
                Print.cfo(getCurThreadName() + ", 轮次：" + j);
                sleepMilliSeconds(10);
            }
        }
    }
    public static final int MAX_TURN = 5;
    public static final int COMPUTE_TIMES = 100000000;
    //又返回的线程实现
    static class ReturnableTask implements Callable<Long> {
        //返回并发执行的时间
        public Long call() throws Exception {
            long startTime = System.currentTimeMillis();
            Print.cfo(getCurThreadName() + " 线程运行开始.");
            for (int j = 1; j < MAX_TURN; j++) {
                Print.cfo(getCurThreadName() + ", 轮次：" + j);
                sleepMilliSeconds(10);
            }
            long used = System.currentTimeMillis() - startTime;
            Print.cfo(getCurThreadName() + " 线程运行结束.");
            return used;
        }
    }
}
```

在以上的例子中，用Executors创建出三个线程，然后分别根据三种不同的情况来进行线程池的执行，分别接收三种不同的参数，那么submit,execute的区别是：

* 接收的参数不一样：submit接收两种入参，一个是线程的runnable实例，或者是有返回值的Callable类型的参数
* 返回值的不同：submit有返回值，但是execute没有返回值，只接收线程的实例



**线程的核心原理**：

CPU时间片：cpu每秒的计算次数非常的高，那么就以**毫秒**为单位进行分段，说白了就是**给线程分配多少秒的计算时间**

调度：基于CPU时间片进行调度，获取到时间片的线程处于运行状态，没有获取的属于就绪状态，由于调度的时间非常短，在在各个线程之间快速的切换，给人的错觉就是线程的并发执行，在java中使用的是**抢占调度模型**，而不是**分时调度模型**，根据线程的优先级分配cpu时间片

线程的优先级：默认是5，最小是1，最大是10，虽然优先级越大理论上分配时间片的机会就越多，但是不是绝对的

线程的生命周期状态：

其实一张图就可以概括这个状态：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/%E7%BA%BF%E7%A8%8B%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2.jpg?raw=true)

值的注意的就是当线程实例调用start的时候，如果线程没有获取到CPU的时间片的话，是ready状态，获取到cpu的时间片之后才是运行状态runnable



**使用Jstack查看线程状态**

可以使用jstack来把线程的堆栈打印出来，那么首先用jps来获取线程对应的线程id，之后根据jstack线程id来获取线程的堆栈信息：那么会有大概这几点比较重要的信息

* tid:在jvm中的线程id
* nid:在操作系统中的线程id
* prio:在jvm中的优先级
* os_prio:在操作系统中对应的优先级
* 以及线程的状态



**线程的sleep状态**

当Thread调用sleep方法的时候，线程会进入**显示等待的状态**。但是他并不释放线程的锁，而且对CPU也不是完全的占用（当多个线程的sleep时间较大时）



**线程的interrupt操作**

首先明确的是，这个interrupt只是给线程设置一个中断的标志位（变为true），并不是像stop一样会把正在运行的线程直接停掉，当线程处在等待状态的时候，线程实例调用interrupt后会直接抛出异常（前提是在线程的run方法捕获了异常）比如：

```java
   	public static final int SLEEP_GAP = 5000;//睡眠时长
    public static final int MAX_TURN = 50;//睡眠次数
    static class SleepThread extends Thread {
        static int threadSeqNumber = 1;
        public SleepThread() {
            super("sleepThread-" + threadSeqNumber);
            threadSeqNumber++;
        }
		//在run方法中对异常进行捕获,如果遇到interrupt中断会进行异常的中断
        public void run() {
            try {
                Print.tco(getName() + " 进入睡眠.");
                // 线程睡眠一会
                Thread.sleep(SLEEP_GAP);
            } catch (InterruptedException e) {
                e.printStackTrace();
                Print.tco(getName() + " 发生被异常打断.");
                return;
            }
            Print.tco(getName() + " 运行结束.");
        }
    }
```

还有一种情况就是，线程正在处于**运行中**，那么这个线程只是打上了一个标记为true,那么怎么知道实例在main方法中被interrupt之后，这个线程要打断呢？其实可以在线程的run方法中加一个isinterrupt()的验证：

```java
 //测试用例：获取异步调用的结果
    @Test
    public void testInterrupted2() {
        Thread thread = new Thread() {
            public void run() {
                Print.tco("线程启动了");
                //一直循环
                while (true) {
                    Print.tco(isInterrupted());
                    sleepMilliSeconds(SLEEP_GAP);

                    //如果调用 interrupt 为true，退出死循环
                    if (isInterrupted()) {
                        Print.tco("线程结束了");
                        return;
                    }
                }
            }
        };
        thread.start();
        sleepSeconds(2);//等待2秒
        thread.interrupt(); //打断线程1
        sleepSeconds(2);//等待2秒
        thread.interrupt();
    }
```

线程在while循环中不断的循环检查自己是否在外部被别的线程打断了，如果检查到了这个那么就直接进行打断



**线程的join操作**

其实join就是对线程进行合并的操作，比如在线程A中调用B.join，那么这个A线程就被称之为**甲方线程**，而B线程称为**乙方线程**，那么在A线程的这个执行流程里面就把B线程的执行例程给拉来了，如果join线程被主线程调用的话，那么主线程就是甲方线程

join的重载方法可以限时合并，也可以不限时合并

**值的注意的是**：如果在syn锁里面用join的话，那么就需要注意了，因为join是实例方法，底层调用的是wait方法，其实他阻塞的还是Thread的这个实例，如果你的syn锁的是别的对象，那么这个阻塞的操作时候没有用的



**线程的yield操作**

这个方法是Thread类的一个静态方法，就是让当前的线程放弃cpu的时间片，让这个时间片去供给别的线程，然后这个线程的状态还是runnable，但是他对应的操作系统线程变为就绪状态，这个方法的作用是：

```java
    static class YieldThread extends Thread {
        static int threadSeqNumber = 1;
        public YieldThread() {
            super("YieldThread-" + threadSeqNumber);
            threadSeqNumber++;
            metric.put(this.getName(), new AtomicInteger(0));
        }
		//在线程的run方法中调用yield方法，执行后就会放弃cpu时间片
        public void run() {
            for (int i = 1; i < MAX_TURN && index.get() < MAX_TURN; i++) {
                Print.tco("线程优先级：" + getPriority());
                index.incrementAndGet();
                metric.get(this.getName()).incrementAndGet();
                if (i % 2 == 0) {
                    //让步：出让执行的权限
                    Thread.yield();
                }
            }
            //输出线程的执行次数
            printMetric();
            Print.tco(getName() + " 运行结束.");
        }
    }
```

个人感觉这个方法用处不是很大



**守护线程daemon**

顾名思义，负责垃圾回收的线程就是一个守护线程，我们可以通过外部的方法把一条用户线程变为守护线程，当用户线程结束完毕之后，JVM进程也随之结束之后，不管你的守护线程还在不在运行中，这个守护线程也要强制结束

```java
 static class DaemonThread extends Thread {
        public DaemonThread() {
            super("daemonThread");
        }
        public void run() {
            Print.synTco("--daemon线程开始.");
            //这是个死循环，但是由于在外部设置了守护线程，那么在用户线程结束后，jvm进程也就结束了，会强制你守护线程结束
            for (int i = 1; ; i++) {
                Print.synTco("--轮次：" + i + "--守护状态为:" + isDaemon());
                // 线程睡眠一会
                sleepMilliSeconds(SLEEP_GAP);
            }
        }
    }
```

还有就是守护线程创建的线程也属于守护线程



**线程池原理与实践**

java中高并发应用频繁创建和销毁线程的操作是非常的低效的，那么就必须使用线程池来合理的管理和分配这些线程资源

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-14.png?raw=true)

* Executor:是一个接口主要提供了执行的功能规范

  ```java
  void execute(Runnable command)
  ```

* ExecutorService:这个接口是Executor的子接口，他提供了获取任务执行结果submit方法和invoke等方法

* AbstractExecutorService:是一个抽象类，他为上面的ExecutorService提供了默认的实现

* ThreadPoolExecutor:线程池的实现类，他是继承于AbstractExecutorService这个抽象类的

* ScheduledExecutorService:也是一个接口，他继承于ExecutorService并且提供了“延
  时，周期性”任务的调度线程池接口

* ScheduledThreadPoolExecutor:继承于线程池实现类ThreadPoolExecutor，并且为延时，周期性等接口提供了默认实现

* Executors:是一个静态工厂方法，通过这个方法可以创建线程池



**Executors 四个快捷创建线程池方法**

* newSingleThreadExecutor() ：创建一个线程池，只有一个线程来执行任务，这样保证任务是先入先出的执行

  ```java
  //异步的执行目标类
      public static class TargetTask implements Runnable {
          static AtomicInteger taskNo = new AtomicInteger(1);
          protected String taskName;
          public TargetTask() {
              taskName = "task-" + taskNo.get();
              taskNo.incrementAndGet();
          }
          public void run() {
              Print.tco("任务：" + taskName + " doing");
              // 线程睡眠一会
              sleepMilliSeconds(SLEEP_GAP);
              Print.tco(taskName + " 运行结束.");
          }
          @Override
          public String toString() {
              return "TargetTask{" + taskName + '}';
          }
      }
  ```

  调用线程池**让里面的线程**去执行这个异步任务：

  ```java
  //测试用例：只有一条线程的线程池
      @Test
      public void testSingleThreadExecutor() {
          ExecutorService pool = Executors.newSingleThreadExecutor();
          for (int i = 0; i < 5; i++) {
              pool.execute(new TargetTask());
              pool.submit(new TargetTask());
          }
          sleepSeconds(1000);
          //关闭线程池
          pool.shutdown();
      }
  ```

  从例子中就可以看出，这个类型的线程池里面只有一个线程，这个线程**一个一个**去执行异步的任务

  * 按照**提交的顺序执行**
  * 池中的线程的存活时间是无限的
  * 当池中的线程正在执行任务的时候，新提交的任务会进入**池中的无界阻塞队列**
  * 应用的场景是：任务按照提交次序，一个任务一个任务逐个执行

  

* newFixedThreadPool(int threads) ：创建拥有固定大小线程数的线程池

  ```java
      //测试用例：只有3条线程固定大小的线程池
      @Test
      public void testNewFixedThreadPool() {
          ExecutorService pool = Executors.newFixedThreadPool(3);
          for (int i = 0; i < 5; i++) {
              pool.execute(new TargetTask());
              pool.submit(new TargetTask());
          }
          sleepSeconds(1000);
          //关闭线程池
          pool.shutdown();
      }
  ```

  这个代码中向线程池中提交了三个任务，但是这个线程池中只能有三个线程同时执行任务，其余的任务会排队你等待，这个线程池的主要特点是：

  * 线程数量**没有**达到固定数量，那么每次提交一个任务的话，就创建一个线程执行这个任务
  * 如果达到了固定的数量，那么就会线程数量就会保持不变，如果有的线程因为异常而结束，就会在补充一个线程
  * 当线程的数量达到规定的峰值那么其他的异步任务就会放到阻塞队列中

  适用场景：

  * 需要任务长期执行（长期有任务得执行着）
  * 用来处理CPU密集型的任务，可以保证cpu在长时间的使用下**尽可能少的分配系统资源**

  

* newCachedThreadPool() ：创建一个拥有n个线程数量的线程池，并且提交的任务立即执行，但是空闲的线程会及时的回收

  ```java
      //测试用例：“可缓存线程池”
      @Test
      public void testNewCacheThreadPool() {
          ExecutorService pool = Executors.newCachedThreadPool();
          for (int i = 0; i < 5; i++) {
              pool.execute(new TargetTask());
              pool.submit(new TargetTask());
          }
          sleepSeconds(1000);
          //关闭线程池
          pool.shutdown();
      }
  ```

  主要特点：

  * 因为这个线程池里面有n个线程，当池内的线程繁忙的时候，会立刻创建一个线程去执行这个任务
  * 当需要执行的异步任务数量<线程的数量时，就会及时的回收那些空闲的线程

  使用场景：

  * 用来处理**突发性强，任务耗时短的异步任务**
  * 不好的地方是：如果一次性的异步任务提交的特别多，那么系统的资源就会快速的消耗殆尽

  

* newScheduledThreadPool() ：创建一个可定期或者延迟执行任务的线程池

  注意：这个是让异步任务重复的并且有周期性的执行，并没有停下的意思，一直重复的执行

  ```java
  //测试用例：“可调度线程池”
      @Test
      public void testNewScheduledThreadPool() {
          ScheduledExecutorService scheduled = Executors.newScheduledThreadPool(2);
          //这个是创建几个异步任务给池子里面的线程，并且线程会按照配置的参数重复性的有周期性的执行任务
          for (int i = 0; i < 2; i++) {
              scheduled.scheduleAtFixedRate(new TargetTask(),
                      0, 500, TimeUnit.MILLISECONDS);
              //以上的参数中：
              //new TargetTask()表示需要执行的目标实例
              // 0表示首次执行任务的延迟时间，500表示每次执行任务的间隔时间
              //TimeUnit.MILLISECONDS所设置的时间的计时单位为毫秒
          }
          sleepSeconds(1000);
          //关闭线程池
          scheduled.shutdown();
      }
  ```

  有两个常用的方法：

  ```java
  public ScheduledFuture<?> scheduleAtFixedRate(
      Runnable command, //异步任务 target 执行目标实例；
      long initialDelay, //首次执行延时；
      long period, //两次开始执行最小间隔时间；
      TimeUnit unit //所设置的时间的计时单位，如 TimeUnit.SECONDS 常量；
  )
      
  public ScheduledFuture<?> scheduleWithFixedDelay(
      Runnable command,//异步任务 target 执行目标实例；
      long initialDelay, //首次执行延时；
      long delay, //前一次执行结束到下一次执行开始的间隔时间（间隔执行延迟时间）；
      TimeUnit unit //所设置的时间的计时单位，如 TimeUnit.SECONDS 常量；
  )
  ```

  注意period和delay的意思，前者是**间隔的最小时间**，后者是真正的**指定的间隔时间**，并且当异步任务的执行时间>指定的间隔时间时，系统不会创建一个新的线程去执行这个新的任务，而是等这个任务执行结束



**线程池的标准创建方式**

```java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) 
```

这七个参数分别是：

* int corePoolSize：这个是线程池的**核心线程数量**，当有任务进入到线程池中，并且线程池中工作线程数<核心线数量的话，就算工作线程处在空闲状态，也会**新创建**一个线程来执行这个任务，直到数量达到核心线程数，**核心线程数，即使线程空闲（Idle），也不会回收**
* int maximumPoolSize：池中的**最大线程数**，当工作线程数量>核心线程数量但是小于池中可以容纳的最大线程数的时候，当阻塞队列(当核心线程都在忙，那么这时候新提交的任务就会进入阻塞队列)已满时才会创建新线程
* long keepAliveTime：线程最大空闲（Idle）时长，超过这个最大的空闲时间**非核心线程就会阻塞**
* TimeUnit unit：空闲时长的单位
* BlockingQueue<Runnable> workQueue：阻塞队列的类型
* ThreadFactory threadFactory：线程创建方法
* RejectedExecutionHandler handler：拒绝新任务的策略

下面是submit的两个例子：

因为submit可以获取异步任务的返回结果,并且可以响应异常举个栗子：

```java
//测试用例：提交和执行
    @Test
    public void testSubmit() {
        ScheduledExecutorService pool = Executors.newScheduledThreadPool(2);
        pool.execute(new TargetTaskWithError());
        /**
         * submit(Runnable x) 返回一个future。可以用这个future来判断任务是否成功完成。请看下面：
         */
        Future future = pool.submit(new TargetTaskWithError());
        try {
            //如果异常抛出，会在调用Future.get()时传递给调用者
            if (future.get() == null) {
                //如果Future的返回为null，任务完成
                Print.tco("任务完成");
            }
            //对异步任务中的异常进行捕获
        } catch (Exception e) {
            Print.tco(e.getCause().getMessage());
        }
        sleepSeconds(10);
        //关闭线程池
        pool.shutdown();
    }

//测试用例：获取异步调用的结果
    @Test
    public void testSubmit2() {
        ScheduledExecutorService pool = Executors.newScheduledThreadPool(2);
        Future<Integer> future = pool.schedule(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                //返回200 - 300 之间的随机数
                return RandomUtil.randInRange(200, 300);
            }
        }, 100, TimeUnit.MILLISECONDS);
        try {
            //通过future获取异步调用的结果
            Integer result = future.get();
            Print.tco("异步执行的结果是:" + result);
        } catch (InterruptedException e) {
            Print.tco("异步调用被中断");
            e.printStackTrace();
        } catch (ExecutionException e) {
            Print.tco("异步调用过程中，发生了异常");
            e.printStackTrace();
        }
        sleepSeconds(10);
        //关闭线程池
        pool.shutdown();
    }
```



**线程池的任务调度流程**

总的一张图是：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-15.png?raw=true)

需要注意的是：核心和最大线程数量、BlockingQueue 队列等参数配置的不好的话会造成严重的排队等待的现象发生，总的原因就是：当核心线程的数量已满，**还要保证阻塞队列已满**才会分配非核心线程去执行异步任务



**ThreadFactory线程工厂**

如果你设置了线程工厂的话，你就可以自定义的设置线程的名字等功能，比如：

```java
    //一个简单的线程工厂
    static public class SimpleThreadFactory implements ThreadFactory {
        static AtomicInteger threadNo = new AtomicInteger(1);
        //实现其唯一的创建线程方法
        @Override
        public Thread newThread(Runnable target) {
            String threadName = "simpleThread-" + threadNo.get();
            Print.tco("创建一条线程，名称为：" + threadName);
            threadNo.incrementAndGet();
            //设置线程名称
            Thread thread = new Thread(target, threadName);
            //设置为守护线程
            thread.setDaemon(true);
            return thread;
        }
    }

//使用这个自定义线程工厂
    @org.junit.Test
    public void testThreadFactory() {
        //使用自定义线程工厂，快捷创建线程池
        ExecutorService pool =
                Executors.newFixedThreadPool(2, new SimpleThreadFactory());
        for (int i = 0; i < 5; i++) {
            pool.submit(new TargetTask());
        }
        //等待10秒
        sleepSeconds(10);
        Print.tco("关闭线程池");
        pool.shutdown();
    }
```

你用自定义的类实现接口中的方法，然后new出一个线程用这个线程的实例就可以随意操作了



**任务阻塞队列**

这个阻塞队列是用来存放**异步任务的**，当这个里面没有异步任务的时候，线程从队列里面获取任务执行的时候，如果队列是空的，那么这个线程就会被阻塞，当队列中有任务的时候线程就会被唤醒，这个过程**用户不会察觉**

线程池参数中的BlockingQueue有以下这几个比较重要的实现类:

* ArrayBlockingQueue:是一个用**数组**实现的**有界**的阻塞队列，在创建的时候就**必须设置队列的大小**，并且遵从FIFO的顺序排序
* LinkedBlockingQueue:是一个用**链表**实现的阻塞队列，你可以不设置队列的大小，不设置的话当任务数量超出核心线程数的时候，其余的任务直接就放在了这个队列中，直到资源耗尽，Executors.newSingleThreadExecutor、Executors.newFixedThreadPool使用了这个
* PriorityBlockingQueue：具有优先级的无界队列
* DelayQueue：是无界阻塞延迟队列，每个元素都有过期的时间，并且头部过期最快 Executors.newScheduledThreadPool 使用了这个创建
* SynchronousQueue：是一个不存储元素的阻塞队列，如果想往这个阻塞队列中增加任务，必须有另一个线程来执行任务才能插入成功，不会保存提交的任务，而是新建一个线程来执行这个任务



**调度器的钩子方法**

这个有点类似于AOP的概念，当线程池执行异步任务的时候，通过钩子方法可以在执行前后停止这三个时间段“织入”代码例子：

```java
@org.junit.Test
    public void testHooks() {
        ExecutorService pool = new ThreadPoolExecutor(2,
                4, 60,
                TimeUnit.SECONDS, new LinkedBlockingQueue<>(2)) {
            @Override
            protected void terminated() {
                Print.tco("调度器已经终止!");
            }
            @Override
            protected void beforeExecute(Thread t, Runnable target) {
                Print.tco(target + "前钩子被执行");
                //记录开始执行时间
                START_TIME.set(System.currentTimeMillis());
                super.beforeExecute(t, target);
            }
            @Override
            protected void afterExecute(Runnable target, Throwable t) {
                super.afterExecute(target, t);
                //计算执行时长
                long time = (System.currentTimeMillis() - START_TIME.get());
                Print.tco(target + " 后钩子被执行, 任务执行时长（ms）：" + time);
                //清空本地变量
                START_TIME.remove();
            }
        };
```

通过重写这三个方法进行异步任务执行时的“织入操作”，在最后执行结果出来的时候就会发现织入了这些代码



**线程池的拒绝策略**

那种情况下会使得线程池触发拒绝策略呢？

* 线程池已经被关闭
* 工作队列已满并且允许的最大线程数已满

咱们的JDK提供了这几种拒绝策略：

* AbortPolicy：拒绝策略

  是线程池默认的拒绝策略，在池中线程数已满的情况下，如果这时候再有任务进来的话就会抛出异常

* DiscardPolicy：抛弃策略，这个是不抛出异常的版本

* DiscardOldestPolicy：抛弃最老任务策略，把对头的任务给放弃，因为是最老的任务，然后在尝试把任务加入阻塞队列

* CallerRunsPolicy：调用者执行策略，在新加入任务失败以后，就让**提交任务线程**去执行这个任务，不使用池中的队列

* 自定义策略:

  ```java
      //自定义拒绝策略
      public static class CustomIgnorePolicy implements RejectedExecutionHandler {
          public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
              // 可做日志记录等
              Print.tco(r + " rejected; " + " - getTaskCount: " + e.getTaskCount());
          }
      }
  ```

  这个在发生拒绝策略的时候就默认走这个自定义的拒绝策略了



**线程池的优雅关闭**

线程池的5种状态：

* RUNNING：这是线程池创建初的状态，已经可以执行任务了
* SHUTDOWN：不在接收新的任务，但是会把线程池里面的异步任务消耗干净
* STOP：不接收新的任务和消耗线程池里面的任务
* TIDYING：是一个完美的状态，里面的任务都执行完毕了或者终止，将会执行**terminated( )**
* TERMINATED：执行完terminated( )的状态

shutdown:

```java
    public void shutdown() {
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try {
            //检查权限
            checkShutdownAccess();
            //设置线程状态
            advanceRunState(SHUTDOWN);
            //中断空闲线程
            interruptIdleWorkers();
            //执行钩子函数
            onShutdown(); // hook for ScheduledThreadPoolExecutor
        } finally {
            mainLock.unlock();
        }
        tryTerminate();
    }

```

shutdownNow:

```java
    public List<Runnable> shutdownNow() {
        List<Runnable> tasks;
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try {
            checkShutdownAccess();
            advanceRunState(STOP);
            //中断所有线程
            interruptWorkers();
            //放弃工作队列中的剩余任务
            tasks = drainQueue();
        } finally {
            mainLock.unlock();
        }
        tryTerminate();
        return tasks;
    }
```

awaitTermination ：在调用shutdown或者shutdownnow之后，一般主线程（用户线程）不会等待线程池结束，这个方法就让工作线程主动地等待线程池结束，线程池完成关闭后这个方法返回true

优雅关闭线程池：

```java
    //测试用例：优雅关闭
    @Test
    public void testShutdownGracefully() {
        ScheduledExecutorService threadPool = Executors.newScheduledThreadPool(2);
        threadPool.shutdown(); // Disable new tasks from being submitted
        try {
            // 设定最大重试次数
            // 等待 60 s，如果等了60 s还不关闭的话，就直接结束
            if (!threadPool.awaitTermination(60, TimeUnit.SECONDS)) {
                // 调用 shutdownNow 取消正在执行的任务
                threadPool.shutdownNow();
                // 再次等待 60 s，如果还未结束，可以再次尝试，或则直接放弃
                if (!threadPool.awaitTermination(60, TimeUnit.SECONDS)) {
                    System.err.println("线程池任务未正常执行结束");
                }
            }
        } catch (InterruptedException ie) {
            // 重新调用 shutdownNow
            threadPool.shutdownNow();
        }
    }
```



**注册 JVM 钩子函数自动关闭线程池**

在**JVM进程关闭**之前，由钩子函数**自动**将线程池优雅的关闭，也就是结束运行的时候自动给你执行钩子函数

```java
        //JVM关闭时的钩子函数
        Runtime.getRuntime().addShutdownHook(
                new ShutdownHookThread("IO密集型任务线程池", new Callable<Void>() {
                    @Override
                    public Void call() throws Exception {
                        //优雅关闭线程池
                        shutdownThreadPoolGracefully(EXECUTOR);
                        return null;
                    }
                }));
```

可以看到其实这个钩子函数里面也是一个线程，是有返回值的，重写了call方法



**Executors快捷创建线程池的问题**

* 使用 newFixedThreadPool 工厂方法存在的问题：存在于他使用的阻塞队列：LinkedBlockingQueue<Runnable>() 上，由于可以提交的异步任务不设置限制，这时候如果**提交的速度大于执行的速度**就会造成资源的浪费
* 使用 newSingleThreadExecutor存在的问题：其实问题还是发生在这个采用的阻塞队列中，**提交速度大于消耗任务的速度就会造成资源的浪费**
* 使用 newCachedThreadPool 工厂方法问题：存在于其**最大线程数量不设限上**，可以认为是无限次数的创建线程，提交过多的话会造成大量的线程被启动从而导致OOM
* Executors 的 newScheduledThreadPool 工厂方法：任务的阻塞队列不设限制，无限的加任务导致oom资源浪费



**确定线程池的线程数**

使用线程池的好处：

1. 降低资源的消耗
2. 提高响应速度
3. 提高线程的可管理性



**任务类型对应不同的线程池种类**

我们一般去运用线程池去执行异步任务，但是根据异步任务的类型，有一下三类：

* IO密集型任务：这种的任务主要是执行IO操作，并且这类任务所要执行的时间比较**长**，但是CPU的利用率比较小，比较经典的例子就是Netty的IO读写，这类任务的cpu长期属于**空闲状态**
* CPU密集型任务：这种主要是需要进行频繁的计算操作，所以cpu的利用率还是比较的高的
* 混合型任务：就是既需要进行IO的磁盘操作，还要进行频繁的计算任务

**为 IO 密集型任务确定线程数**

因为IO密集型任务对于CPU的消耗不严重，cpu利用率低，所以就需要把线程数量设置为**cpu核心线程数的2倍**，核心来提高**CPU的使用率**

```java
//CPU核心线程数
public static final int CPU_COUNT = Runtime.getRuntime().availableProcessors();
//求CPU核心线程数的二倍
public static final int IO_MAX = Math.max(2, CPU_COUNT * 2);
//懒汉式单例创建线程池：用于IO密集型任务
public class IoIntenseTargetThreadPoolLazyHolder {
    //线程池： 用于IO密集型任务
    public static final ThreadPoolExecutor EXECUTOR = new ThreadPoolExecutor(
            IO_MAX,
            IO_MAX,
            KEEP_ALIVE_SECONDS,
            TimeUnit.SECONDS,
        	//1000个有界队列
            new LinkedBlockingQueue(QUEUE_SIZE),
            new ThreadUtil.CustomThreadFactory("io"));
    public static ThreadPoolExecutor getInnerExecutor() {
        return EXECUTOR;
    }
    //钩子函数
    static {
        log.info("线程池已经初始化");
        //把线程空闲时间的范畴拉大到核心线程，使得核心线程超过最大空闲时间时也被回收
        EXECUTOR.allowCoreThreadTimeOut(true);
        //JVM关闭时的钩子函数
        Runtime.getRuntime().addShutdownHook(
                new ShutdownHookThread("IO密集型任务线程池", new Callable<Void>() {
                    @Override
                    public Void call() throws Exception {
                        //优雅关闭线程池
                        shutdownThreadPoolGracefully(EXECUTOR);
                        return null;
                    }
                }));
    }
}
```

注意：这里让 corePoolSize 、 maximumPoolSize的值相等是为了**尽可能的增加CPU的利用率**，关于书里面提到的可以让非核心线程直接去执行异步任务的说法我抱有一点疑问，不加入阻塞队列的话就直接让非核心线程去消耗异步任务的做法很显然是要执行拒绝策略的，所以书里的说法我不是很认同，这里使用的是懒汉式设计模式创建的线程池，当使用的时候创建线程池

**为CPU 密集型任务确定线程数**

对于这种类型的线程池来说的话，就让核心线程数还有最大线程数等于CPU的**核心数**就行了，这样做的目的是尽可能的**最高效的利用CPU**

```java
//cpu核心数
public static final int CPU_COUNT = Runtime.getRuntime().availableProcessors();
public static final int MAXIMUM_POOL_SIZE = CPU_COUNT;
//懒汉式单例创建线程池：用于CPU密集型任务
public class CpuIntenseTargetThreadPoolLazyHolder {
    //线程池： 用于CPU密集型任务
    private static final ThreadPoolExecutor EXECUTOR = new ThreadPoolExecutor(
            MAXIMUM_POOL_SIZE,
            MAXIMUM_POOL_SIZE,
            KEEP_ALIVE_SECONDS,
            TimeUnit.SECONDS,
            new LinkedBlockingQueue(QUEUE_SIZE),
            new CustomThreadFactory("cpu"));
    public static ThreadPoolExecutor getInnerExecutor() {
        return EXECUTOR;
    }
    static {
        log.info("线程池已经初始化");

        EXECUTOR.allowCoreThreadTimeOut(true);
        //JVM关闭时的钩子函数
        Runtime.getRuntime().addShutdownHook(
                new ShutdownHookThread("CPU密集型任务线程池", new Callable<Void>() {
                    @Override
                    public Void call() throws Exception {
                        //优雅关闭线程池
                        shutdownThreadPoolGracefully(EXECUTOR);
                        return null;
                    }
                }));
    }
}
```



**为混合型任务确定线程数**

对于混合型的任务CPU的利用率不是太高，可以根据一个公式来推断出所需要的核心线程数量大小：

```java
最佳线程数目 =（线程等待时间与线程 CPU 时间之比 + 1）* CPU 核数
```

比如线程CPU运行时间是100s,线程等待时间是900s那么这个公式就是：[(900s/100s)+1]*8 = 80,一般多线程的场景就是存在相当比例的**非CPU耗时操作**，比如IO、网络操作等，那么这时候就要**提升CPU的利用率**，上面的80就是整体的线程数，核心线程数和最大线程数都是这个，后续还会调用JVM的钩子方法，其实基本上都是差不多的，就不举例了



**ThreadLocal 原理与实战**

为保证多个线程对变量的**安全访问**，线程可以将变量放到ThreadLocal的对象中，这样的话就保证**变量**在每个线程中都有独立的值，可以想象ThreadLocal是一个收容所，线程是父母，把变量这个孩子放到ThreadLocal里面，可以理解为是**线程的后花园**



**ThreadLocal的基本使用**

ThreadLocal保存着**专属于线程的本地变量**，在多线程并发操作线程的本地变量的时候，实际上操作的是线程本地的值，避免了线程安全问题，记住一点就是ThreadLocal代表的是**线程本地变量**

在早期的ThreadLocal中，当线程从本地的变量取值的时候，他自身的这个Thread实例就是作为KEY，然后设置的值为VALUE，这个结构就相当于一个map：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-18.png?raw=true)

下面是一个使用的例子：我们着重看这个例子的Run方法,也就是线程要干什么

```java
    public void testThreadLocal() throws InterruptedException {
        //获取自定义的混合线程池
        ThreadPoolExecutor threadPool = ThreadUtil.getMixedTargetThreadPool();
        //共5个线程
        for (int i = 0; i < 5; i++) {
            threadPool.execute(new Runnable() {
                @Override
                public void run() {
                    //获取“线程本地变量”中当前线程所绑定的值
                    if (LOCAL_FOO.get() == null) {
                        //设置“线程本地变量”中当前线程所绑定的值
                        LOCAL_FOO.set(new Foo());
                    }
                    Print.tco("初始的本地值：" + LOCAL_FOO.get());
                    //每个线程执行10次
                    for (int i = 0; i < 10; i++) {
                        Foo foo = LOCAL_FOO.get();
                        foo.setBar(foo.getBar() + 1);
                        sleepMilliSeconds(10);
                    }
                    Print.tco("累加10次之后的本地值：" + LOCAL_FOO.get());

                    //删除“线程本地变量”中当前线程所绑定的值，对于线程池中的线程尤其重要
                    LOCAL_FOO.remove();
                }
            });
        }
        ThreadUtil.sleepMilliSeconds(Integer.MAX_VALUE);
    }
```

可以看到每个异步任务在执行自己的run方法的时候从ThreadLocal中添加了值，然后在轮询过后再从ThreadLocal中取值，这一系列丝滑的操作



**ThreadLocal使用场景**

其实使用这个技术的目的跟给并发线程加锁来解决并发问题是差不多的，并且更简单，更方便

使用例子：

* 线程隔离

  在ThreadLocal中的数据只属于是**当前线程**那么在多线程下可以保证自己的变量不被其他的线程修改，一般的应用就是给每一个线层分配一个数据库的连接，线程独享数据库连接，或者是保存会话Session，这是相对于**不同的线程**

  使用场景:

  ```java
  private static final ThreadLocal threadSession = new ThreadLocal();
  public static Session getSession() throws InfrastructureException {
  Session s = (Session) threadSession.get();
  try {
  	if (s == null) {
  		s = getSessionFactory().openSession();
  		threadSession.set(s);
  	}
  } catch (HibernateException ex) {
  t	hrow new InfrastructureException(ex);
  }
  		return s;
  }
  ```

  以上的例子中session代表数据库连接，那么这个session显然就是线程独享的，可以做到线程的安全连接

* 跨函数传递数据

  对于同一个线程相互之间的数据传递时会需要用到返回值和参数，那么可以通过TL进行保存，对于**同一线程**来说在某地方进行设置，在随后的任意地方都可以获取到，我的理解是在一个函数中对TL变量复制后，在后面任意的地方可以对TL中的变量取出来



**ThreadLocal 内部结构演进**

在早期的TL内部有map实例，也就是每个TL实例对应一个map实例，里面存储的是线程的实例(KEY)和他存储的值(VALUE)的键值对关系：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/TL.png?raw=true)

其实这个内部的map结构在处理Hash冲突的时候，采用的方式与hashmap不一致，他采用一种叫做**开放地址**法的方式指的是**发生了碰撞的话按照某种方法继续寻找Hash表中的其他存储单元，直到找到空位置**，可以看到多个线程共用一个Map结构

JDK8后的版本内部的结构发生了转变，因为是hash表所以就存在**扩容性能问题**，比如我们给一个线程创建多个ThreadLocal实例的时候，按照上面的map结构里面的key都是一样的，就会较快的发生hash冲突，于是为了减少扩容的成本，这个map的拥有者发生了变化，为**Thread**实例本身，Thread实例中的KEY为**创建的ThreadLocal实例**，VALUE为**线程设置的值**这样就算你给一个线程创建很多的ThreadLocal，那么hash冲突也是可以避免的，这样的性能损耗就减少很多，所以可以知道往往一个系统中几百条线程，几个threadlocal实例是很正常的，所以用下面的这种结构就能减少k-v的数量，如图：

![](https://github.com/JOYBOY-777/ReadStudyNote/blob/main/javaimg/java%E9%AB%98%E5%B9%B6%E5%8F%91%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%E5%8D%B7%E4%BA%8C%E5%9B%BE%E7%89%87/1-19.png?raw=true)

这种方式的优势为：

1. 每个 ThreadLocalMap 存储的“Key-Value 对”数量变少，因为不跟线程实例作为key强相关了
2. 早期版本中thread实例销毁后theradlocalmap结构存在，后面的结构就不用了



**ThreadLocal源码分析**

* set方法
* get方法
* remove方法
* initialValue方法

**ThreadLocalMap源码分析**































































