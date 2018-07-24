### 线程建议---启动线程前stop方法是不可靠的
```
class Client {
    public static void main(String[] args) {
        while (true) {//部分昼夜分制造垃圾短信
            SpamMachine sm = new SpamMachine();
            if (true) {
                sm.stop();//将线程停止，
            }
            sm.start();//如果线程是stop状态，则不会启动，但是却一直在制造大量的短信
        }
    }
}

class SpamMachine extends Thread {
    @Override
    public void run() {
        super.run();
        System.out.print("大量制造短信");
    }
}
```
+ `所有的线程在启动前都执行stop方法，虽然它是一个已经过时的方法，但它的运行逻辑还是正常的，况且stop方法在此处的目的并不是停止一个线程，而是设置线程为不可启动状态，但是运行结果却出现了奇怪的现象，部分线程还是启动了，也就是在某些线程（没有规律）中的start方法正常执行了`。这是因为线程启动start方法有一个缺陷，Thread类的stop方法会根据线程状态来判断是终结线程还是设置线程为不可运行状态，对于未启动的线程（线程状态为NEW）来说，会设置其标志位为不可启动，而其他的状态则是直接停止`；以下是修改；
```
class Client {
    public static void main(String[] args) {
        while (true) {//部分昼夜分制造垃圾短信
            if (true) {
                new SpamMachine().start();
            }
        }
    }
}

class SpamMachine extends Thread {
    @Override
    public void run() {
        super.run();
        System.out.print("大量制造短信");
    }
}
```
+ `不再使用stop方法进行状态的设置`，直接通过判断条件来决定线程是否可启动，对于start方法的该缺陷，一般不会引起太大的问题，只是增加了线程启动和停止的精度而已；

### 线程建议---不使用stop方法停止线程
+ 线程启动完毕后，在运行时可能需要终止，Java提供的终止方法只有一个stop，但是不建议使用这个方法，原因如下；
#### stop方法是过时的
+ 从Java编码规则来说，已经过时的方法不建议采用；
#### stop方法会导致代码逻辑不完整
+ stop方法是一种恶意的中断，一旦执行stop方法，即终止当前正在运行的线程，不管线程逻辑是否完整；
```
class Client {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(){
            @Override
            public void run() {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.print("此处代码不会执行");
            }
        };
        thread.start();
        Thread.sleep(100);
        thread.stop();
    }
}
```
+ 这段代码的逻辑是这样的：子线程是一个匿名内部类，它的run方法在执行时会休眠一秒，然后再执行后续的逻辑，而主线程则是休眠0.1秒后终止子线程的运行，也就是说，JVM在执行thread.stop方法时，子线程还在执行sleep方法，此时stop方法会清除栈内消息，结束该线程，这也就导致了run方法的逻辑不完整，输出语句println代表的逻辑不会被执行；stop连基本的逻辑完整性都无法保证，而且此操作也是非常隐蔽的，子线程执行到何处会被关闭很难定位，这为以后的维护带来了很多麻烦；
#### stop方法会破坏原子逻辑
+ 多线程为了解决共享资源抢占的问题，使用了锁概念，避免资源不同步，但是正因为此原因，stop方法却会带来更大的麻烦，他会丢弃所有的锁，导致原子逻辑受损；
```
class Client {
    public static void main(String[] args) throws InterruptedException {
        MultiThread t = new MultiThread();
        Thread t1 = new Thread(t);
        t1.start();
        for (int i = 0; i < 5; i++) {
            new Thread(t).start();
        }
        t1.stop();
    }
}

