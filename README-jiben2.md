### 提升Java性能的基本方法
#### 不要在循环条件中计算
+ `如果在循环条件中计算，则每循环一遍就要计算一次，这会降低系统效率`；

```
//每次循环都要计算count*2
while(i < count * 2){
       //do something
}
```
+ 应该替换为：

```
//只计算一遍
int total = count * 2;
while(i < total){
       //do something
}
```
#### 尽可能把变量、方法声明为final static类型
```
public String toChineseNum(int num){
       //中文数字
       String[] cns = {"零" , "壹" , "贰" , "叁" , "肆" , "伍" , "陆" , "柒" , "捌" , "玖"};
       return cms[num];
}
```
+ `每次调用该方法时都会重新生成一个cns数组，注意该数组不会改变，属于不变数组，在这种情况下，把它声明为类变量，并且加上final static修饰会更合适`，`在类加载后就会生成该数组，每次方法调用则不再重新生成数组对象了，这有助于提高系统性能`；

#### 缩小变量的作用范围
+ `关于变量，能定义在方法内的就定义在方法内，能定义在一个循环体内的就定义在循环体内，能放在一个try块中的就放置在该块内，其目的就是加快GC的回收`；
#### 频繁字符串操作使用StringBuilder和StringBuffer
+ `虽然String的连接操作+号已经做了很多优化，但是大量的追加操作上StringBuffer或StringBuilder还是比+号的性能好很多`；
#### 使用非线性检索
+ `如果在ArrayList中存储了大量的数据，使用indexOf方法查找元素会比java.utils.Collections.binarySearch的效率低很多，原因是binarySearch是二分搜索法，而indexOf使用的是逐个元素对比的方法，这里要注意，使用binarySearch搜索时，元素必须进行排序，否则准确性就不可靠了`；
#### 重写Exception的fillInStackTrace方法
+ `fillInStackTrace方法是用来记录异常时的栈信息的，这是非常消耗时间的动作，如果不需要关注栈信息，则可以重写它，如下重写fillInStackTrace的自定义异常会使性能提升10倍以上`；

```
class MyException extends Exception{
       public Throwable fillInStackTrace(){
              return this;
       }
}
```
#### 不建立冗余对象
+ `不需要建立的对象就不能建立`；
```
public void doSomething(){
       //异常信息
       String exceptionMsg = "我出现了异常，快来救我";
       try{
              Thread.sleep(10);
       }catch(Exception e){
              //转换为自定义运行期异常
              throw new MyException(e , exceptionMsg);
       }
}
```
+ `在catch块中定义exceptionMsg才是正确的，需要的时候才创建对象`；`一段程序需要三种资源：CPU、内存、I/O，提升CPU的处理速度可以加快代码的执行速度，直接表现就是返回时间缩短了，效率提高了`，`内存是Java程序必须要考虑的问题`。`在32位的机器上，一个JVM最多只能使用2GB的内存，而且程序占用的内存越大，寻址效率就越低，这也是影响效率的一个因素`，`I/O是程序展示和存储数据的主要通道，如果它很缓慢就会影响正常的显示效果，所以我们在编码时需要从这三方面入手接口（当然了，任何程序优化都是从这三方面入手的）`；

#### 必须定义性能衡量标准
+ `性能衡量标准是技术与业务之间的契约，性能衡量标志是技术优化的目标，int i = 100 * 16；如果我们为了提升系统性能，使用左移的方式来计算int i = 100 << 4`；

#### 调整JVM参数以提升性能

