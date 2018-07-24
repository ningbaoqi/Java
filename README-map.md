### Java8增强的Map集合
+ Map用于保存具有映射关系的数据，因此Map集合里保存着两组值，一组值用于保存Map中的key，另外一组值用于保存Map里的value；

|Map接口定义了如下常用的方法|说明|
|-------|-------|
|void clear()|删除该Map对象中的所有键值对|
|boolean containsKey(Object key)|查询Map中是否包含指定的key，如果包含则返回true|
|boolean containsValue(Object value)|查询Map中是否包含一个或多个指定的value，如果包含则返回true|
|Set entrySet()|返回Map中包含键值对所组成的Set集合，每个集合元素都是Map.Entry（Entry是Map的内部类）对象|
|Object get(Object key)|返回指定key所对应的value，如果此Map中不包含该key，则返回null|
|boolean isEmpty()|查询该Map是否为空（即不包含任何键值对）如果为空则返回true|
|Set keySet()|返回该Map所有key组成的Set集合|
|Object put(Object key,Object value)|添加一个键值对，如果当前已有一个与该key相等的键值对，则新的键值对会覆盖原来的键值对|
|void putAll(Map m)|将指定的Map中的键值对复制到本Map中|
|Object remove(Object key)|删除指定key所对应的键值对，返回被删除的key所关联的value，如果该key不存在则返回null|
|boolean remove(Object key , Object value)|这是java8新增的方法，删除指定key、value所对应的键值对，如果从该Map中成功的删除了，则返回true，否则返回false|
|int size()|返回该Map里的键值对的个数|
|Collection values()|返回该Map里所有value组成的Collection|

+ Map中包括一个内部类Entry，该类封装了一个键值对，Entry包含以下三个方法：

|方法|说明|
|------|------|
|Object getKey()|返回该Entry里包含的key值|
|Object getValue()|返回该Entry里包含的value值|
|Object setValue(V value)|设置该Entry里包含的value值，并返回新设置的value值|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-60.jpg) 

### Java8为Map新增的方法

|方法|说明|
|------|------|
|Object compute(Object key , BiFunction remappingFunction )|该方法使用remappingFunction根据原键值对计算一个新的value，只要新的value不为null，就使用新的value覆盖原来的value，如果原来的balue不为null，但新的value为null，则删除原来的键值对，如果原来和新的value都为null，那么该方法不改变任何的键值对，直接返回null|
|Object computeIfAbsent(Object key,Function mappingFunction)|如果传给该方法的key参数在Map中对应的value为null，则使用mappingFunction根据key计算一个新的结果，如果计算结果不为null，则用计算结果覆盖原有的value，如果原Map不包含该key，那么该方法可能会添加一个键值对|
|Object computeIfPresent(Object key ,BiFunction remappingFunction)|如果传给该方法的key 参数在Map中对应的value不为null，该方法将使用remappingFunction根据原键值对计算一个结果，如果计算结果不为null，则使用该计算结果覆盖原来的value，如果计算结果为null，则删除原来的键值对|
|void forEach(BiConsumer action)|该方法是java8为Map新增的一个遍历键值对的方法，通过该方法可以更简洁的遍历Map的键值对|
|Object getOrDefault(Object key,V defaultValue)|获取指定key对应的value，如果该key不存在，则返回defaultValue|
|Object merge(Object key,Object value,BiFunction remappingFunction)|该方法会先根据key参数获取该Map中对应的value，如果获取的value为null，则直接用传入的value覆盖原有的value，如果获取的value不为null，则使用remappingFunction函数根据原value、新的value计算一个新的结果，并用得到的结果去覆盖原有的value|
|Object putIfAbsent(Object key ,Object value)|该方法会自动检测指定的key对应的value是否为null，如果该key对应的value为null，该方法将会用新的value替代原来的null值|
|Object replace(Object key , Object value)|将Map中指定的key对应的value替换成新的value，与传统的put方法不同的是，该方法不可能添加新的键值对，如果尝试替换的key在原Map中不存在，该方法不会添加键值对，而是返回null|
|booelan replace(K key , V oldvalue , V newValue)|将Map中指定的键值对的原value替换成新value，如果在Map中找到指定的键值对，则执行替换并返回true，否则返回false|
|replaceAll(BiFunction remappingFunction)|该方法使用BiFunction对原键值对执行计算，并将计算结果作为该键值对的value值|

