### 操作集合的工具类：Collections
+ Collections该工具类里提供了大量方法对集合进行排序、查找修改等操作，还提供了将集合对象设置为不可变、对集合对象实现同步控制等方法；
#### 排序操作

|Collections提供了如下常用方法的类方法用于对list集合元素进行排序方法|说明|
|------|------|
|void reverse(List list)|反转指定的List集合中的元素的顺序|
|void shuffle(List list)|对List集合元素进行随机排序（该方法提供了一个模拟洗牌的动作）|
|void sort(List list)|根据元素的自然顺序对指定的List集合的元素按升序进行排序|
|void sort(List list ,Comparator c)|根据指定的Comparator产生的顺序对List集合元素进行排序|
|void swap(List list , int i , int j)|将指定的List集合中的第i处元素和第j处元素进行交换|
|void rotate(List list , int distance)|当distance为正数时，将list集合的后distance个元素整体移动到前面，当distance为负数时，将list集合的前distance个元素整体移到后面，该方法不会改变集合的长度|