|四种常用的JVM优化手段|
|------|
|`调整内存大小`；在JVM中有两种内存：`栈内存，堆内存`，`栈内存的特点是空间比较小，速度快，用来存储对象的引用及程序中的基本类型`，`而堆内存的特点是比较大，速度慢，一般对象都会在这里生成，使用和消亡`，`栈内存是由线程开辟的，线程结束的，栈内存由JVM回收，因此它的大小一般不会对性能有太大的影响，但是它会影响系统的稳定性，在超过栈内存的容量时，系统会报StackOverflowError错误`，可以通过`java -Xss <size>`设置栈内存大小来解决此类问题，；`堆内存的调整不能太随意，调整的大小，会导致Full GC频繁执行，轻则导致系统性能急剧下降，重则导致系统根本无法使用`；`调整的太大，一则是浪费资源（当然，若设置了最小堆内存则可以避免此问题）`，`二则是产生系统不稳定的情况，例如在32位的机器上设置超过1.8GB的内存就有可能产生莫名其妙的错误，设置初始化堆内存为1G（也就是最小堆内存），最大堆内存为1.5GB`可以使用如下命令：`java -Xmx1536m -Xms1024m`|
|`调整堆内存中各分区的比例`；JVM的堆内存包括三部分：`新生区`、`养老区`、`永久存储区`，其中`新生成的对象都在新生区`，它有分为`伊甸区`、`幸存0区`、`幸存1区`，`当在程序中使用new关键字时，首先在伊甸区生成该对象`，如果伊甸区满了，则用垃圾回收器先进行回收，然后把剩余的对象移动到`幸存区（0区或1区）`，可如果幸存区满了，垃圾回收器会再回收一次，然后再把剩余的对象移动到`养老区`，如果`养老区`也满了，此时就会触发`Full GC（这是一个非常危险的动作，JVM会停止所有的执行，所有系统资源都会让位于垃圾回收器）`，会对所有的对象过滤一遍，检查是否有可以回收的对象，如果还是没有的话，就抛出`OutOfMemoryError`错误，系统就不干了；一般情况下`新生区`和`养老区`的比例为`1：3`左右，设置命令为：`java -XX:NewSize=32m -XX:MaxNewSize=640m -XX.MaxPermSize=1280m -XX:NewRatio=5`；设置`新生区初始化为32MB（也就是最小值是32，最大值是640）`，`养老区最大不超过1280MB，新生区和养老区的比例为1：5`|
|`变更GC的垃圾回收策略`；Java程序性能的最大障碍就是垃圾回收，我们不知道它何时会发生，也不知道它会执行多长时间，但是我们可以想办法改变它对系统的影响，比如启动并行垃圾回收、规定并行回收的线程数量等，命令格式为：`java -XX:+UseParallelGC -XX:ParallelGCThreads=20`，这里启动了并行垃圾收集机制，并且定义了20个收集线程（默认的收集线程等于CPU的数量），这对多CPU的系统是非常有帮助的，可以大大减少垃圾回收对系统的影响，提高系统性能，当然，垃圾回收的策略还有很多属性可以修改，比如UseSerialGC启动串行GC，默认值；ScavengeBeforeFullGC新生代GC优先于Full GC执行；UseConcMarkSweepGC对老生代采用并发标记交换算法进行GC等，这些参数需要在系统中逐步调试|
|`更换JVM`；`如果所有方法都不见效，则可以选择更换JVM`|

#### 若非必要、不要克隆对象
```
class Client {
    public static void main(String[] args) throws CloneNotSupportedException {
        final int maxLogs = 10000 * 10;
        int loops = 0;
        long start = System.nanoTime();
        Apple apple = new Apple();
        while (++loops < maxLogs) {
            apple.clone();
        }
        long mid = System.nanoTime();
        System.out.print("clone生成对象消耗的时间" + (mid - start) + "ns");
        while (--loops > 0) {
            new Apple();
        }
        long end = System.nanoTime();
        System.out.print("new生成对象消耗的时间" + (end - mid) + "ns");
    }
}

class Apple implements Cloneable {
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
//该方式clone消耗的时间远大于new消耗的时间
```
+ `一般情况下，new生成的对象比clone生成的性能方面要好很多`；`原因是Apple的构造函数非常简单，而且JVM对new做了大量的性能优化，而clone方法只是一个冷避的生成对象方式，并不是主流，它主要是用于构造函数比较复杂，对象属性比较多`，`通过new关键字创建一个对象比较消耗时间的时候；克隆对象并不比直接生成对象效率高`；