### Java8改进的HashMap和HashTable实现类

|HashMap和HashTable都是Map接口的典型实现类；HashTable和HashMap的区别|
|------|
|HashTable是一个线程安全的Map实现，但是HashMap是线程不安全的实现，所以HashMap比HashTable的性能高一点，但是如果有多线程访问同一个Map对象时，使用HashTable实现类会更好|
|HashTable不允许使用null作为key和value，如果试图将null值放进HashTable中，将会引发空指针异常，但是HashMap可以使用null作为key或value|

+ HashMap最多只能有一个键为null，但是可以有很多的null value；为了成功的在HashMap 、HashTable中存储、获取对象，用做key的对象必须实现hashCode()方法和equals()方法；HashMap、HashTable判断两个key相等的标准是：两个key通过equals方法比较返回true，两个key的hashCode值也相等；HashMap、HashTable判断两个value相等的标准更简单，只要两个对象通过equals方法返回true即可；

### LinkedHashMap实现类
+ LinkedHashMap 也使用双向链表来维护键值对的次序，该链表负责维护Map的迭代顺序，迭代顺序与键值对的插入顺序保持一致；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-61.jpg) 

### 使用Properties读写属性文件
+ Properties类是HashTable类的子类，不保证键值对的顺序，Properties类可以把Map对象和属性文件关联起来，从而可以把Map对象中的键值对写入属性文件中，也可以把属性文件中的属性名=属性值加载到Map对象中，由于属性文件里的属性名、属性值只能是字符串类型，所以Properties里的key、value都是字符串类型，该类提供了三个方法来修改Properties里的key、value值；

|方法|说明|
|------|-------|
|String getProperty(String key)|获取Properties中指定属性名对应的属性值，类似于Map的get(Object key)方法|
|String getProperty(String key,String defaultValue)|该方法与上个方法类似，该方法多了一个功能，如果Properties中不存在指定的key时，则该方法指定默认值|
|Object setProperty(String key, String value)|设置属性值，类似put方法|

+ 用于读写属性文件的方法；

|方法|说明|
|------|------|
|void load(InputStream inStream)|从属性文件中加载键值对，把加载到的键值对追加到Properties里|
|void store(OutputStream out , String comments)|将Properties中的键值对输出到指定的属性文件中|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-62.jpg) 

+ Properties可以把键值对以XML文件的形式保存起来，也可以从XML文件中加载键值对，用法类似；
### SortedMap接口和TreeMap实现类
+ TreeMap就是一个红黑树数据结构，每个键值对即作为红黑树的一个节点，TreeMap存储键值对时，需要根据key对节点进行排序，TreeMap可以保证所有的键值对处于有序状态，TreeMap也有两种排序方式：一、自然排序：TreeMap的所有key必须实现Comparable接口，而且所有的key应该是同一个类的对象，否则将会抛出异常；二、定制排序：创建TreeMap时，传入一个Comparable对象，该对象负责对TreeMap中的所有key进行排序，采用定制排序时不要求Map的key实现Comparable接口；TreeMap中判断两个key相等的标i准是：两个key通过compareTo()方法返回0，TreeMap即认为两个key是相等的；

