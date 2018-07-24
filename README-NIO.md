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

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-66.jpg) 

+ Buffer的主要作用是装入数据，然后输出数据；程序通过put()方法想Buffer中放入一些数据，当Buffer装入数据结束后，调用Buffer的flip()方法，该方法将limit设置为position所在位置，并将position设为0，也就是说，Buffer调用flip方法之后，Buffer为输出数据做好了准备，当Buffer输出数据结束后，Buffer调用clear()方法，clear方法不是清空Buffer的数据，它仅仅是将position设置为0，将limit设置为capacity，这样为再次向capacity中装入数据做好了准备；

|Buffer还包括了一些常用的方法|说明|
|------|------|
|int capacity()|返回Buffer的capacity大小|
|boolean hasRemaining()|判断当前位置position和界限limit之间是否还有元素可供处理|
|int limit()|返回Buffer的界限limit位置|
|Buffer limit(int newLt)|重新设定界限limit的值，并返回一个具有新的limit的缓冲区对象|
|Buffer mark()|设置Buffer的mark位置，它只能在0到position之间做mark|
|int position()|返回Buffer中的position值|
|Buffer position(int  newPs)|设置Buffer的position，并返回position被修改后的Buffer对象|
|int remaining()|返回当前位置和界限之间的元素个数|
|Buffer reset()|将位置position转到mark所在位置|
|Buffer rewind()|将位置position设置成0，取消设置的mark|

+ Buffer的所有子类还提供了两个重要的方法，put()和get()方法，用于向Buffer中放入数据和从Buffer中提取数据，当使用put和set方法放入取出数据时，Buffer即支持对单个数据的访问，也支持对批量数据的访问（以数组作为参数）；当使用put和get方法来访问Buffer中的数据时，分为相对和绝对两种：一、相对：从Buffer的当前position处开始读取或写入数据，然后将position的值按处理元素的个数增加；二、绝对：直接根据索引向Buffer中读取和写入数据，使用绝对方式访问Buffer里的数据时，不会影响位置position的值；

### 使用Channel
+ Channel可以直接将指定文件的部分或全部映射成Buffer；程序中不能直接访问Channel中的数据，包括读取、写入都不行，Channel只能与Buffer进行交互，也就是说，如果想从Channel中取得数据，必须先用Buffer从Channel中取出一些数据，然后让程序从Buffer中取出这些数据，如果要将程序中的数据写入Channel，一样先让程序将数据放进Buffer中，程序再将Buffer里的数据写入Channel中；所有的Channel都不应该使用构造器来直接创建，而是通过传统的节点InputStream、OutputStream等的getChannel()方法来返回对应的Channel，不同的节点流获取的Channel不一样；Channel中最常见的三类方法是map()、read()、write()，其中map方法用于将Channel对应的部分或全部的数据映射成ByteBuffer，而read方法或write方法都有一系列重载形式，这些方法用于从Buffer中读取数据或向Buffer中写入数据；map方法的方法签名为：`MappedByteBuffer map(FileChannel.MapMode mode , long position,long size)`，第一个参数执行映射时的模式，分别为只读、读写等模式，第二个、第三个参数用于控制将Channel的哪些数据映射成ByteBuffer；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-67.jpg) 

### 字符集和CharSet
+ 把明文的字符序列转换成计算机理解的二进制序列成为编码，把二进制序列转换成普通人能看懂的明文字符串称为解码；java默认使用Unicode字符集，但很多惭怍系统不使用Unicode字符集，那么当从系统中读取数据到java文件中时，就可能出现乱码的问题；JDK1.4提供了Charset来处理字节序列和字符序列之间的转换，该类包含了用于创建编码器和解码器的方法，还提供了获取Charset所支持的字符集的方法，Charset类是不可变的；Charset类提供了一个availableCharset()静态方法来获取当前JDK所支持的所有字符集；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-68.jpg) 

|常用的字符集|说明|
|------|------|
|GBK|简体中文字符集|
|BIG5|繁体中文字符集|
|ISO-8859-1|ISO拉丁字母表No.1也叫做IOS-LATIN-1|
|UTF-8|8位UCS转换格式|
|UTF-16BE|16位UCS转换格式|
|UTF-16LE|16位UCS转换格式|
|UTF-16|16位UCS转换格式|

+ 一旦知道了字符集的别名之后，程序就可以调用Charset的forName()方法来创建对应的Charset对象，forName方法的参数就是相应字符集的别名；如：
```
CharSet cs = CharSet.forName("GBK");
```
+ 获取了Charset的对象之后就可以通过该对象的newDecoder()方法、newEncoder()方法分别返回CharsetDecoder和CharsetEncoder对象，代表该Charset的解码器和编码器，调用CharsetDecoder的decode()方法就可以将ByteBuffer字节序列转换成CharBuffer字符序列；调用CharsetEncoder的encode()方法就能将CharBuffer字符序列或String转换成ByteBuffer字节序列；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-69.jpg) 

|Charset类提供的其他三个方法|说明|
|------|------|
|CharBuffer decode(ByteBuffer bb)|将ByteBuffer中的字节序列转换成字符序列的便捷方法|
|ByteBuffer encode(CharBuffer cb)|将CharBuffer中的字符序列转换成字节序列的便捷方法|
|ByteBuffer encode(String cb)|将String中的字符序列转换成为字节序列的便捷方法|

### 文件锁
+ java提供了FileLock来支持文件锁定功能，在FileChannel中提供的lock()/tryLock()方法可以获得文件锁FileLock对象，从而锁定文件，lock()和tryLock()的区别在于：当lock()试图锁定某个文件时，如果无法得到文件锁，程序将一直阻塞；而tryLock()是尝试锁定文件，他将直接返回而不是阻塞，如果获得了文件锁，该方法则返回该文件锁，否则返回null；如果FileChannel只想锁定文件的部分内容，而不是锁定全部内容，则可以使用如下的lock()或tryLock()方法：

|方法|
|------|
|lock(long position , long size , boolean shared)：对文件从position开始，长度为size的内容加锁，该方法是阻塞式的|
|tryLock(long position , long size , boolean shared)：非阻塞式的加锁方法|

+ 当参数shared为true时，表明该锁是一个共享锁，他将允许多个进程来读取该文件，但阻止其他进程获得对该文件的排他锁；当shared为false时，表明该锁是一个排他锁，他将锁住对该文件的读写，程序可通过调用FileLock的isShared方法来判断它获得的锁是否是共享锁；直接使用lock()/tryLock()方法获取的文件锁是排他锁；处理完文件后通过FileLock的release()方法释放文件锁；

|关于文件锁需要指出几点|
|------|
|在某些平台上，文件锁仅仅是建议性的，并不是强制性的，这意味着即使一个程序不能获得文件锁，它也可以对文件进行读写|
|在某些平台上，不能同步的锁定一个文件并把它映射到内存中|
|文件锁是由java虚拟机持有的，如果两个java程序使用同一个虚拟机运行，则他们不能对同一个文件进行加锁|
|在某些平台上关闭FileChannel时，会释放java虚拟机在该文件上的所有锁，因此应该避免对同一个被锁定的文件打开多个FileChannel|
