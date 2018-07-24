### Collection和Iterator接口
+ Collection接口里定义的操作集合元素的方法：Iterator接口是Collection接口的父接口；

|Collection接口里定义的方法|说明|
|------|------|
|boolean add(Object o)|用于向集合里添加元素，如果集合对象被添加操作改变了，则返回true|
|boolean addAll(Collection c)|用于把集合c里的所有元素添加到指定的集合中，如果集合对象被添加操作改变了，则返回true|
|void clear()|清除集合里的所有元素，将集合长度变为0|
|boolean contains(Object o)|返回集合里是否包含指定元素|
|boolean containsAll(Collection o)|返回集合里是否包含集合c里的所有元素|
|boolean isEmpty()|返回集合是否为空，当集合长度为0时返回true，否则返回false|
|Iterator iterator()|返回Iterator对象，用于遍历集合里的元素|
|boolean remove(Object o) | 删除集合中的指定元素o，当集合里包含了一个或多个元素o时，该方法只删除第一个符合条件的元素，该方法将返回true|
|boolean removeAll(Collection c)|从集合中删除集合c里包含的所有元素（相当于把调用该方法的集合减去集合c）如果删除了一个或多个以上的元素，则该方法返回true|
|boolean retainAll(Collection c)|从集合中删除集合c中不包含的元素（相当于把调用该方法的集合变成该集合与集合c的交集）如果该操作改变了调用该方法的集合，则该方法返回true|
|int size()|返回集合中的元素个数|
|Ｏbject[] toArray()|把集合转换成一个数组，所有的集合元素变成相应的数组元素|

