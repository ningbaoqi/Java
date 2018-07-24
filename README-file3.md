### 输入/输出流体系
#### 处理流的用法
+ 使用处理流时的典型思路就是：使用处理流来包装节点流，程序通过处理流来执行输入/输出功能，让节点流与底层的IO设备，文件交互；

```
FileOutPutStream fos = new FileOutPutStream("sb.txt");//节点流
PrintStream ps = new PrintStream(fos);//处理流
ps.println("很好");
```

+ 在使用处理流包装了底层节点之后，关闭输入/输出流资源时，只要关闭最上层的处理流即可，关闭最上层的处理流时，系统会自动关闭被该处理流包装的节点流；

### 输入/输出体系
+ Java输入/输出流体系中常用的流分类；

|分类|字节输入流|字节输出流|字符输入流|字符输出流|
|------|------|------|------|------|
|抽象基类|InputStream|OutputStream|Reader|Writer|
|访问文件|FileInputStream节点流，必须直接与指定物理节点关联|FileOutputStream节点流，必须直接与指定物理节点关联|FileReader节点流，必须直接与指定物理节点关联|FileWriter节点流，必须直接与指定物理节点关联|
|访问数组|ByteArrayInputStream节点流，必须直接与指定物理节点关联|ByteArrayOutputStream节点流，必须直接与指定物理节点关联|CharArrayReader节点流，必须直接与指定物理节点关联|CharArrayWriter节点流，必须直接与指定物理节点关联|
|访问管道|PipedInputStream节点流，必须直接与指定物理节点关联|PipedOutputStream节点流，必须直接与指定物理节点关联|PipedReader节点流，必须直接与指定物理节点关联|PipedWriter节点流，必须直接与指定物理节点关联|
|访问字符串|||StringReader节点流，必须直接与指定物理节点关联|StringWriter节点流，必须直接与指定物理节点关联|
|缓冲流|BufferedInputStream|BufferedOutputStream|BufferedReader|BufferedWriter|
|转换流|||InputStreamReader|OutputStreamWriter|
|对象流|ObjectInputSream|ObjectOutputStream|||
|抽象基类|FilterInputStream|FilterOutputStream|FilterReader|FilterWriter|
|打印流||PrintStream||PrintWriter|
|推回输入流|PushbackInputStream||PushbackReader||
|特殊流|DataInputStream|DataOutputStream|||

+ 通常有一个规则：如果进行输入/输出的内容是文本内容，则应该考虑使用字符流，如果进行输入/输出的内容是二进制内容，则应该考虑使用字节流；

### 转换流
+ 转换流用于实现将字节流转换成字符流，其中InputStreamReader将字节输入流转换成字符输入流；OutputStreamWriter将字节输出流转换成字符输出流；
### 推回输入流

|推回输入流提供了三个方法|说明|
|------|------|
|void  unread(byte[]/char[] buf)|将一个字节/字符数组内容推回缓冲区里，从而允许重复读取刚刚读取的内容|
|void unread(byte[]/char[] buf,int off, int len)|将一个字节/字符数组里从off开始，长度为len字节/字符的内容推回到推回缓冲区里，从而允许重复读取刚刚读取的内容|
|void unread(int b)|将一个字节/字符推回到推回缓冲区里，从而允许重复读取刚刚读取的内容|

+ 这两个推回输入流都带有一个推回缓冲区，当程序调用这两个推回输入流的unread方法时，系统将会把指定数组的内容推回到该缓冲区中，而推回输入流每次调用read方法时总是先从推回缓冲区读取，只有完全读取了推回缓冲区中的内容后，但还没有装满read所需的数组时才会从原输入流中读取；
