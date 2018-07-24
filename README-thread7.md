### 线程池
+ `系统启动一个新线程的成本比较高`，因为它涉及与操作系统交互，在这种情况下，`使用线程池可以很好的提高性能`，`尤其是当程序中需要创建大量生命周期短的线程时`，`更应该考虑使用线程池`；`线程池在系统启动时即创建大量空闲的线程`，`程序将一个Runnable或Callable对象传给线程池`，`线程池就会启动一个线程来执行他们的run方法或call方法`，`当run或call方法执行结束后`，`该线程并不会死亡`，`而是再次返回线程池中成为空闲状态`，`等待执行下一个Runnable或Callable对象的run或call方法`；`使用线程池可以有效的控制系统中并发线程的数量`，`当系统中包含大量的并发线程时，会导致系统性能下降，甚至导致JVM崩溃，而线程池的最大线程数参数可以控制系统中并发线程数不超过此数`；
#### Java8改进的线程池
+ java5以前，开发者必须手动实现自己的线程池，从java5开始，`java内建支持线程池`，java5新增的一个`Executors工厂类来产生线程池`，该工厂类包含如下静态工厂方法来创建线程池：

|方法|说明|
|------|------|
|newCachedThreadPool()|创建一个具备缓存功能的线程池，系统根据需要创建线程，这些线程将会被缓存在线程池中|
|newFixedThreadPool(int nThreads)|创建一个可重用的，具有固定线程数的线程池|
|newSingleThreadExecutor()|创建一个只有单线程的线程池，他相当于调用newFixedThreadPool(1)|
|newScheduleThreadPool(int corePoolSize)|创建具有执行线程数的线程池，它可以在指定延迟后执行线程任务，corePoolSize指池中所保存的线程数，即使线程是空闲的也被保存在线程池内|
|newSingleThreadScheduledExecutor()|创建只有一个线程的线程池。他可以在指定延迟后执行线程任务|
|ExecutorService newWorkStealingPool(int parallelism)|创建持有足够的线程的线程池来支持给定的并行级别，该方法还会使用多个队列来较少竞争|
|ExecutorService newWorkStealingPool()|该方法是前一个方法的简化版本，如果当前机器有4个CPU则目标并行级别被设置为4，也就相当于为前一个方法传入4作为参数|

+ 上面的7个方法的前三个方法返回一个`ExecutorService`对象，`该对象代表一个线程池`，`它可以执行Runnable对象或Callable对象所代表的线程`，而中间两个方法返回一个`ScheduleExecutorService`线程池，它是`ExecutorService的子类`，`他可以在指定延迟后执行线程任务`，最后两个方法则是java8新增的，这两个方法充分利用多CPU并行的能力，这两个方法生成的`work、stealing池`，都相当于`后台线程池`，如果所有的前台线程都死亡了，`work、stealing池中的线程会自动死亡`；`ExecutorService代表尽快执行线程的线程池`（只要线程池中有空闲线程，就立即执行线程任务），程序只要将一个Runnable对象或Callable对象（代表线程任务）提交给该线程池，该线程池就会尽快执行该任务，ExecutorService里提供了如下三个方法：

|方法|说明|
|------|------|
|`Future<?> submit(Runnable task)`|将一个Runnable对象提交给指定的线程池，线程池将在有空闲线程时执行Runnable对象代表的任务，其中Future对象代表Runnable任务的返回值---但run方法没有返回值，所以Future对象将在run方法执行结束后返回null，但可以调用Future的isDone()、isCancelled()方法来获得Runnable对象的执行状态|
|`<T> Future<T> submit(Runnable task,T result)`|将一个Runnable对象提交给指定的线程池，线程池将在有空闲线程时执行Runnable对象代表的任务，其中result显式指定线程执行结束后的返回值，所以Future对象将在run方法执行结束后返回result|
|`<T> Future<T> submit(Callable<T> task)`|将一个Callable对象提交给指定的线程池，线程池将在有空闲线程时执行Callable对象代表的任务，其中Future代表Callable对象里call方法的返回值|

