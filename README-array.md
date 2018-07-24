### 数组类型
#### 理解数组：数组也是一种类型
+ `一旦数组的初始化完成，数组在内存中所占的空间将被固定下来，因此数组的长度将不可改变，即使把某个数组元素的数据清空，它所占的空间依然被保留，依然属于该数组，数组的长度依然不变`；
#### 为什么会有栈内存和堆内存之分呢？
+ `当一个方法执行时，每个方法都会建立自己的内存栈，在这个方法内定义的变量将会逐个放入这块栈内存中，随着方法的执行结束，这个方法的内存栈也将会自然销毁`，因此，`所有在方法中定义的局部变量都是放在栈内存的，在程序中创建一个对象时，这个对象将会被保存到运行时数据区中，以便反复利用（因为创建对象的成本较大），这个运行时数据区就是堆内存`，`堆内存中的对象不会随方法的结束而销毁，即使方法结束了，这个对象还可能被另一个引用变量引用，则这个对象依然不会被销毁，只有当一个对象没有任何引用变量引用它的时候，系统的垃圾回收机制才会在合适的时候将其回收`；

### Java8增强的工具类Arrays
+ Java提供了Arrays类里包含的一些static修饰的方法可以直接操作数组，这个Arrays类里包含了如下几个static修饰的方法； 除此之外，在System类里也包含了一个`static void arraycopy(0bject sec , int srcPos , Object dest , int destPos , int length)`方法，该方法可以将src数组里的元素值赋给dest数组的元素，其中srcPos指定从src数组的第几个元素开始赋值，length参数指定将src数组的多少个元素值赋给dest数组的元素；

|方法|说明|
|------|------|
|int binarySearch(type[] a,type k )|使用二分法查找key元素值在a数组中出现的索引，如果a数组不包含key元素值，则返回负数，调用该方法时要求数组中的元素已经按升序排列，这样才能得到正确的结果；|
|int binarySearch(type[] a ,int fromindex,int toindex,type key)：|这个方法与前一个方法类似，但它只搜索a数组中fromindex到toindex索引的元素，调用该方法时要求数组中的元素已经按升序排列，这样才能得到正确结果；|
|type[] copyOf(type[] original,int lenght)：|这个方法将会把original数组复制成一个新数组，其中length是新数组的长度，如果length小于original则新数组就是原数组的前面length个元素，如果length大于original数组的长度，则新数组的前面元素就是原数组的所有元素，后面补充默认值；|
|type[] copyOfRange(type[] original,int from ,int to)：|这个方法与前面的方法类似，但这个方法只复制original数组的from索引到to索引的元素；|
|boolean equals(type[] a,type[] b)：|如果a数组与b数组的长度相等，而且a数组和b数组的数组元素也一一相同，该方法将会返回true；|
|void fill(type[] a ,type val)：|该方法将会把a数组的所有元素都赋值为val；|
|void fill(type[] a ,int fromindex,int toindex,type val)：|该方法与前一个方法的作用相同，区别只是该方法仅仅将a数组的fromindex(包含)到toindex（不包含）索引的数组元素赋值为val；|
|void sort(type[] a)：|该方法对a数组的数组元素进行排序，从小到大；|
|void sort(type[] a ,int fromindex , int toindex)：|该方法与前一个方法相似，区别是该方法仅仅对fromindex到toindex索引的元素进行排序；|
|String toString(type[] a )：|该方法将一个数组转换成一个字符串，该方法按顺序把多个数组元素连在一起，多个数组元素使用英文逗号和空格隔开；|

#### Arrays例子
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-3.jpg) 

#### Java8增强的工具类Arrays的并行函数
+ Java8增强了Arrays类的功能，为Arrays类增加了一些工具方法，这些工具方法可以充分利用多CPU并行的能力来提高设值、排序的功能；

|方法|说明|
|------|------|
|void parallelPrefix(xxx[] array,XxxBinaryOperator op)|该方法使用op参数指定的计算公式计算得到的结果作为新的元素，op计算公式包括left、right两个行参，其中left代表数组中前一个索引处的元素，right代表数组中当前索引处的元素，当计算第一个新数组元素时，left的值默认为1；|
|void parallelPrefix(xxx[] array, int fromindex,int toindex,XxxBinaryOperator op)|该方法与上一个方法类似，区别是该方法仅重新计算fromindex到toindex索引的元素；|
|void setAll(xxx[] array, IntToXxxFunction generator)|该方法使用指定的生成器generator为所有数组元素设置值，该生成器控制数组元素的值的生成算法；|
|void parallelSort(xxx[] array,  IntToXxxFunction generator)|该方法的功能与上一个方法的功能相同，只是该方法增加了并行功能，可以利用多CPU并行来提高性能；|
|void parallelSort(xxx[] a)|该方法的功能与Arrays类的sort方法相似，只是该方法增加了并行功能，可以利用多CPU并行来提高性能；|
|void parallelSort(xxx[] a，int fromindex,int toindex)|与上一个方法相似，区别是该方法仅仅在fromindex和toindex之间的元素进行排序；|
|Spliterator.OfXxx spliterator(xxx[] array)|将该数组的所有元素转换成对应的Spliterator对象；|
|Spliterator.OfXxx spliterator(xxx[] array，int startInclusive, int endExclusive)|该方法作用与上一方法相似，区别是该方法仅转换区间的元素；|
|XxxStream stream(xxx[] array)|该方法将数组转换为stream，Stream是Java8新增的流式编程的API；|
|XxxStream stream(xxx[] array，int from , int to)|该方法与上一方法相似，区别在于值转换区间内的元素；|

