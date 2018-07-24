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
