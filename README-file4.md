### Java虚拟机读写其他进程的数据
+ 使用Runtime对象的exec()方法可以运行平台上的其他程序，该方法产生了一个Process对象，Process对象代表由java程序启动的子进程，Process类提供了如下三个方法，用于让程序和其子程序进行通信；

|方法|说明|
|------|------|
|InputStream getErrorStream()|获取子进程的错误流|
|InputStream getInputStream()|获取子进程的输入流|
|OutputStream getOutputStream()|获取子进程的输出流|
