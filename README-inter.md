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

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-51.jpg) 

### 使用Ｌambda表达式遍历集合

```
books.forEach(obj->System.out.println("迭代集合元素" + obj));
```

### 使用Java8增强的Iterator遍历集合元素
+ Iterator主要用于遍历Ｃollection集合中的元素，Iterator对象也被称为迭代器，Iterator定义的4个方法：

|方法|说明|
|------|------|
|boolean  hasNext()|如果被迭代的集合元素还没有遍历完，则返回true|
|Ｏbject next()|返回集合里的下一个元素|
|void remove()|删除集合里上一个next方法返回的集合|
|void forEachRemaining(Consumer consumer)|这是java8为Iterator新增的默认方法，该方法可使用Lambda表达式来遍历集合元素|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-52.jpg) 

+ Iterator本身并不提供盛装对象的能力，如果需要创建Iterator对象，则必须有一个被迭代的集合，没有集合的Iterator仿佛无本之木，没有存在的价值；Iterator必须依附于Collction对象，若有一个Iterator对象，则必然会有一个与之关联的Collction对象，Collction提供了两个方法来迭代访问Collction集合里的元素，并可通过remove()方法来删除集合中上一次next()方法返回的集合元素；当使用Iterator对集合元素进行迭代时，Iterator并不是把集合元素本身传给了迭代变量，而是把集合元素的值传给了迭代变量，所以修改迭代变量的值对集合元素本身没有任何影响；当使用Iterator迭代访问Collction集合元素时，Collction里的元素不能被修改，只能通过Iterator的remove()方法删除上一次的元素；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-53.jpg) 

### 使用Lambda表达式遍历Iterator

```
Iterator it = books.iterator();
it.forEachRemaining(obj->System.out.println("迭代集合元素：" + obj));
```

### 使用foreach循环遍历集合元素

```
for(Object obj : books){
       //操作obj
}
```

### 使用Java8新增的Predicate操作集合
+ java8中为Collection集合增加了一个`removeIf(Predicate filter)`方法，该方法会批量删除符合`filter`条件的所有元素；
### 使用Java8新增的Stream操作集合

|独立使用Stream的步骤|
|------|
|使用Stream或ＸxxStream的builder()类方法创建该Stream对应的Builder|
|重复使用Builder的add()方法向该流中添加多个元素|
|调用Builder的builder()方法获取对应的Stream|
|调用Stream的聚集方法|

```
IntStream is = IntStream.builder().add(20).add(12).build();
```

#### Stream常用的中间方法

|方法|说明|
|------|------|
|filter(Predicate predicate)|过滤Stream中不符合predicate的元素|
|mapToXxx(ToXxxFunction mapper)|使用ToXxxFunction对流中的元素执行一对一的转换，该方法返回的新流中包含了ToXxxFunction转换生成的所有元素|
|peek(Consumer consumer)|依次对每个元素执行一些操作，该方法返回的流与原有流包含相同的元素，该方法主要用于调试|
|distinct()|该方法用于排序刘中所有重复的元素（判断元素重复的标准是使用equals()比较返回true，这是一个有状态的方法）|
|sorted()|该方法用于保证流中的元素在后续的访问中处于有序状态，这是一个有状态的方法|
|limit(long maxSize)|该方法用于保证对该流的后续访问中最大允许访问的元素个数，这是一个有状态的、短路的方法|

#### Stream常用的末端方法

|方法|说明|
|------|------|
|forEach(Consumer consumer)|遍历流中所有元素，对每个元素执行consumer|
|toArray()|将流中所有元素转换为一个数组|
|reduce()|该方法有三个重载的版本，都用于通过某种操作来合并流中的元素|
|min()|返回流中所有元素的最小值|
|max()|返回流中所有元素的最大值|
|count()|返回流中所有元素的数量|
|anyMatch(Predicate predicate)|判断流中是否至少包含一个元素符合predicate条件|
|allMatch(Predicate predicate)|判断流中是否每个元素都符合predicate条件|
|noneMatch(Predicate predicate)|判断流中是否所有元素都不符合predicate条件|
|findFirst()|返回流中的第一个元素|
|findAny()|返回流中的任意一个元素|
