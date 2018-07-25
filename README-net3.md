### 加入多线程
+ 在使用传统的BufferedReader的readLine()方法读取数据时，在该方法成功返回之前，线程被阻塞，程序无法继续执行，考虑到这个原因，服务器应该为每个socket单独启动一个线程，每个线程负责与一个客户端通信；客户端读取服务器数据的线程同样会被阻塞，所以系统应该单独启动一个线程，该线程专门负责读取服务器端数据；
#### 服务器端程序

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-160.jpg) pic-160.jpg
