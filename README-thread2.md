### 线程的创建和启动
+ java使用`Thread类`代表线程，所有的线程对象都必须是Thread类或其子类的实例；
#### 继承Thread类创建线程类

|通过继承Thread类来创建并启动多线程的步骤|
|------|
|定义Thread类的子类，并重写该类的run()方法，该run()方法的方法体代表了线程需要完成的任务，因此把run()方法称为线程执行体|
|创建Thread子类的实例，即创建了线程对象|
|调用线程对象的start()方法来启动该线程|

+ 当java程序开始运行后，程序至少会创建一个主线程，主线程的线程执行体不是由run()方法确定的，而是由`main()`方法确定的，`main()方法的方法体代表主线程的线程执行体`；进行多线程编程时不要忘记了，java程序运行时默认的主线程，main()方法的方法体就是主线程的线程执行体；

|线程的方法|说明|
|------|------|
|Thread.currentThread()|该方法是Thread类的静态方法，该方法总是返回当前正在执行的线程对象|
|getName()|该方法是Thread类的实例方法，该方法返回调用该方法的线程名字|
|setName()|该方法将为线程设置名字|

+ 默认情况下，主线程的名字为`main`，用户启动的多个线程的名字依次为`Thread-0、Thread-1、Thread-2..........................`等；`使用继承Thread类的方法来创建线程类时，多个线程之间无法共享线程类的实例变量`；

#### 实现Runnable接口创建线程类
##### 实现Runnable接口来创建并启动多线程的步骤如下：
+ 定义Runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体；创建Runnable实现类的实例，并以此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象；

```
//创建Runnable实现类的对象
SecondThread st = new SecondThread();
//以Runnable实现类的对象作为Thread的target来创建Thread对象，即线程对象
new Thread(st);
```

+ 也可以在创建Thread对象时为该Thread对象指定一个名字：

```
new Thread(st , "线程名1");
```

+ Runnable对象仅仅作为Thread对象的target，Runnable实现类里包含的run()方法仅仅作为线程执行体，而实际的线程对象依然是Thread实例，只是该Thread线程负责执行其target的run()方法；调用线程对象的start()方法来启动该线程；`以下的代码执行时，在两个线程中切换的时候，因为使用的是同一个target所以会接着上次线程执行的地方继续执行`如，线程1执行到了一个地方，返回线程2执行，线程2执行的地方将会接着线程1执行到的地方接着执行：

```
SecondThread st = new SecondThread();
new Thread(st , "线程1").start();
new Thread(st , "线程2").start();
```

+ `也就是采用Runnable接口的方法创建的多线程可以共享线程类的实例变量`，这是因为在这种方式下，程序所创建的Runnable对象只是线程Thread的target，而多个线程可以共享同一个target，所以多个线程可以共享同一个线程类的实例变量；Runnable是函数式接口；

#### 使用Callable和Future创建线程
+ `Callable接口提供了一个call()方法可以作为线程执行体，但call()方法比run()方法功能更加强大`：`call()方法可以有返回值`；`call()方法可以声明抛出异常`；`因此完全可以提供一个Callable对象作为Thread的target，而该线程的线程执行体就是该Callable对象的call()方法`，但是：`Callable不是Runnable接口的子接口，所以Callable对象不能直接作为Thread的target，而且call()方法还有一个返回值，call()方法并不是直接调用，她是作为线程执行体被调用的`，那么就需要使用Future接口来代表Callable接口里的call()方法的返回值，并为Future接口提供了一个FutureTask实现类，该实现类实现了Future接口，并实现了Runnable接口，可以作为Thread类的target；

|在Future接口里定义的如下公共方法来控制它关联的Callable任务|说明|
|-------|------|
|boolean cancel(boolean mayInterruptIfRunning)|试图取消该Future里关联的Callable任务|
|V get()|返回Callable任务里call()方法的返回值，调用该方法将导致程序阻塞，必须等到子线程结束后才会得到返回值|
|V get(long timeout , TimeUnit unit)|返回Callable任务里call()方法的返回值，该方法让程序最多阻塞timeout和unit指定的时间，如果经过指定时间后Callable任务依然没有返回值，将会抛出TimeoutException异常|
|boolean isCanneled()|如果Callable任务正常完成前被取消，则返回true|
|boolean isDone()|如果Callable任务已完成，则返回true|

##### 创建并启动有返回值的线程的步骤

|步骤|
|------|
|创建Callable接口的实现类，并实现Callable的call方法，该call方法将作为线程执行体，且该call方法有返回值，再创建Callable实现类的实例，因为是函数式接口，可以使用Lambda表达式|
|创建FutureTask类来包装Callable对象，该FutureTask对象封装了该Callable对象的call方法的返回值|
|使用FutureTask对象作为Thread对象的target创建并启动新线程|
|调用FutureTask对象的get()方法来获取子线程执行结束后的返回值|

```
//实现了Callable接口的类
ThirdThread tt = new ThirdThread();
//创建FutureTask对象包装Callable对象
FutureTask tf = new FutureTask(tt);
//将FutureTask对象作为target来启动新线程
new Thread(ft).start();
//获取Callable的call方法的返回值
ft.get();
```

#### 创建线程的三种方式对比
##### 采用实现Runnable、Callable接口的方式创建多线程的优缺点：
+ 线程类只是实现了Runnable、Callable接口，还可以继承其他类；在这种方式下，多线程可以共享同一个target对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将CPU、代码和数据分开，形成清晰的模型，较好的体现了面向对象的思想；劣势是：编程稍稍复杂，如果需要访问当前线程，则必须使用Thread.currentThread()方法；
##### 采用继承Thread类的方式创建多线程的优缺点：
+ 劣势是：因为线程类已经继承的Thread类，所以不能再继承其他父类；优势是：编写简单，如果需要访问当前线程，则无需使用Thread.currentThread()方法，直接使用this即可获得当前线程；所以一般推荐使用实现Runnable、Callable接口的方式来创建多线程；