+ `ScheduleExecutorService代表可在指定延迟后或周期性的执行线程任务的线程池`，他提供了如下4个方法：

|方法|说明|
|------|-------|
|ScheduledFuture<V> schedule(Callable<V> callable , long delay , TimeUnit unit)|指定Callable任务将在delay延迟后执行|
|ScheduledFuture<V> schedule(Runnable<V> command , long delay , TimeUnit unit)|指定command任务将在delay延迟后执行|
|ScheduledFuture<?> scheduleAtFixedRate(Runnable command , long initialDelay , long period , TimeUnit unit)|指定command任务将在delay延迟后执行，而且以设定频率重复执行，也就是说，在initialDelay后开始执行，依次在initialDelay+period 、period *2+initialDelay...........处重复执行，以此类推|
|ScheduledFuture<?> scheduleWithFixedDelay(Runnable command , long initialDelay , long period , TimeUnit unit)|创建并执行一个在给定初始延迟后首次启动的定期操作，随后在每一次执行终止和下一次执行开始之间都存在给定的延迟，如果任务在任一次执行时出现异常，就会取消后续执行，否则，只能通过程序来显式取消或终止该任务|

+ `用完线程池后，应该调用该线程池的shutdown()方法，该方法将启动线程池的关闭序列，调用shutdown方法后的线程池不再接受新任务，但会将以前所有已提交的任务执行完成，当线程池中的所有任务都执行完了，池中的所有线程都会死亡`，另外也可以调用线程池的`shutdownnow()`方法来关闭线程池，该方法试图停止所有正在执行的活动任务，暂停处理正在等待的任务，并返回等待执行的任务列表；

|使用线程池来执行线程任务的步骤|
|------|
|调用Executor类的静态工厂方法创建一个ExecutorService对象，该对象代表一个线程池|
|创建Runnable实现类或Callable实现类的实例，作为线程执行任务|
|调用ExecutorService对象的submit方法来提交Runnable实现类或Callable实现类的实例|
|当不想提交任何任务时，调用ExecutorService对象的shutdown方法来关闭线程池|

##### 使用线程池来执行指定Runnable对象所代表的任务
```
class Test{
    public static void main(String[] args) {
        ExecutorService pool = Executors.newFixedThreadPool(6);//创建线程池
        pool.submit(new Runnable() {//向线程池中提交两个线程
            @Override
            public void run() {

            }
        });
        pool.submit(new Runnable() {
            @Override
            public void run() {

            }
        });
        pool.shutdown();//关闭线程池
    }
}
```
#### java8增强的ForkJoinPool

+ java7提供了`ForkJoinPool来支持将一个任务分成多个小任务并行计算，再把多个小任务的结果合并成总的计算结果`，`ForkJoinPool是ExecutorService的实现类，因此是一种特殊的线程池`；

|ForkJoinPool提供如下两个常用的构造器|说明|
|------|------|
|ForkJoinPool(int parallelism)|创建一个包含parallelism个并行线程的ForkJoinPool|
|ForkJoinPool()|以Runtime.availableProcessors()方法的返回值作为parallelism参数来创建ForkJoinPool|

+ java8为ForkJoinPool增加了通用池功能；

|方法|说明|
|------|------|
|ForkJoinPool commonPool()|该方法返回一个通用池，通用池的运行状态不会受shutdown方法或shutdownnow方法的影响，当然，如果程序直接执行System.exit(0)来终止虚拟机，通用池以及通用池中正在执行的任务都会被自动终止|
|int getCommonPoolParallelism()|该方法返回通用池的并行级别|

+ 创建了`ForkJoinPool实例`后，就可`调用ForkJoinPool的submit方法或invoke方法来执行指定任务`了，其中`ForkJoinPool代表一个可以并行、合并的任务`，`ForkJoinPool是一个抽象类`，他有两个抽象子类：`RecursiveAction类和RecrrsiveTask`,其中`RecrrsiveTask代表有返回值的任务`，而`RecursiveAction代表没有返回值的任务`；
