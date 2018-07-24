### RandomAcessFile类
+ RandomAcessFile是java输入/输出流体系中功能最丰富的文件内容访问类，他提供了众多的方法来访问文件内容，他既可以读取文件内容，也可以向文件输出数据；RandomAcessFile支持随即访问的方式，程序可以直接跳转到文件的任意地方来读写数据；RandomAcessFile的方法虽然多，但它最大的一个局限就是：只能读写文件；不能读写其他IO节点；

|RandomAcessFile包含了如下两个方法来操作文件记录指针|说明|
|------|------|
|long getFilePointer()|返回文件记录指针的当前位置|
|void seek(long pos)|将文件记录指针定位到pos位置，pos指的是字节|

+ RandomAcessFile既可以读文件，也可以写文件，所以它即包含了完整类似于InputSteram的三个read方法，也包含了类似于OutputStream的三个write方法，RandomAcessFile还包含了一系列的readXxx()和writeXxx()方法来完成输入、输出；创建RandomAcessFile对象时需要指定一个mode参数，该参数指定RandomAcessFile的访问模式，该参数有如下4个值：

|参数值|说明|
|------|-----|
|`r`|以只读方式打开指定文件，如果试图对该RandomAcessFile执行写入方法，则将抛出IO异常|
|`rw`|以读写方式打开指定文件，如果该文件不存在，则尝试创建该文件|
|`rws`|以读写方式打开指定文件，相对于rw模式，还要求文件的内容或元数据的每个更新都同步写入到底层存储设备|
|`rwd`|以读写方式打开指定文件，相对于rw模式，还要求对文件内容的每次更新都同步写入到底层存储设备|

+ RandomAcessFile依然不能向文件的指定位置插入内容，如果直接将文件记录指针移动到中间某个位置后开始输出，则新输出的内容会覆盖文件中原有的内容，如果需要向指定位置插入内容，程序需要先将插入点后面的内容读入缓冲区，等把要插入的数据写入文件后，再将缓冲区的内容追加到文件后面；