class MultiThread implements Runnable {
    int a = 0;
    @Override
    public void run() {
        synchronized ("") {
            a++;
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            a--;
            String tn = Thread.currentThread().getName();
            System.out.print(tn + "a:" + a);
        }
    }
}
//运行结果是不同的线程ID，但是输出的a的值都是1
```
+ 首先要说明的是所有线程共享了一个MultiThread的实例变量t，其次由于在run方法中加入了同步代码块，所以只能有一个线程进入到synchronized块中；该段代码的执行顺序为：线程t1启动，并执行run方法，由于没有其他线程持同步代码块的锁，所以ti线程执行后执行到sleep方法即开始休眠，此时a为1；JVM又启动了5个线程，也同时运行run方法，由于synchronized关键字的阻塞作用，这5个线程不能执行自增和自减操作，等待t1线程锁释放；主线程执行了t1.stop方法，终止了t1线程，注意，由于a变量是所有线程共享的，所以其他5个线程获得的a变量也是1；其他5个线程依次获得CPU执行机会，打印出a值；
#### 正确终止一个运行线程的方法
+ 该方法是使用`自定义的标志位`决定线程的执行情况；
```
class SafeStopThread extends Thread {
    private volatile boolean stop = false;//必须加上volatile

    @Override
    public void run() {
        while (stop) {
            
        }
    }
    public void terminate(){
        stop = true;
    }
}
```
#### interrupt方法
+ `interrupt方法不能终止一个正在执行着的线程，它只是修改中断标志而已`；`可以通过isInterrupted方法来获取`；
#### 停止线程的另外方法
+ 可以使用的是`线程池（比如ThreadPoolExecutor类）`，那么可以通过`shutdown方法逐步关闭池中的线程`，它采用的是比较温和，安全的关闭线程方法，完全不会产生类似stop方法的弊端；

### 线程建议---线程优先级只使用三个等级
+ 线程的优先级（Priority）决定了线程获得CPU运行的机会，优先级越高获得的运行机会越大，优先级越低获得的机会越小，Java的线程有10个等级（确切的说是11个级别，级别为0的线程是JVM的，应用程序不能设置该级别）；
```
class Client {
    public static void main(String[] args) {
        for (int i = 0; i < 20; i++) {
            new TestThread().start(i % 10 + 1);
        }
    }
}

class TestThread extends Thread {
    @Override
    public synchronized void start(int _pro) {//正常情况下不要重写start方法，只是为了掩饰
        Thread t = new Thread(this);
        t.setPriority(_pro);
        t.start();
    }

    @Override
    public void run() {
        for (int i = 0; i < 100000; i++) {//消耗CPU的计算
            Math.hypot(Math.pow(924526789, i), Math.cos(i));
        }
        //删除线程优先级
    }
}
```
+ `并不是严格遵循线程优先级级别来执行的；优先级差别越大，运行机会差别越明显`；线程优先级推荐使用`MIN_PRIORITY（1）、NORM_PRIORITY（5）、MAX_PRIORITY（10）三个级别`，不建议使用其他数字；
### 线程建议---使用线程异常处理器提升系统可靠性
+ 在Java1.5版本以后在Thread类中增加了`setUncaughtExceptionHandler方法`，`实现了线程异常的捕捉和处理`；
```
class Client {
    public static void main(String[] agrs) {

    }
}

class TcpServer implements Runnable {
    public TcpServer(){
        Thread t = new Thread(this);
        t.setUncaughtExceptionHandler(new TcpServerExceptionHandler());
        t.start();
    }

