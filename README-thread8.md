### 线程相关类
#### ThreadLocal类
+ `线程局部变量ThreadLocal`的功能其实非常简单，就是`为每一个使用该变量的线程都提供一个变量值的副本`，`使每个线程都可以独立地改变自己的副本，而不会和其他线程的副本冲突`，从线程的角度看，就好像每个线程都完全拥有该变量一样；

|ThreadLocal类的用法非常简单，他只提供了如下几个方法|说明|
|-------|--------|
|T get()|返回此线程局部变量中当前线程副本中的值|
|void remove()|删除此线程局部变量中当前线程的值|
|void set(T value)|设置此线程局部变量中当前线程副本中的值|

+ ThreadLocal从另一个角度看解决了多线程的并发访问，`ThreadLocal将需要并发访问的资源复制多份，每个线程拥有一份资源，每个线程都拥有自己的资源副本，从而也就没有必要对该变量进行同步了`；

#### 包装线程不安全的集合
+ 如果程序中有多个线程可能访问不安全的集合，就可以`使用Collections类提供的类方法把这些集合包装成线程安全的集合`，Collections提供了如下静态方法：

|方法|说明|
|------|-------|
|`<T> Collection<T> synchronizedCollection(Collection<T> c)`|返回指定c对应的线程安全的collection|
|`static<T> List<T> synchronizedList(List<T> list)`|返回指定list对应的线程安全的List|
|`static<K，V> Map<K，V> synchronizedMap(Map<K，V> map)`|返回指定map对应的线程安全的Map|
|`static<T> Set<T> synchronizedSet(Set<T> set)`|返回指定set对应的线程安全的Set|
|`static<K，V> SortedMap<K，V> synchronizedSirtedMap(SortedMap<K，V> map)`|返回指定map对应的线程安全的Map|
|`static<T> SortedSet<T> synchronizedSortedSet(SortedSet<T> set)`|返回指定set对应的线程安全的Set|

```
HashMap map = Collection.synchronizedMap(new HashMap());
```
#### 线程安全的集合类

|线程安全的集合类可分为两类|
|-------|
|以Concurrent开头的集合类，如ConcurrentHashMap，ConcurrentSkipListMap、ConcurrentSkipListSet、ConcurrentLinkedQueue、ConcurrentLinkedDeque|
|以CopyOnWrite开头的集合类，如CopyOnWriteArrayList、CopyOnWriteArraySet|

+ 其中以`Concurrent开头的集合类代表了支持并发访问的集合`，他们支持多个线程并发写入访问，这些写入线程的所有操作都是线程安全的，但读取操作不必锁定，以`Concurrent开头的集合类采用了更复杂的算法来保证永远不会锁定整个集合`，因此在并发写入时有较好的性能；
