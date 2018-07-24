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


### 算法
[算法](https://github.com/ningbaoqi/Java/blob/master/README-suanfa.md)