    @Override
    public void run() {
        for (int i = 0; i < 3; i++) {
            try {
                Thread.sleep(300);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        throw new RuntimeException();
    }

    private static class TcpServerExceptionHandler implements Thread.UncaughtExceptionHandler {//异常处理器

        @Override
        public void uncaughtException(Thread t, Throwable e) {
            System.out.print("线程：" + t.getName() + "出现异常，自动重启，请分析原因");//记录线程异常信息
            e.printStackTrace();
            new TcpServer();//重启线程，保证业务不中断
        }
    }
}
```

|如果要在实际环境中应用，需要注意如下三个方面|
|------|
|共享资源锁定；如果线程异常产生的原因是资源被锁定，自动重启应用只会增加系统的负担，无法提供不间断服务，例如：一个即时通信服务器出现信息不能写入的情况时，即使再怎么重启服务，也无法解决问题，在此情况下最好的办法是停止所有的线程，释放资源|
|脏数据引起系统逻辑混乱；异常的产生中断了正在执行的业务逻辑，特别是如果正在执行一个原子操作。但如果此时抛出了运行期异常可能会破坏正常的业务逻辑，例如出现用户认证通过了，但签到不成功的情况，在这种情况下重启应用服务器，虽然可以提供服务，但对部分用户则产生了逻辑异常|
|内存溢出；线程异常了，但由线程创建的对象并不会马上回收，如果再重新启动新线程，在创建一批新对象，特别是加入了场景接管，就非常危险了|

### 线程建议---异步运算考虑使用Callable接口
+ 从1.5版本开始引入了一个新的接口`Callable`，它类似于Runable接口，实现它就可以实现多线程任务；`实现Callable接口的类，只是表明它是一个可调用的任务，并不表示它具有多线程运算能力，还是需要执行器来执行的`；
```
class Client {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executorService = Executors.newSingleThreadExecutor();//生成一个单线程的异步执行器
        Future<Integer> future = executorService.submit(new TaxCalculator(100));//线程执行后的期望值
        while (!future.isDone()) {
            TimeUnit.MILLISECONDS.sleep(200);
        }
        executorService.shutdown();
    }
}

class TaxCalculator implements Callable<Integer> {
    private int seedMoney;

    public TaxCalculator(int _seedMoney) {
        this.seedMoney = _seedMoney;
    }

