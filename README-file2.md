### 字节流和字符流
#### InputStream和Reader
+ InputStream和Reader是所有输入流的抽象基类，本身并不能创建实例来执行输入，但他们将成为所有输入流的模版，所以他们的方法是所有输入流都可使用的方法；
#### 在InputStream里包含的方法

|方法|描述|
|------|------|
|int read()|从输入流中读取单个字节，返回所读取的字节数据（字节数据可直接转换为int类型）|
|int read(byte[] b)|从输入流中最多读取b.length个字节的数据，并将其存储在字节数组b中，返回实际读取的字节数|
|int read(byte[] b , int off , int len)|从输入流中最多读取len个字节的数据，并将其存储在数组b中，放在数组b中时，并不是从数组起点开始的，而是从off位置开始，返回实际读取的字节数|

#### 在Reader里包含的三个方法

|方法|说明|
|------|------|
|int read()|从输入流中读取单个字符，返回所读取的字符数据（字符数据可直接转换为int的类型）|
|int read(char[] cbuf)|从输入流中最多读取cbuf.length个字符的数据，并将其存储在字符数组cbuf中，返回实际读取的字符数|
|int read(char[] cbuf,int off, int len)|从输入流中最多读取len个字符的数据，并将其存储在字符数组cbuf中，放入数组cbuf中时，并不是从数组起点开始，而是从off位置开始，返回实际读取的字符数|

+ 直到read方法返回-1，即表明到了输入流的结束点；InputStream和Reader都是抽象类，本身不能创建实例，但他们分别有一个用于读取文件的输入流：FileInputStream和FileReader,他们都是节点流，会直接和指定文件关联；在读取字节流的时候，因为中文字符占2个字节，所以可能出现乱码的情况；java7改写了所有的IO资源类，他们都实现了`AutoCloseable`接口，因此都可通过自动关闭资源的try语句来关闭这些IO流；
```
try(FileReader fr = new FileReader("filereader.java")){//则不用在代码中关闭IO了，因为会自动关闭
       //代码块
}catch(Exception e){
       //代码块
}
```

|InputStream和Reader还支持如下几种方法来移动记录指针|说明|
|------|------|
|void mark(int readAheadLimit)|在记录指针当前位置记录一个标记mark|
|boolean markSupported()|判断此输入流是否支持mark()操作，即是否支持记录标记|
|void reset()|将此流的记录指针重新定位到上一次记录标记mark的位置|
|long skip(long n)|记录指针向前移动n个字节/字符|

### OutputStream和Writer

|两个流都提供了如下三个方法|说明|
|------|------|
|void write(int c)|将指定的字节/字符输出到输出流中，其中c既可以代表字节，也可以代表字符|
|void write(byte[]/char[] buf)|将字节数组/字符数组中的数据输出到指定输出流中|
|void write(byte[]/char[] buf , int off , int len)|将字节数组/字符数组中从off位置开始，长度为len的字节/字符输出到输出流中|

+ 因为字符流直接以字符作为操作单位，所以Writer可以用字符串来代替字符数组，即以String对象作为参数，Writer里还包含如下两个方法：

|方法|说明|
|------|------|
|void write(String str)|将str字符串里包含的字符输出到指定输出流中|
|void write(String str ,int off , int len)|将str字符串里从off位置开始，长度为len的字符输出到指定输出流中|
