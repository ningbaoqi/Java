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
