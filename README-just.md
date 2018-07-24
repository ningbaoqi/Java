### 集合建议
#### 在明确的场景下，为集合指定初始容量
+ `ArrayList的初始化容量是10，非常有必要在集合初始化时声明容量；因为这样避免了性能上出现问题`；
#### 集合中的元素必须做到compareTo和equals同步
+ `在compareTo的返回为0时，它表示的是进行比较的两个元素是相等的`；`集合的indexOf方法是通过equals来判断相等的，而Collections的binarySearch方法是根据compareTo方法进行判断的，所以为了保证代码的严谨性，实现了compareTo方法，就应该重写equals方法，并且确保二者同步`；
#### 集合运算时使用更优雅的方式

|集合|说明|
|------|------|
|并集|也叫做合集，把两个集合加起来即可；`list1.addAll(list2)`|
|交集|计算两个集合的共有元素，`list1.retainAll(list2)`；使用该方法会删除list1中没有出现在list2中的元素|
|差集|由所有属于A但不属于B的元素组成的集合，`list1.removeAll(list2)`|
|无重复的并集|并集是集合A加集合B，如果集合A和集合B有交集，就需要确保并集的结果中只有一份交集，此为无重复的并集，`list2.removeAll(list1)；list1.addAll(list2)`|

#### 减少HashMap中元素的数量
+ `因为HashMap是通过内部类封装数据的，所以，在有大量的数据的时候，将会创建出大量的对象，而且从HashMap的扩容机制上，也会导致很大的内存开销，所以尽量让HashMap中的元素少量并简单`；
#### 非稳定排序推荐使用List
+ `SortedSet接口（TreeSet实现了该接口）只是定义了在给集合加入元素时将其进行排序，并不能保证元素修改后的排序结果，因此TreeSet适用于不变量的集合数据排序`，比如：`String、Integer、`等类型，但`不适用于可变量的排序`，`特别是不确定何时元素会发生变化的数据集合`，`SortedSet中的元素被修改后可能会影响其排序位置`；`在可能改变的数据时，可以使用List解决问题，然后再使用Collections.sort()方法对List排序，代码比较简单`；
#### 由点及面，一叶知秋-------集合大家庭

|集合分类说明|
|-------|
|List；实现List接口的集合主要有：ArrayList、LinkedList、Vector、Stack，其中ArrayList是一个动态数组，LinkedList是一个双向链表，Vector是一个线程安全的动态数组，Stack是一个对象栈，遵循先进后出的原则|
|Set；Set是不包含重复元素的集合，其主要的实现类有：EnumSet、HashSet、TreeSet，其中EnumSet是枚举类型的专用Set，所有元素都是枚举类型，HashSet是以哈希码决定其元素位置的Set，其原理与HashMap相似，它提供快速的插入和查找方法；TreeSet是一个自动排序的Set，它实现了SortedSet接口|
|Map；Map是一个大家庭，它可以分为排序Map和非排序Map，排序Map主要是TreeMap类，它根据Key值进行自动排序，非排序Map主要包括：HashMap、HashTable、Properties、EnumMap等，其中Properties是HashTable的子类，它的主要用途是从Property文件中加载数据，并提供方便的读写操作，EnumMap则是要求其Key必须是某一个枚举类型；Map中还有一个WeakHashMap类需要说明，它是一个采用弱键方式实现的Map类，它的特点是：WeakHashMap对象的存在并不会阻止垃圾回收器对键值对的回收，也就是说使用WeakHashMap装载数据不用担心内存溢出的问题，GC会自动删除不用的键值对，这是好事，但也存在一个严重问题，GC是静悄悄回收的，我们的程序无法知晓该动作，存在着重大的隐患|
|Queue：队列，它分为两类，一类是阻塞式队列，队列满了以后再插入元素则会抛出异常，主要包括：ArrayBlockingQueue、PriorityBlockingQueue、LinkedBlockingQueue，其中ArrayBlockingQueue是一个以数组方式实现的有界阻塞队列，PriorityBlockingQueue是按照优先级组建的队列，LinkedBlockingQueue是通过链表实现的阻塞队列，另一类是非阻塞队列，无边界的，只要内存允许，都可以持续追加元素，我们经常使用的是PriorityQueue类；还有一种队列，是双端队列，支持在头、尾两端插入和移除元素，它的主要实现是：ArrayDeque、LinkedBlockingDeque、LinkedList|
|数组；数组与集合的最大区别就是数组能够容纳基本类型，而集合就不行，更重要的一点就是所有的集合底层存储的都是数组|
|工具类；数组的工具类是java.util.Arrays和java.lang.reflect.Array，集合的工具类是java.util.Collections，有了这两个工具类，操作数组和集合会易如反掌，得心应手|
|扩展类；集合类当然可以自行扩展了，想写一个自己的List，没问题，但最好的办法还是拿来主义，可以使用Apache的commons-collections扩展包，也可以使用Google的google-collections扩展包，这些是以应对我们的开发需要|

