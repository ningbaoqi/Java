### HashSet和HashMap的性能选项

|HashSet、HashMap的hash表包括如下属性|说明|
|------|------|
|容量capacity|hash表中桶的数量|
|初始化容量initial capacity|创建hash表时的桶的数量，以上两个类都允许在构造器中指定初始化容量|
|尺寸size|当前hash表中记录的数量|
|负载因子load factor|负载因子等于size/capacity，负载因子为0，表示空的kash表，0.5表示半满的hash表，以此类推，轻负载的hash表具有冲突少、适宜插入与查询的特点（但是使用迭代器迭代时候比较慢）|
