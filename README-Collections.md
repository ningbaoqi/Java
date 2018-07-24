### 操作集合的工具类：Collections
+ Collections该工具类里提供了大量方法对集合进行排序、查找修改等操作，还提供了将集合对象设置为不可变、对集合对象实现同步控制等方法；
#### 排序操作

|Collections提供了如下常用方法的类方法用于对list集合元素进行排序方法|说明|
|------|------|
|void reverse(List list)|反转指定的List集合中的元素的顺序|
|void shuffle(List list)|对List集合元素进行随机排序（该方法提供了一个模拟洗牌的动作）|
|void sort(List list)|根据元素的自然顺序对指定的List集合的元素按升序进行排序|
|void sort(List list ,Comparator c)|根据指定的Comparator产生的顺序对List集合元素进行排序|
|void swap(List list , int i , int j)|将指定的List集合中的第i处元素和第j处元素进行交换|
|void rotate(List list , int distance)|当distance为正数时，将list集合的后distance个元素整体移动到前面，当distance为负数时，将list集合的前distance个元素整体移到后面，该方法不会改变集合的长度|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-55.jpg) 

### 查找、替换操作

|Collections还提供了如下常用的用于查找、替换集合元素的类方法|说明|
|------|-------|
|int binarySearch(List list，Object key)|使用二分法搜索指定的List集合，以获取指定对象在List集合中的索引，如果要使该方法可以正常工作，则必须保证List集合处于有序状态|
|Object max(Collection c)|根据元素的自然顺序，返回给定的集合中的最大元素|
|Object max(Collection c,Comparator comp)|根据Comparator指定的顺序，返回给定的集合中的最大元素|
|Object min(Collection c)|根据元素的自然顺序，返回给定的集合中的最小元素|
|Object min(Collection c,Comparator comp)|根据Comparator指定的顺序，返回给定的集合中的最小元素|
|void fill(List list , Object obj)|使用指定元素obj替换指定List集合中的所有元素|
|int frequency(Collection c，Object o)|返回指定集合中指定的元素的出现次数| 
|int indexOfSubList(List source,List target)|返回子list对象在父list对象中第一次出现的位置索引，如果不存在，则返回-1|
|int lastIndexOfSubList(List source,List target)|返回子list对象在父list对象中最后一次出现的位置索引，如果不存在，则返回-1|
|boolean replaceAll(List list , Object oldVal,Object newVal)|使用一个新值newVal替换List对象的所有旧值oldVal|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-56.jpg) 

### 同步控制
+ Collections类中提供了多个`synchronizedXxx()`方法，该方法可以将指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-57.jpg) 

### 设置不可变的集合

|Collections提供了如下三个类方法来返回一个不可变的集合|说明|
|------|------|
|emptyXxx()|返回一个空的，不可变的集合对象，此处的集合既可以是List也可以是SortedSet、Set还可以是Map、SortedMap等|
|singletonXxx()|返回一个只包含指定对象（只有一个或一项元素）的、不可变的集合对象，此处的集合既可以是List，还可以是Map|
|unmodifiableXxx()|返回指定集合对象的不可变视图，此处的集合既可以是List也可以是Set，SortedSet，还可以是Map、SortedMap等|

+ 上面三类方法的参数是原有的集合对象，返回值是该集合的只读版本，通过Collections提供的三个方法，可以生成只读的Collection或Map；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-58.jpg) 
