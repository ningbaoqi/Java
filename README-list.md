### List集合
+ List集合代表一个元素有序、可重复的集合，集合中的每个元素都有其对应的顺序索引，List集合允许使用重复元素，可以通过索引来访问指定位置的集合元素，List集合默认按元素的添加顺序设置元素索引；索引从0开始；
#### Java8改进的List接口和ListIterator接口
+ List集合里增加了一些根据索引来操作集合元素的方法；

|方法|说明|
|------|------|
|void add(int index, Object element)|将元素element插入到list集合的index处|
|boolean addAll(int index , Collection c)|将集合c所包含的所有元素都插入到list集合的index处|
|Object get(int index)|返回集合index索引处的元素|
|int indexOf(Object o)|返回对象o在list集合中第一次出现的位置索引|
|int lastIndexOf(Object o)|返回对象o在list集合中最后一次出现的位置索引|
|Object  remove(int index)|删除并返回index索引处的元素|
|Object set(int index , Object element)|将index索引处的元素替换成element对象，返回被替换的旧对象，只会替换，不会增加集合的长度|
|List subList(int fromIndex , int toIndex)|返回从索引fromIndex（包含）到toIndex（不包含）处所有集合元素组成的子集合|

+ java8还为List接口添加了如下两个默认方法；

|方法|说明|
|------|------|
|void replaceAll(UnaryOperator operator)|根据operator指定的计算规则重新设置list集合的所有元素|
|void sort(Comparator c)|根据Comparator参数对list集合的元素排序|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-31.jpg) 

+ List判断两个对象相等的标准只要通过equals方法返回true即可；List还额外的提供了一个ListIterator()方法，该方法返回一个ListIterator对象，ListIterator接口继承了Iterator接口，提供了专门操作List的方法，ListIterator接口在Iterator接口的基础上添加了如下方法：

|方法|说明|
|------|-------|
|boolean hasPrevious()|返回该迭代器关联的集合是否还有上一个元素|
|Object previous()|返回该迭代器的上一个元素|
|void add(Object o)|在指定位置插入一个元素|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-32.jpg) 

### ArrayList和Vector实现类
+ ArrayList和Vector作为List类的两个典型实现，Vector和ArrayList都是基于数组实现的List类，ArrayList或Vector对象使用initialCapacity参数来设置该数组的长度，当向ArrayList或Vector中添加元素超过了该数组的长度时，他们的initialCapacity会自动增加；如果向ArrayList或Vector集合中添加大量的元素时，可以使用ensureCapacity(int minCapacity)方法一次性增加initialCapacity，这可以减少重分配的次数，从而提高性能；如果创建空的ArrayList或Vector集合时不指定initialCapacity参数，则Object[] 数组的长度默认为10；ArrayList和Vector还提供了如下两个方法来重新分配Object[] 数组：

|方法|说明|
|------|------|
|void ensureCapacity(int minCapacity)|将ArrayList或Vector集合的Object[]数组长度增加大于或等于minCapacity|
|void trimToSize()|调整ArrayList或Vector集合的Object[]数组长度为当前元素的个数，调用该方法可减少ArrayList或Vector集合对象占用的存储空间|

+ Vector具有很多缺陷，尽量少用Vector实现类；ArrayList和Vector的显著区别是：ArrayList是线程不安全的，当多个线程访问同一个ArrayList集合时，如果超过了一个线程修改了ArrayList集合，则程序必须手动的保证该集合的同步性；但Vector集合是线程安全的，无需程序保证该集合的同步性；但是也不要用它；Collections工具类，可以将一个ArrayList变成线程安全的；Vector还提供了一个Stack子类；在该子类里提供了几个方法：

|方法|说明|
|------|------|
|Object peek()|返回栈的第一个元素，但并不将该元素pop出栈|
|Object pop()|返回栈的第一个元素，并将该元素pop出栈|
|void push(Object item)|将一个元素push进栈，最后一个进栈的元素总是位于栈顶|

+ 应该尽量减少使用Stack类，如果程序需要使用栈这种数据结构，则可以考虑使用后面的ArrayDeque；

### 固定长度的List

+ Arrays工具类提供了一个asList(Object...a)方法，该方法可以把一个数组或指定个数的对象转换成一个List集合，这个List集合即不是ArrayList实现类的实例，也不是Vector实现类的实例，而是Arrays的内部类ArrayList的实例；Arrays.ArrayList是一个固定长度的List集合，程序只能遍历访问该集合里的元素，不可增加、删除该集合里面的元素；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-33.jpg) 