    @Override
    public Integer call() throws Exception {
        TimeUnit.MILLISECONDS.sleep(10000);
        return seedMoney / 10;
    }
}
```
+ Exexutors是一个静态工具类，提供了异步执行器的创建能力，如但线程执行器newSingleThreadExecutor、固定线程数量的执行器newFixedThreadPool等，一般它是异步计算的入口类，Future关注的是线程执行后的结果，比如有没有完成运算，执行结果是多少等；异步计算的好处是：尽可能多的占用系统资源，提供快速运算；可以监控线程执行的情况，比如是否执行完毕，是否有返回值，是否有异常等；可以为用户提供良好的支持；

### 线程建议---优先选择线程池
+ 线程不能从结束状态直接转变为可运行状态，在容器（系统）启动时，创建足够多的线程，当容器（或系统）需要时直接从线程池中获取线程，运算出结果，再把线程返回到线程池中，`ExecutorService就是实现了线程池的执行器`；
```
class Client {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService service = Executors.newFixedThreadPool(2);//2个线程的线程池
        for (int i = 0; i < 4; i++) {//多次执行线程体
            service.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.print(Thread.currentThread().getName());
                }
            });
        }
        service.shutdown();//关闭执行器
    }
}
```

|线程池的实现涉及的名词|
|------|
|工作线程（Worker）；线程池中的线程，只有两个状态：可运行状态和等待状态，在没有任务时他们处于等待状态，运行时可以循环的执行任务|
|任务接口（Task）；每个任务必须实现的接口，以供工作线程调度器调度，它主要规定了任务的入口，任务执行完的场景处理，任务的执行状态等，这里有两种类型的任务：具有返回值（或异常）的Callable接口任务和无返回值并兼容旧版本的Runnable接口任务|
|任务队列（Work Queue）；也叫做工作队列，用于存放等待处理的任务，一般是BlockingQueue的实现类，用来实现任务的排队处理|

+ 线程池的创建过程：创建一个阻塞队列以容纳任务，在第一次执行任务时创建足够多的线程（不超过许可线程数），并处理任务，之后每个工作线程自行从任务队列中获取任务，直到任务队列中的任务数量是0为止，此时，线程将处于等待状态，一旦有任务再加入到队列中，即唤醒工作线程进行处理，实现线程的可复用性；使用线程池减少的是线程的创建和销毁时间，这对于多线程应用来说非常有帮助，如果不采用线程池技术，每次请求都会重新创建一个线程，这会导致系统的性能负荷加大，响应效率下降，降低了系统的友好性；

### 线程建议---适当设置阻塞队列长度
+ `阻塞队列BlockingQueue扩展了Queue和Collection接口`，`对元素的插入和提取使用了阻塞处理`；
```
public class Common {
    public static void main(String[] args) {
        BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(5);//定义初始化长度为5
        for (int i = 0; i < 10; i++) {//加入10个元素，将会报出非法参数异常，队列已满
            blockingQueue.add("");
        }
    }
}
```
+ `阻塞队列的容量是固定的，非阻塞队列则是变长的，阻塞队列可以在声明时指定队列的容量，若指定了容量，则元素的数量不可超过该容量，若不指定，队列的容量为Integer的最大值；阻塞队列和非阻塞队列有此区别的原因是：阻塞队列是为了容纳（或排序）多线程任务而存在的，其服务的对象是多线程应用，而非阻塞队列容纳的则是普通的数据元素`；

### 线程建议---CyclicBarrier让多线程齐步走
+ 两个线程独立运行，在没有线程间通信的情况下，如何解决两个线程汇集在同一原点的问题；`Java提供了CyclicBarrier（关卡，或者栅栏）工具类实现`；
```
public class Common {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(2, new Runnable() {//设置汇集数量，以及汇集完成后的任务
            @Override
            public void run() {
                System.out.print("隧道已经打通");
            }
        });
        new Thread(new Worker(cyclicBarrier), "工人1").start();//工人1挖隧道
        new Thread(new Worker(cyclicBarrier), "工人2").start();//工人2挖隧道
    }

    static class Worker implements Runnable {
        private CyclicBarrier cyclicBarrier;

        public Worker(CyclicBarrier cyclicBarrier) {
            this.cyclicBarrier = cyclicBarrier;
        }

        @Override
        public void run() {
            try {
                Thread.sleep(new Random().nextInt(1000));
                cyclicBarrier.await();//到达汇合点
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        }
    }
}
```
+ `CyclicBarrier关卡可以让所有线程全部处于等待状态（阻塞），然后在满足条件的情况下继续执行`；CyclicBarrier可以用在系统的性能测试中，例如编写一个核心算法，但不能确定其可靠性和效率如何，我们就可以让N个线程汇集到测试原点上，然后一声令下，所有的线程都引用该算法，即可观察出算法是否有缺陷；

### 线程建议--使用CountDownLatch协调子线程
```
public class Common {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        int num = 10;//参加赛跑人数
        CountDownLatch begin = new CountDownLatch(1);//发令枪只响一次
        CountDownLatch end = new CountDownLatch(num);//参与跑步有多个
        ExecutorService executorService = Executors.newFixedThreadPool(num);//每个跑步者一个跑道
        List<Future<Integer>> futures = new ArrayList<>();//记录比赛结果
        for (int i = 0; i < num; i++) {//跑步者就位，所有线程处于等待状态
            futures.add(executorService.submit(new Runner(begin, end)));
        }
        begin.countDown();//发令枪响，跑步者开始跑步
        end.await();//等待所有跑步者开始跑步
        int count = 0;
        for (Future<Integer> future : futures) {
            count += future.get();
        }
        System.out.print("平均分数:" + count / num);
    }

    static class Runner implements Callable<Integer> {
        private CountDownLatch begin;//开始信号
        private CountDownLatch end;//结束信号

        public Runner(CountDownLatch _begin, CountDownLatch _end) {
            this.begin = _begin;
            this.end = _end;
        }
        @Override
        public Integer call() throws Exception {
            int score = new Random().nextInt(25);
            begin.await();//等待发令响起
            TimeUnit.MILLISECONDS.sleep(score);//跑步中...
            end.countDown();//跑步者已经跑完全程
            return score;
        }
    }
}
```

|程序执行逻辑|
|------|
|10个线程都开始运行，执行到begin.await()后线程阻塞，等待begin的计时器为0|
|主线程调用begin的countDown方法，使begin的计时器为0|
|10个线程继续运行|
|主线程继续运行下一个语句，end的计时器不为0，主线程等待|
|每个线程运行结束时把end的计时器减一，标志着本线程运行完毕|
|10个线程全部结束，end计时器为0|
|主线程继续执行，打印出成绩平均值|

+ `CountDownLatch的作用是控制一个计数器，每个线程在运行完毕后会执行countDown，表示自己运行结束，这对于多个子任务的计算特别有效，如：一个异步任务需要拆分成10个子任务执行，主任务必须要知道子任务是否完成，所有子任务完成后才能进行合并计算，从而保证了一个主任务的逻辑正确性`；

### 线程建议--预防线程死锁
```