+ 所有以`parallel`开头的方法都表示该方法可利用CPU并行的能力来提高性能，上面的`Xxx`指的是不同的类型；

### 数组建议
#### 多种最值算法，适时选择
##### 自行实现，快速查找最大值
```
public static int max(int[] data) {
    int max = data[0];
    for (int i:data) {
        max = max > i ? max : i;
    }
    return max;
}
```
+ `这是我们经常使用的最大值算法，也是速度最快的算法，它不要求排序，只要遍历一遍数组即可找到最大值`；
##### 先排序，后取值
```
public static int max(int[] data) {
    Arrays.sort(data.clone());
    return data[data.length - 1];
}
```
+ `如果数组数量少于10000时，两者基本没有差别，在同一毫秒级别里，此时就可以不用自己写算法了，直接使用数组先排序后取值的方式；最值计算时使用集合最简单，使用数组性能最优`；
#### asList方法产生的List对象不可更改
```
public class Test {
    public static void main(String[] args) {
        Week[] workDays = {Week.MON, Week.TUE, Week.WED, Week.THU, Week.FRI};
        List list = Arrays.asList(workDays);
        list.add(Week.SAT);//运行时异常；UnSupportedOperationException
    }
}

enum Week {
    SUN , MON , TUE , WED , THU , FRI , SAT
}
```
+ `这是因为通过Arrays的asList方法返回的不是java.util.ArrayList，而是Arrays工具类的一个内置类，在该类中是不允许容器数据改变的`；

|ArrayList静态内部类实现的5个方法|
|------|
|`size：元素数量`|
|`toArray：转化为数组，实现了数组的浅拷贝`|
|`get：获取制定元素`|
|`set：重置某一元素值`|
|`contains：是否包含某元素`|

+ `所以asList返回的是一个长度不可变的列表，数组是多长，转换成的列表也就是多长`；
#### 避开基本类型数组转换集合列表陷阱
```
public class Test {
    public static void main(String[] args) {
        int[] data = {1, 2, 3, 4, 5};
        List list = Arrays.asList(data);//从asList方法的实现来看，是因为传递的参数是泛型的，所以是将这个数组当作了一个对象进行处理的
        list.size();//输出1
        list.get(0).getClass();//输出class [I
        data.equals(list.get(0));//true

        //修改方案就是直接使用包装类
        Integer[] data1 = {1, 2, 3, 4, 5};
        List list1 = Arrays.asList(data1);
        list1.size();//输出5
    }
}
```
+ 注意：`在把基本类型数组转换成容器列表时，要特别小心asList方法的陷阱，避免出现程序逻辑混乱的情况；原始类型数组不能作为asList的输入参数，否则会引起程序逻辑混乱`；
#### 警惕数组的浅拷贝
```
public class Test {
    public static void main(String[] args) {
        int ballNum = 7;
        Ballon[] ball = new Ballon[ballNum];
        for (int i = 0; i < ballNum; i++) {
            ball[i] = new Ballon(Color.values()[i], i);
        }
        Ballon[] ball2 = Arrays.copyOf(ball, ball.length);//拷贝第一个箱子
        ball2[6].setColor(Color.BLUE);//之后输出ball时，ball的第七个元素的Color为BLUE
    }
}

enum Color {
    RED , ORANGE , YELLOW , GREEN , INDIGO , BLUE , VOILET
}

class Ballon {
    private int id;
    private Color color;

    public Ballon(Color color, int id) {
        this.color = color;
        this.id = id;
    }

    public void setColor(Color color) {
        this.color = color;
    }
}
```
+ `通过copyOf方法产生的数组是一个浅拷贝，基本类型是直接拷贝值，其他都是拷贝引用地址`，`需要说明的是，数组的clone方法也是浅拷贝，而且集合的clone方法也是浅拷贝，这就需要大家在拷贝时多留心了`；

### 算法
[算法](https://github.com/ningbaoqi/Java/blob/master/README-suanfa.md)
