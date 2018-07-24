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

