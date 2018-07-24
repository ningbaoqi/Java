### 线程通信
#### 传统的线程通信
+ 假如现在系统中有两个线程，这两个线程分别代表存款者和取款者，现在假设系统有一种特殊的要求，系统要求存款者和取款者不断的重复存款、取款的动作，而且要求每当存款者将钱存入指定帐户后，取款者就立即取出这笔钱，不允许存款者连续两次存钱，也不允许取款者连续两次取钱；为了实现上述的功能，可以借助`Object类的wait()、notify()、notifyAll()`三个方法，这三个方法并不属于Thread类，而是属于Object类，但这三个方法必须由同步监视器对象来调用，这可分成以下两种情况：

|情况|说明|
|------|------|
|对于使用synchoronized修饰的同步方法|因为该类的默认实例this就是同步监视器，所以可以在同步方法中直接调用这三个方法|
|对于使用synchoronized修饰的同步代码块|同步监视器是synchoronized后括号里的对象，所以必须使用该对象来调用这三个方法|

##### wait()、notify()、notifyAll()方法说明

|方法|说明|
|------|-----|
|wait()|导致当前线程等待，直到其他线程调用该同步监视器的notify()、notifyAll()方法来唤醒该线程，该wait方法有三种形式；无时间参数的wait（一直等待，直到其他线程通知）；带毫秒参数的wait方法和带毫秒、毫微秒参数的wait方法（这两种方法都是等待指定时间后自动苏醒）；调用wait方法的当前线程会释放对该同步监视器的锁定|
|notify()|唤醒在此同步监视器上等待的单个线程，如果所有线程都在此同步监视器上等待，则会唤醒其中一个线程，选择是任意性的；只有当前线程放弃对该同步监视器的锁定后（使用wait方法），才可以执行被唤醒的线程|
|notifyAll()|唤醒在此同步监视器上等待的所有线程；只有当前线程放弃对该同步监视器的锁定后，才可以执行被唤醒的线程|

#### 使用Condition控制线程通信
+ `如果程序不适用synchoronized关键字来保证同步，而是直接使用Lock对象来保证同步，则系统中不存在隐式的同步监视器，也就不能使用notify()、notifyAll()、wait()方法进行线程通信了`；当使用`Lock`对象保证同步时，java提供了`Condition`类来保持协调，`使用Condition可以让那些已经得到Lock对象却无法继续执行的线程释放Lock对象`，`Condition对象也可以唤醒其他处于等待的线程`；`Condition实例被绑定在一个Lock对象上，要获取特定Lock实例的Condition实例，调用Lock对象的newCondition()方法即可`；

|Condition类提供了如下三个方法|说明|
|------|------|
|await()|类似于隐式同步监视器上的wait方法，导致当前线程等待，直到其他线程调用该Condition的signal()方法或SignalAll()方法来唤醒该线程，该await方法有更多变体如：long awaitNanos(long nanosTimeout)、void awaitUninterruptibly()、awaitUntil(Date deadline)等可以完成更丰富的等待操作|
|signal()|唤醒在此Lock对象上等待的单个线程，如果所有线程都在该Lock对象上等待，则会唤醒其中一个线程，选择是任意性的，只有当前线程放弃对该Lock对象的锁定后（使用await方法），才可以制定被唤醒的线程|
|signalAll()|唤醒在此Lock对象上等待的所有线程，只有当前线程放弃对该Lock对象的锁定后，才可以执行被唤醒的线程|

```
class C{
       //显式定义Lock对象
       private final Lock lock = new ReentrantLock();
       //获取指定的Lock对象对应的Condition对象
       private final Condition cond = lock.newCondition();
       public void draw(){
              //加锁
              lock.lock();
              try{
                     cond.await();//阻塞当前线程并释放锁
                     //do something
                     cond.signalAll();
              }......{
                     //do something
              }finally{
                     lock.unlock();
              }
       }
}
```
#### 使用阻塞队列BlockingQueue控制线程通信
+ `BlockingQueue`具有一个特征：`当生产者线程试图向BlockingQueue中放入元素时，如果该队列已满，则该线程被阻塞`；`当消费者线程试图从BlockingQueue中取出元素时，如果该队列为空，则该线程被阻塞`；`程序的两个线程通过交替向BlockingQueue中放入元素、取出元素即可很好控制线程的通信`；

|BlockingQueue提供如下两个支持阻塞的方法|说明|
|------|------|
|put(E e)|尝试把E元素放入BlockingQueue中，如果该队列已满，则阻塞该线程|
|take()|尝试从BlockingQueue的头部取出元素，如果该队列的元素已空，则阻塞该线程|

+ BlockingQueue继承了Queue接口，当然也可使用Queue接口中的方法，这些方法归纳起来可分为以下三组：

|操作|说明|
|------|------|
|在队列尾部插入元素|包括add(E e)、offer(E e)、put(E e)方法，当该队列已满时，这三个方法分别会抛出异常、返回false、阻塞队列|
|在队列头部删除并返回删除的元素|包括remove()、poll()、take()方法，当该队列为空，这三个方法分别会抛出异常，返回false，阻塞队列|
|在队列头部取出但不删除元素|包括element()、peek()方法，当队列为空时，这两个方法分别抛出异常，返回false|

+ BlockingQueue包含的方法之间的对应关系表：

|表|抛出异常|不同返回值|阻塞线程|指定超时时长|
|------|------|------|------|------|
|队尾插入元素|add(e)|offer(e)|put(e)|offer(e,time,unit)|
|队头删除元素|remove()|poll()|take()|poll(time , unit)|
|获取、不删除元素|element()|peek()|无|无|

+ BlockingQueue包含的实现类；

|实现类|说明|
|------|------|
|ArrayBlockingQueue|基于数组实现的BlockingQueue队列|
|LinkedBlockingQueue|基于链表实现的BlockingQueue队列|
|PriorityBlockingQueue|它并不是标准的阻塞队列，与前面的PriorityQueue类似，该队列调用remove()、poll()、take()等方法取出元素时，并不是取出队列中存在时间最长的元素，而是队列中最小的元素，PriorityBlockingQueue判断元素的大小即可根据（实现Comparator接口）的本身大小来自然排序，也可使用Comparator进行定制排序|
|SynchronousQueue|同步队列，对该队列的存、取操作必须交替进行|
|DelayQueue|它是一个特殊的BlockingQueue，底层基于PriorityBlockingQueue实现，不过DelayQueue要求集合元素都实现Delay接口（该接口只有一个long getDelay()方法）DelayQueue根据集合元素的getDelay()方法的返返回值进行排序|
```
//创建一个容器为1的BlockingQueue
BlockingQueue<String> bq = new BlockingQueue<String>(1);
//启动3个继承Thread的线程
new Producer(bq).start();
new Producer(bq).start();
new Producer(bq).start();
//在线程的run方法中使用BlockingQueue的方法，来实现队列在线程间的通信
```