|TreeMap中提供的根据key顺序访问键值对的方法|说明|
|------|------|
|Map.Entry firstEntry()|返回该Map中最小的key所对应的键值对，如果该Map为空，则返回null|
|Object firstKey()|返回该Map中的最小的key值，如果该Map为空。则返回null|
|Map.Entry lastEntry()|返回该Map中最大key所对应的键值对，如果该Map为空或不存在这样的键值对，则都返回null|
|Object lastKey()|返回该Map中的最大key值，如果该Map为空或不存在这样的key，则返回null|
|Map.Entry higherEntry(Object key)|返回该Map中位于key后一位的键值对，如果该Map为空，则返回null|
|Object higherKey(Object key)|返回该Map中位于key后一位的key值，如果该Map为空或不存在这样的键值对，则返回null|
|Map.Entry lowerEntry(Object key)|返回该Map中位于key前一位的键值对，如果该Map为空或不存在这样的键值对，则返回null|
|Object lowerKey(Object key)|返回该Map中位于一个key前一位的key，如果该Map为空或不存在这样的键值对，则返回null|
|NavigableMap subMap(Object fromKey , boolean fromInclusive , Object toKey , boolean toInclusive)|返回该Map的子Map，其key的范围是从fromKey（是否包含取决第二个参数）到toKey（是否包含取决第四个参数）|
|SortedMap subMap(Object fromkey,Object toKey)|返回该Map的子Map，该key的范围是fromkey（包括）到toKey（不包括）|
|SortedMap tailMap(Object fromKey)|返回该Map的子Map，其key的范围是大于fromKey的所有key，并且包含fromKey|
|NavigableMap tailMap(Object fromKey， boolean fromInclusive)|返回该Map的子Map，其key的范围是大于fromKey的所有key，其是否包含参见第二个参数|
|SortedMap headMap(Object toKey)|返回该Map的子Map，其key的范围是小于tokey（不包含）的所有key|
|NavigableMap headMap(Object toKey，boolean toInclusive)|返回该Map的子Map，其key的范围是小于toKey的所有key，其是否包含参见第二个参数|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-63.jpg) 

### WeakHashMap实现类

+ HashMap的key保留了对实际对象的强引用，这意味着只要该HashMap对象不被销毁，该HashMap的所有key所引用的对象就不会被垃圾回收，HashMap也不会自动删除这些key所对应的键值对，但是WeakHashMap的key只保留了对实际对象的弱引用，这意味着如果WeakHashMap对象的key所引用的对象没有被其他强引用变量所引用，则这些key所引用的对象可能被垃圾回收，WeakHashMap也可能自动删除这些key所对应的键值对；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-64.jpg) 

+ 如果需要使用WeakHashMap的key来保存对象的弱引用，则不要让该key所引用的对象具有任何强引用，否则将失去使用WeakHashMap的意义；

### IdentityHashMap实现类
+ 他在处理两个key相等时比较独特，在IdentityHashMap中，当且仅当两个key严格相等（key1 == key2）时，IdentityHashMap才会认为两个key相等，对于普通的HashMap而言，只要两个key 通过equals方法返回true且他们的hashCode值相等即可；IdentityHashMap提供了与HashMap基本相似的方法，也允许使用null作为key和value，与HashMap相似，IdentityHashMap也不保证键值对之间的顺序，更不能保证他们的顺序随时间的推移保持不变；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-65.jpg) 

### EnumMap实现类
+ EnumMap是一个与枚举类一起使用的Map实现，EnumMap中的所有的key都必须是单个枚举类的枚举值，创建EnumMap是必须显式或隐式指定他所对应的枚举类；

|EnumMap具有如下特征|
|------|
|EnumMap在内部以数组形式保存，所以这种实现形式非常紧湊、高效|
|EnumMap根据key的自然顺序（即枚举值在枚举类中的定义顺序）来维护键值对的顺序，当程序通过keySey()、entrySet()、values()等方法遍历EnumMap时可以看到这种顺序|
|EnumMap不允许使用null作为key，但允许使用null作为value，如果试图使用null作为key时将会抛出异常，如果只是查询是否包含值为null的key，或只是删除值为null的key，则不会抛出异常|

### 各Map实现类的性能分析
+ HashMap通常比HashTable快，TreeMap通常比HashMap、HashTable要慢，因为TreeMap底层使用红黑树来管理键值对，使用TreeMap的好处是：TreeMap中的键值对总是有序状态，无需专门的进行排序操作，当TreeMap被填充之后，就可以调用keySet方法获取由key组成的set，然后调用toArray方法生成key的数组，接下来使用Arrays的binarySearch方法在已排序好的数组中快速查找对象；对一般场景，程序应该多考虑使用HashMap，因为HashMap正是为快速查询设计的，但是如果程序需要一个总是排序好的Map，则使用TreeMap；LinkedHashMap比HashMap慢一点，因为他需要维护链表来保存Map中的键值对的添加顺序，IdentifyHashMap性能没有特别出色之处，因为采用与HashMap相似的实现，只是判断两个key的方法不同，EnumMap的性能最好，但是他只能使用同一个枚举类的枚举值来作为key；
