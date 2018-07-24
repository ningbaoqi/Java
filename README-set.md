### Set集合
+ Set集合不允许包含相同的元素，如果试图把两个相同的元素添加到同一个Set集合，则添加操作失败，add()方法返回false，则新元素不会被加入；
### HashSet类
+ HashSet按Hash算法开存储集合中的元素，因此具有良好的存取和查找性能；

|HashSet具有以下特点|
|------|
|不能保证元素的排列顺序，顺序可能与添加顺序不同，顺序也可能发生变化|
|HashSet不是同步的，如果多线程同时访问一个HashSet，假设同时修改HashSet集合时，必须通过代码来保证其同步|
|集合元素可以是null|

+ 当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据该hashCode值决定对象在HashSet只中的存储位置，如果两个元素通过equals()方法返回true，但是他们的hashCode方法返回值不同，HashSet将会把他们存储在不同的位置，依然可以添加成功；也就是说HashSet判断两个元素是不是相同的条件就是：equals方法返回true，hashCode方法返回值也相等；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-40.jpg) 

### LinkedHashSet类
+ HashSet还有一个子类LinkedHashSet，LinkedHashSet集合也根据元素的hashcode值来决定元素的存储位置，但他同时使用链表维护元素的次序，这样使得元素看起来是以插入的顺序保存的，也就是说，当遍历LinkedHashSet集合里的元素时，LinkedHashSet将会按元素的添加顺序来访问集合里的元素；性能略低于HashSet，但是在迭代Set里的全部元素时将有很好的性能；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-41.jpg) 

### TreeSet类
+ TreeSet是SortedSet接口的实现类，TreeSet可以确保集合元素处于排序状态，TreeSet还提供了几个额外的方法；

|方法|说明|
|------|-------|
|Comparator comparator()|如果TreeSet采用了定制排序，则该方法返回定制排序所使用的Comparator，如果TreeSet采用了自然排序，则返回null|
|Object first()|返回集合中的 第一个元素|
|Object last()|返回集合中的最后一个元素|
|Object lower(Object e)|返回集合中位于指定元素之前的元素|
|Object higher(Object e)|返回集合中位于指定元素之后的元素|
|SortedSet subSet(Object fromElement ,Object toElement )|返回此set的子集合，范围从fromElement（包含）到toElement（不包含）|
|SortedSet headSet(Object toElement )|返回此set 的子集合，由小于toElement的元素组成|
|SortedSet tailSet(Object fromElement )|返回此set 的子集合，由大于或等于fromElement的元素组成|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-42.jpg) 

+ TreeSet并不是根据元素的插入顺序进行排序的，而是根据实际元素值的大小来进行排序的；TreeSet采用红黑树的数据结构来存储集合元素，TreeSet支持两种排序方法，自然排序和定制排序；在默认情况下，TreeSet采用自然排序；

#### 自然排序
+ TreeSet会调用集合元素的compareTo(Object obj)方法来比较元素之间的大小关系，然后将集合元素按升序排列，这种方法叫做自然排序；如果试图把一个对象添加到TreeSet中，则该对象的类必须实现Comparable接口，否则程序会抛出异常；


![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-43.jpg) 

+ 向TreeSet集合中添加元素时，只有第一个元素无需实现Comparable接口，后面添加的所有元素偶必须实现，当然这不是一个好做法，当试图从TreeSet中取出元素时，依然会引发ClassCastException异常；因为只有相同的类的两个实例才会比较大小，向TreeSet中添加的应该是同一个类的实例，否则会报ClassCastException异常；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-44.jpg) 

+ 如果两个对象通过compareTo方法比较相等，新对象就无法添加到TreeSet集合中；对于TreeSet集合而言，他判断两个对象是否相等的唯一标准就是两个对象通过compareTo方法比较是否返回0，如果返回0，TreeSet就会认为他们相等，否则他们就不相等；

#### 定制排序
+ 定制排序就是修改添加进的元素的类的compareTo方法，让它改变流程就可以实现逆序了；
### EnumSet类
+ EnumSet是一个专门为枚举类型设计的集合类，EnumSet中的所有元素都必须是指定枚举类型的枚举值，该枚举类型在创建EnumSet时显式或隐式的指定，EnumSet的集合元素也是有序的，EnumSet以枚举值在Enum中定义的顺序来决定集合元素的顺序；EnumSet对象占用内存很小，而且运行效率很好，尤其是做批量处理；EnumSet不允许添加null元素，如果试图添加null元素，EnumSet会抛出NullPointerException异常；EnumSet类没有暴露任何构造器来创建该类的实例，程序应该通过他提供的类方法来创建EnumSet对象：如：

|方法|说明|
|------|------|
|EnumSet allOf(Class elementType)|创建一个包含指定枚举类里所有枚举值的EnumSet集合|
|EnumSet complementOf(EnumSet s)|创建一个其元素类型与指定EnumSet里元素类型相同的EnumSet集合，新EnumSet集合包含原EnumSet集合所不包含的此枚举剩下的枚举值|
|EnumSet copyOf(Collection c)|使用一个普通集合来创建EnumSet集合|
|EnumSet copyOf(EnumSet s)|创建一个与指定EnumSet具有相同元素类型、相同集合元素的EnumSet集合|
|EnumSet noneOf(Class elementType)|创建一个元素类型为指定枚举类型的空EnumSet|
|EnumSet of(E first , E...rest)|创建一个包含一个或多个枚举值的EnumSet集合，传入的多个枚举值必须属于同一个枚举类|
|EnumSet range(E from ,E to )|创建一个包含从from到to枚举值范围内所有枚举值的EnumSet集合|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-45.jpg) 
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-46.jpg) 

+ 当试图复制一个Collection集合里的元素来创建EnumSet集合时，必须保证Collection集合里的所有元素都是同一个枚举类的枚举值；

### 各Set实现类的性能分析
+ HashSet的性能总是比TreeSet好，因为TreeSet需要额外的红黑树算法来维护集合元素的次序，只有当需要一个保持排序的Set时，才应该使用TreeSet，否则都应该使用HashSet；HashSet还有一个子类LinkedHashSet，对普通的插入、删除操作，LinkedHashSet比HashSet要略慢一点，这是由维护链表所带来的额外开销，但由于使用链表，遍历LinkedHashSet会更快；EnumSet是所有Set实现类中性能最好的，但他只能保存同一个枚举类的枚举值作为集合元素；Set的三个实现类HashSet、HashSet、EnumSet都是线程不安全的，如果有多个线程同时访问一个集合。并且有超过一个线程改变了该Set集合，则必须手动保证该Set集合的同步性，通常可以通过Collections工具类的synchronizedSortedSet方法来包装该Set集合，此操作最好在创建时进行，以防止对Set集合的意外非同步访问；如：

```
SortedSet s = Collections.synchronizedSortedSet(new TreeSet(......));
```
