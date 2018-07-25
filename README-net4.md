### 使用DategramSocket发送、接收数据

|DatagramSocket的构造器|说明|
|------|------|
|DatagramSocket()|创建一个DatagramSocket对象，并将该对象绑定到本机默认IP地址，本机所有可用的端口随机选择的某个端口|
|DatagramSocket(int port)|创建一个DatagramSocket对象，并将该对象绑定到本机默认IP地址，指定端口|
|DatagramSocket(int port,InetAddress laddr)|创建一个DatagramSocket对象，并将该对象绑定到指定IP地址、端口上|

+ 一旦得到了DatagramSocket实例之后，就可以通过如下两个方法来接收、发送数据：

|方法|说明|
|------|------|
|receive(DatagramPackat p)|从该DatagramSocket中接收数据报|
|send(DatagramPacket p)|以该DatagramSocket对象向外发送数据报|

+ DatagramPacket的构造器；

|方法|说明|
|------|------|
|DatagramPacket(byte[] buf , int length)|以一个空数组来创建DatagramPacket对象，该对象的作用是接收DatagramSocket中的数据|
|DatagramPacket(byte[] buf , int length , InetAddress addr , int port)|以一个包含数据的数组来创建DatagramPacket对象，创建该DatagramPacket对象时还指定了IP地址和端口，这就决定了该数据报的目的地|
|DatagramPacket(byte[] buf , int offset , int length)|以一个空的数组来创建DatagramPacket对象，并指定接收到的数据放入buf数组中时从offset开始，最多放length个字节|
|DatagramPacket(byte[] byte , int offset , int length , InetAddress address , int port)|创建一个用于发送的DatagramPacket对象，指定发送buf数组中从offset开始，总共length个字节|

+ receive方法将一直等待（该方法会阻塞调用该方法的线程），直到收到一个数据报为止；

```
//创建一个接收数据的DatagramPacket对象
DatagramPacket packet = new DatagramPacket(buf , 256);
//接收数据报
socket.receive(packet);
```

+ send方法根据数据报的目的地址来寻找以传送数据报；

```
//创建一个发送数据的DatagramPacket对象
DatagramPacket packet = new DatagramPacket(buf , length , address , port);
//发送数据报
socket.send(packet);
```

|可以调用DatagramPacket的如下三个方法来获得发送者的IP地址和端口|说明|
|------|------|
|InetAddress getAddress()|当程序准备发送此数据报时，该方法返回此数据报的目标机器的IP地址，当程序刚接收到一个数据报时，该方法返回该数据报的发送主机的IP地址|
|int getPort()|当程序准备发送此数据报时，该方法返回此数据报的目标机器的端口，当程序刚接收到一个数据报时，该方法返回该数据报的发送主机的端口|
|SocketAddress getSocketAddress()|当程序准备发送此数据报时，该方法返回此数据报的目标SocketAddress，当程序刚接收到一个数据报时，该方法返回该数据报的发送主机的SocketAddress|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-180.jpg) pic-180.jpg
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-181.jpg) pic-181.jpg

