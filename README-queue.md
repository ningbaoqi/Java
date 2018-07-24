### Queue集合
+ Queue用于模拟队列这种数据结构，队列通常是指“先进先出”的容器，队列的头部保存在队列中存放时间最长的元素，队列的尾部保存在队列中存放时间最短的元素，新元素插入到队列的尾部，访问元素操作会返回队列头部的元素，通常，队列不允许随即访问队列中的元素；

|Queue接口定义的方法|说明|
|------|------|
|void add(Object c)|将指定元素加入此队列的尾部|
|Object element()|获取队列头部的元素，但是不删除该元素|
|boolean offer(Object c)|将指定的元素加入到此队列的尾部，当使用有容量限制的队列，此方法通常比add方法更好|
|Object peek()|获取队列头部的元素，但是不删除该元素，如果队列为空，则返回null|
|Object poll()|获取队列头部的元素，并删除该元素，如果队列为空。则返回null|
|Object remove()|获取队列头部的元素，并删除该元素|

+ Queue接口有一个PriorityQueue实现类，除此之外，Queue还有一个Deque接口，Deque代表一个双端队列，双端队列可以同时从两端添加删除元素，因此Deque的实现类既可以当成队列使用，也可以当成栈来使用；java的Deque提供了ArrayDeque和LinkedList两个实现类；

### PriorityQueue实现类
+ PriorityQueue保存队列元素的顺序并不是按加入队列的顺序，而是按队列元素的大小进行重新排序，因此当调用peek()方法或者poll()方法取出队列中的元素时，并不是取出最先进入队列的元素，而是取出队列中最小的元素；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-47.jpg) 

+ PriorityQueue不允许插入null元素，它还需要对队列元素进行排序，PriorityQueue的元素两种排序方法：一、自然排序：采用自然顺序的PriorityQueue集合中的元素必须实现了Comparable接口，而且应该是同一个类的多个实例，否则就会报异常；二、定制排序：创建PriorityQueue队列时，传入的Comparable对象，该对象负责对队列中的所有元素进行排序，采用定制排序时不要求队列元素实现Comparable接口；

### Deque接口和ArrayDeque实现类
+ Deque接口是Queue的子接口，他代表一个双端队列。Deque接口中定义的一个双端队列的方法：

|方法|说明|
|------|------|
|void addFirst(Object c)|将指定的元素插入到双端队列的开头|
|void addLast(Object c)|将指定的元素插入到双端队列的末尾|
|Iterator descendingIterator()|返回该双端队列对应的迭代器，该迭代器将以逆序来迭代队列中的元素|
|Object getFirst()|获取但不删除双端队列的第一个元素|
|Object getLast()|获取但不删除双端队列的最后一个元素|
|boolean offerFirst(Object c)|将指定元素插入该双端队列的开头|
|boolean offerLast(Object c)|将指定元素插入到双端队列的末尾|
|Object peekFirst()|获取但不删除该双端队列的第一个元素，如果此双端队列为空，则返回null|
|Object peekLast()|获取但不删除该双端队列的最后一个元素，如果此双端队列为空，则返回null|
|Object pollFirst()|获取并删除该双端队列的第一个元素，如果该双端队列为空，则返回null|
|Object pollLast()|获取并删除该双端队列的最后一个元素，如果该双端队列为空，则返回null|
|Object pop()|栈方法，pop出该双端队列所表示的栈的栈顶元素，相当于removeFirst()|
|void push(Object c)|栈方法，将一个元素push到该双端队列所表示的栈的栈顶，相当于addFirst()|
|Object removeFirst()|获取并删除该双端队列的第一个元素|
|Object removeFirstOccurrence(Object c)|删除该双端队列的第一次出现的元素c|
|Object removeLast()|获取并删除该双端队列的最后一个元素|
|Object removeLastOccurrence(Object c)|删除该双端队列的最后一次出现的元素c|

+ Deque接口提供了一个典型的实现类，ArrayDeque，它是基于数组实现的双端队列，创建Deque时同样可指定一个numElements参数，该参数用于指定Object[]数组的长度，如果不指定numElements参数，Deque底层数组的长度为16；

#### ArrayDeque当成栈来使用

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-48.jpg) 

#### ArrayDeque当成队列来使用

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-49.jpg)

### LinkedList实现类
+ LinkedList类是List接口的实现类，这意味着他是一个List集合，可以根据索引来随机访问集合中的元素，除此之外，LinkedList还实现了Deque接口，可以被当成双端队列来使用，因此既可以被当成栈来调用，也可以当成队列来调用；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-50.jpg)

+ LinkedList内部以链表的形式来保存集合中的元素，因此随机访问集合元素时性能较差，但是插入和删除元素时，性能极好；

### 各种线性表的性能分析
+ 由于数组以一块内存区来保存所有的数组元素，所以数组在随机访问时性能最好，所有的内部一数组作为底层实现的集合在随机访问时性能都比较好，而内部以链表的作为底层实现的集合在执行插入和删除操作时比较好的性能，但总体来说，ArrayList的性能比LinkedList的性能好，因此，大部分时候都应该考虑使用ArrayList；如果需要遍历List集合元素，对于ArrayList、Vector集合，应该使用随机访问方法get来遍历集合元素，这样性能更好，对于LinkedList集合，应该采用迭代器来遍历集合元素；如果需要经常插入删除操作来改变包含大量数据的List集合的大小，可考虑使用LinkedList集合，使用ArrayList、Vector集合可能需要经常重新分配内部数组的大小，效果可能较差；如果有多线程需要同时访问List集合中的元素，可以使用Collections将集合包装成线程安全的集合；