public class Common {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        new Thread(new Foo()).start();
    }
}

class Foo implements Runnable {

    @Override
    public void run() {
        fun(10);
    }

    public synchronized void fun(int i) {
        if (--i > 0) {
            for (int j = 0; j < i; j++) {
                System.out.print("*");
            }
            System.out.println(i);
            fun(i);
        }
    }
}

```
+  `不会产生死锁，因为在运行时当前线程获得了foo对象的锁（synchronized虽然是标注在方法上的，但实际作用的是整个对象），也就是该线程持有了foo对象的锁`。所以它可以重复多次调用fun()方法，也就是递归，不会产生死锁；
```

class Foo implements Runnable{

       public void run(){

              method1();

       }

       public synchronized void method1(){

              method2();

       }

       public synchronized void method2(){

              //do something

       }

}

```

+ `方法method1是synchronized修饰的，方法method2也是synchronized修饰的，method1调用method2是没有任何问题的，因为是同一个线程持有对象锁，在一个线程内多个synchronized方法调用完全是可行的，这种情况不会产生死锁`；

##### 产生死锁的场景

```
public class Common {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        final A a = new A();
        final B b = new B();
        new Thread(new Runnable() {
            @Override
            public void run() {
                a.a(b);
            }
        },"线程A").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                b.b1(a);
            }
        },"线程B").start();
    }

    static class A {
        public synchronized void a(B b) {
            String name = Thread.currentThread().getName();
            System.out.print(name + " 进入A.a方法");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print(name + "试图访问B.b2方法");
            b.b2();
        }

        public synchronized void a2() {
            System.out.print("进入A.a2方法");
        }
    }

    static class B {
        public synchronized void b1(A a) {
            String name = Thread.currentThread().getName();
            System.out.print(name + " 进入B.b1方法");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.print(name + " 进入A.a2方法");
            a.a2();
        }

        public synchronized void b2() {
            System.out.print(" 进入b.B2方法");
        }
    }
}
```

|达到线程死锁需要的四个条件，只有满足了这些条件才可能产生线程死锁|
|------|
|互斥条件；一个资源每次只能被一个线程使用|
|资源独占条件；一个线程因请求资源而阻塞时，对已获得的资源保持不放|
|不剥夺条件；线程已获得的资源在未使用完之前，不能强制剥夺|
|循环等待条件；若干线程之间形成一种头尾相接的循环等待资源关系|

#### 避免死锁的方法
##### 避免或减少资源共享
##### 使用自旋锁

```
public void b1(){
       try{
              //立即获得锁，或者2秒等待锁资源
              if(lock.tryLock(2 , TimeUtil.SECONDS)){
                     System.out.print("进入B.b2()");
              }
       }catch(InterruptException e){
              //异常处理
       }finallt{
              //释放锁
              lock.unlock();
       }
}
```
+ 上面的代码中使用`tryLock实现了自旋锁，它跟互斥锁一样，如果一个执行单元要想访问被自旋锁保护的共享资源，则必须先得到锁，在访问完共享资源后，也必须释放锁，如果在获取自旋锁时，没有任何执行单元保持该锁，那么将立即得到锁，如果在获取自旋锁时锁已经有保持者，那么获取锁操作将自旋在那里，直到该自旋锁的保持者释放了锁为止`；

