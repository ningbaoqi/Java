### NIO
#### Java新IO概述
+ 新IO采用的是内存映射文件的方式来处理输入/输出，新IO将文件或文件的一段区域映射到内存中，这样就可以像访问内存一样来访问文件了；

| java中与新IO相关的包|说明|
|------|------|
|java.nio包|主要包含各种与Buffer相关的类|
|java.nio.channels包|只要包含与Channel和Selector相关的类|
|java.nio.charset包|主要包含与字符集相关的类|
|java.nio.channels.spi包|主要包含与Channel相关的服务提供者编程接口|
|java.nio.charset.spi包|包含与字符集相关的服务提供者编程接口|

+ Channel通道和Buffer缓冲是新IO中的两个核心对象，Channel是对传统的输入/输出系统的模拟，在新IO系统中所有的数据都需要通过通道传输，Channel提供了一个map方法，通过该map方法可以直接将一块数据映射到内存中；Buffer可以被理解为一个容器，他的本质是一个数组，发送到Channel中的所有对象都必须首先放到Buffer中，而从Channel中读取的数据也必须先放在Buffer中；

### 使用Buffer
+ Buffer就像一个数组，他可以保存多个类型相同的数据，Buffer是一个抽象类；`static XxxBuffer allocate(int capacity)`：创建一个容量为capacity的XxxBuffer对象；

|在Buffer中有三个重要的概念|说明|
|------|------|
|容量capacity|缓冲区的容量表示该Buffer的最大数据容量，即最多可以存储多少数据，缓冲区的容量不能为负值，创建后不能改变|
|界限limit|第一个不应该被读出或写入的缓冲区位置索引，也就是说，位于limit后的数据既不可被读，也不可被写|
|位置position|用于指明下一个可以被读出或写入的缓冲区位置索引，当使用Buffer从Channel中读取数据时，position的值恰好等于已经读到了多少数据，当刚刚创建一个Buffer时，其position为0，如果从Channel中读取了2个数据到该Buffer中，则position为2，指向Buffer中第三个位置|

+ 除此之外，Buffer里还支持一个可选的标记mark；Buffer允许直接将position定位到该mark处：`0<=mark<=position<=limit<=capacity`；

