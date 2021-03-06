### 控制线程
#### join线程
+ Thread提供了一个让一个线程等待另一个线程完成的方法，`join()方法`，`当在某个程序执行流中调用其他线程的join方法时，调用线程将被阻塞，直到被join方法加入的线程执行完为止`；

|join方法的三种重载形式|说明|
|------|------|
|join()|等待被join的线程执行完成|
|join(long mills)|等待被join的线程的时间最长为mills毫秒，如果在mills毫秒内被join的线程还没有执行结束，则不再等待|
|join(long mills , int nanos)|等待被join的线程的时间最长为mills毫秒加nano毫微秒|

#### 后台线程
+ `后台线程有个特性，如果所有的前台线程都死亡，后台线程会自动死亡`；`调用Thread对象的setDaemon(true)方法可将指定线程设置成后台线程`；Thread类还提供了一个isDaemon()方法，用于判断指定线程是否为后台线程；`主线程默认是前台线程`，并不是所有的线程默认都是前台线程，有些线程默认就是后台线程，`前台线程创建的子线程默认是前台线程，后台线程创建的子线程默认是后台线程`；前台线程死亡后，JVM会通知后台线程死亡，但从它接受指令到做出响应需要一定时间，而且要将某个线程设置为后台线程，必须在该线程启动之前设置，也就是说，`setDaemon()方法必须在start方法之前调用，否则会引发IllegalThreadStateException异常`；

#### 线程睡眠sleep
+ 如果需要让当前正在执行的线程暂停一段时间，并`进入阻塞状态`，则可以通过调用Thread类的静态`sleep方法`来实现，sleep方法有两种形式的重载：

|方法|说明|
|------|------|
|static void sleep(long mills)|让当前正在执行的线程暂停mills毫秒，并进入阻塞状态，该方法受到系统计时器和线程调度器的精度与准确度的影响|
|static void sleep(long mills , int nanos)|让当前正在执行的线程暂停mills毫秒加nano毫微秒，并进入阻塞状态，该方法受到系统计时器和线程调度器的精度与准确度的影响|

+ `当当前线程调用sleep方法进入阻塞状态后，在其睡眠时间内，该线程不会获得执行的机会，即使系统中没有其他可执行的线程，处于sleep中的线程也不会执行`，因此sleep方法常用来暂停程序的执行；

#### 线程让步：yield
+ `yield()方法`是一个和`sleep()`方法有点相似的方法，它也是Thread类提供的一个静态方法，`它也可以让当前正在执行的线程暂停，但它不会阻塞该线程，他只是将该线程转入就绪状态`，让系统调度器重新调度一次；实际上，当某个线程调用了`yield()方法暂停之后，只有优先级与当前线程相同，或优先级比当前线程更高的处于就绪状态的线程才会获得执行机会`；

|sleep和yeild方法的区别|
|------|
|sleep方法暂停当前线程后，会给其他线程执行机会，不会理会其他线程的优先级，但yeild方法只会给优先级相同，或优先级更高的线程执行机会|
|sleep方法会将线程转入阻塞状态，知道经过阻塞时间才会转入就绪状态，而yeild不会将线程转入阻塞状态，它只是强制当前线程进入就绪状态，因此完全有可能某个线程调用yield方法暂停之后，立即再次获得处理器资源被执行|
|sleep方法生命抛出InterruptedException异常，所以调用sleep方法时要么捕捉该异常，要么显式抛出该异常，而yield方法则没有声明抛出任何异常|
|sleep方法比yield方法有更好的可移植性，通常不建议使用yield方法来控制并发线程的执行|

#### 改变线程优先级

+ `每个线程默认的优先级都与创建它的父线程的优先级相同，在默认情况下，main线程具有普通优先级，由main线程创建的子线程也具有普通优先级`；Thread类提供了`setPriority(int newPriority)`方法、`getPriority()`方法、来设置和返回指定线程的优先级，其中`setPriority方法`的参数可以是一个整数，范围在`1~10`，也可以使用Thread类的如下三个静态常量：

|Thread静态常量|数值|
|------|------|
|`MAX_PRIORITY`|其值为10|
|`MIN_PRIORITY`|其值为1|
|`NORM_PRIORITY`|其值为5|
