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

