### 线程组和未处理的异常
+ java使用`ThreadGroup`来表示`线程组`，它可以对一批线程进行分类管理，对线程组的控制相当于同时控制这批线程，`用户创建的所有线程都属于指定线程组`；`如果程序没有显式指定线程属于哪个线程组，则该线程属于默认线程组`，在默认情况下，`子线程和创建它的线程处于同一个线程组内`；`一旦某个线程加入指定线程组之后，该线程将一直属于该线程组，直到该线程死亡，线程运行中途不能修改他所属的线程组`；

|Thread类提供了如下几个构造器来设置新创建的线程属于哪个线程组|说明|
|------|-------|
|Thread(ThreadGroup group , Runnable target)|以target的run()方法作为线程执行体创建新线程，属于group线程组|
|Thread(ThreadGroup group , Runnable target , String name)|以target的run()方法作为线程执行体创建新线程，属于group线程组，且线程名为name|
|Thread(ThreadGroup group , String name)|创建新线程，新线程名为name，属于group线程组|

+ ThreadGroup类提供了如下两个简单的构造器来创建实例：

|方法|说明|
|------|------|
|ThreadGroup(String name)|以指定的线程组名字来创建新的线程组|
|ThreadGroup(ThreadGroup parent , String name)|以指定的名字、指定的父线程组创建一个新线程组|

+ 线程组总会具有一个字符串类型的名字，该名字可通过调用`ThreadGroup的getName()方法来获取`，`但不允许改变线程组的名字`；

|ThreadGroup类提供了如下常用的方法来操作整个线程组里的所有线程|说明|
|------|------|
|int activeCount()|返回此线程组中活动线程的数目|
|interrupt()|中断此线程组中的所有线程|
|isDaemon()|判断该线程组是否是后台线程组|
|setDaemon(boolean daemon)|把该线程组设置成后台线程组，后台线程组具有一个特性------当后台线程组的最后一个线程执行结束或最后一个线程被销毁后，后台线程组将自动销毁|
|setMaxPriority(int pri)|设置线程组的最高优先级|
|void uncaughtException(Thread t , Throwable b)|该方法可以处理该线程组内的任意线程所抛出的未处理异常|

+ 从java5开始，`java加强了线程的异常处理，如果线程执行过程中抛出了一个未处理异常，JVN会在结束该线程之前会自动查找是否有对应的Thread.UncaughtExceptionHandler对象，如果找到该处理器对象，则会调用该对象的uncaughtException(Thread t , Throwable b)方法来处理该异常`；`Thread.UncaughtExceptionHandler是Thread类的一个静态内部接口，该接口内只有一个方法，void uncaughtException(Thread t , Throwable b)该方法中的t代表出现异常的线程，而e代表该线程抛出的异常`；

|Thread类提供了如下两个方法来设置异常处理器|说明|
|------|-------|
|static setDefaultUncaughtException(Thread.UncaughtExceptionHandler eh)|为该线程类的所有线程实例设置默认的异常处理器|
|setUncaughtExceptionHandler(Thread.UncaughtExceptionHandler eh)|为指定的线程实例设置异常处理器|

+ ThreadGroup类实现了Thread.UncaughtExceptionHandler接口，所以每个线程所属的线程组将会作为默认的异常处理器；

|线程组处理异常的默认流程如下|
|------|
|如果该线程组有父线程组，则调用该父线程组的uncaughtException方法来处理该异常|
|如果该线程实例所属的线程类有默认的异常处理器，那么就调用该异常处理器处理该异常|
|如果该异常对象是ThreadDeath对象，则不做任何处理；否则，将异常跟踪栈的信息打印到System.err错误输出流，并结束该线程|