#### 集合相等只需关心元素数据
```
public class Test {
    public static void main(String[] args) {
        ArrayList<String> strings = new ArrayList<>();
        strings.add("A");
        Vector<String> strings1 = new Vector<>();
        strings1.add("A");
        strings.equals(strings1);//返回true
    }
}
```
+ `只要所有的元素相等，并且长度也相等就表明两个List是相等的，与具体的容量类型无关；判断集合是不是相等时只需要关注元素是否相等即可`；
#### 子集合只是原集合的一个视图
```
public class Test {
    public static void main(String[] args) {
        List<String> c = new ArrayList<>();
        c.add("A");
        c.add("B");
        List<String> c1 = new ArrayList<>(c);
        List<String> c2 = c.subList(0, c.size());
        c2.add("C");
        c.equals(c1);//false
        c.equals(c2);//true
    }
}
```
+ `subList产生的容器只是一个视图，所有的修改动作直接作用于原容器`；
#### 使用shuffle打乱集合
```
public class Test {
    public static void main(String[] args) {
        int tagCloudNum = 10;
        List<String> tagClouds = new ArrayList<>(tagCloudNum);
        Collections.shuffle(tagClouds);//打乱顺序
    }
}
```
|shuffle使用场景|
|------|
|可以用在程序的伪装上；如标签云，或者游戏中的打怪、修行、群殴时宝物的分配策略|
|可以用在抽奖程序中；先使用shuffle把员工排序打乱，每个员工的中奖几率就是相等的|
|可以用在安全传输方面；如发送端发送一组数据，先随机打乱顺序，然后加密发送，接收端解密，然后自行排序，即可实现即使是相同的数据源，也会产生不同的密文的效果，加强了数据的安全性|

#### 不推荐使用binarySearch对集合进行检索
```
public static void main(String[] args) {
    List<String> cities = new ArrayList<>();
    cities.add("shanghai");
    cities.add("guangzhou");
    cities.add("guangzhou");
    cities.add("beijing");
    cities.add("tianjin");
    int index1 = cities.indexOf("guangzhou");//1
    int index2 = Collections.binarySearch(cities, "guangzhou");//2
}
```
+ `二分法查询的一个首要前提是：数据集已经实现升序排序，否则二分法查找的值是不准确的`；`使用binarySearch的二分法查找比indexOf的遍历算法性能高很多，特别是在大数据集而且目标值又接近尾部时，bianrySearch方法与indexOf方法相比，性能上会提升几十倍，因此在从性能的角度上考虑时可以选择binarySearch；从性能方面考虑，binarySearch是最好的选择`；
#### 集合中的哈希码不要重复
```
public static void main(String[] args) {
    int size = 10000;
    List<String> list = new ArrayList<>();
    for (int i = 0; i < size; i++) {
        list.add("value" + i);
    }
    long start = System.nanoTime();
    list.contains("value" + (size - 1));
    long end = System.nanoTime();
    long x = end - start;//输出7994722ns

    Map<String, String> map = new HashMap<>();
    for (int i = 0; i < size; i++) {
        map.put("key " + i, "value" + i);
    }
    start = System.nanoTime();
    map.containsKey("key" + (size - 1));
    end = System.nanoTime();
    long j = end - start;//输出8809ns
}
```
+ `HashMap的查找则是依赖hashCode定位的，因为是直接定位，那效率就相当高了；所以，HashMap中的hashCode应避免冲突`；
#### 推荐使用subList处理局部列表
+ `一个容器有100个元素，现在要删除索引位置为20~30的元素`；
```
public static void main(String[] args) {
    List<Integer> initData = Collections.nCopies(100, 0);//初始化一个固定长度，不可变列表
    List<Integer> list = new ArrayList<>(initData);//转换为可变列表
    for (int i = 0, size = list.size(); i < size; i++) {
        if (i >= 20 && i < 30) {
            list.remove(20);
        }
    }
    for (int i = 20; i < 30; i++) {
        if (i < list.size()) {
            list.remove(20);
        }
    }
    list.subList(20, 30).clear();
}
```
+ `subList方法的具体实现方式，所有的操作都是在原始容器上进行的，所以可以用subList先取出一个子容器，然后清空，因此subList返回的List是原始列表的一个视图，删除这个视图中的所有元素，最终就会反应到原始字符串上，那么一行代码即可解决问题`；
#### 生成子容器后不要再操作原容器
```
public static void main(String[] args) {
    List<String> list = new ArrayList<>();
    list.add("A");
    list.add("B");
    list.add("C");
    List<String> subList = list.subList(0, 2);
    list.add("D");
    list.size();//输出4
    subList.size();//运行时错误ConcurrentModificationException
}
```
+ `这是因为sunList取出的集合是原集合的一个视图，原集合修改了，但是subList取出的子容器不会重新生成一个新容器，后面在对子容器继续操作时，就会检测到修改计数器与预期不相同，于是就抛出了并发修改异常`；`为了解决上面的问题，最有效的方法就是通过Collections.unmodifiableList方法设置容器为只读状态`；
```
public static void main(String[] args) {
    List<String> list = new ArrayList<>();
    list.add("A");
    list.add("B");
    list.add("C");
    List<String> sub = list.subList(0, 2);
    list = Collections.unmodifiableList(list);//设置容器为只读
    //......对list进行只读操作，对sub进行读写操作
}
```
+ `subList生成子容器后，保持原容器是只读状态`；
#### 使用Compare进行排序
+ 在Java中，在进行排序的时候，`尽量不要自己写算法`，`而是采用拿来原则`，`将系统中的方法为我所用，要给数据排序，可以使用实现Comparable接口，实现Comparator接口`；
```
public class Test {
    public static void main(String[] args) {
        List<Employee> list = new ArrayList<>(5);
        list.add(new Employee(1001, "zhangsan", Position.BOSS));
        list.add(new Employee(1002, "lisi", Position.MANAGER));
        list.add(new Employee(1003, "wangwu", Position.MANAGER));
        Collections.sort(list);
    }
}

class Employee implements Comparable<Employee> {
    private int id;
    private String name;
    private Position position;

    public Employee(int id, String name ,Position position) {
        this.id = id;
        this.name = name;
        this.position = position;
    }
    @Override
    public int compareTo(@NonNull Employee o) {
        return this.id - o.id;
    }
}

enum Position {
    BOSS , MANAGER , STAFF
}
```
+ `Comparable接口可以作为实现类的默认排序法，Comparator接口则是一个类的扩展排序工具`；
#### 不同列表选择不同的遍历方法
```
public class Test {
    public static void main(String[] args) {
        int stunum = 80 * 10000;
        List<Integer> scores = new ArrayList<>(stunum);
        for (int i = 0; i < stunum; i++) {
            scores.add(new Random().nextInt(150));
        }
        average(scores);//时间是12ms
        average1(scores);//时间是9ms
        List<Integer> score = new LinkedList<>(scores);
        average2(score);//时间是3ms
    }

    public static int average(List<Integer> list) {
        int sum = 0;
        for (int i : list) {
            sum += i;
        }
        return sum / list.size();
    }

    public static int average1(List<Integer> list) {
        int sum = 0;
        for (int i = 0, size = list.size(); i < size; i++) {
            sum += list.get(i);
        }
        return sum / list.size();
    }

    public static int average2(List<Integer> list) {
        int sum = 0;
        for (int i : list) {
            sum += i;
        }
        return sum / list.size();
    }
}
```
#### 多线程使用Vector或HashTable
```
/**
 * 会报出ConcurrentModificationException
 */
public class Test {
    public static void main(String[] args) {
        final List<String> tickets = new ArrayList<>();
        for (int i = 0; i < 100000; i++) {
            tickets.add("火车票" + i);
        }
        final Thread t = new Thread(){
            @Override
            public void run() {
                while (true) {
                    tickets.add("车票" + new Random().nextInt());
                }
            }
        };
        Thread sale = new Thread(){
            @Override
            public void run() {
                for (String s : tickets) {
                    tickets.remove(s);
                }
            }
        };
        t.start();
        sale.start();
    }
}
```
+ `因为ArrayList是线程不安全的，两个线程访问同一个ArrayList集合肯定会有问题`；`在多线程环境下考虑使用Vector和HashTable`；
