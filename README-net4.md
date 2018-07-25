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

### 使用MulticastSocket实现多点广播
+ DatagramSocket只允许数据报发送给指定的目标地址，而MulticastSocket可以将数据报以广播方式发送到多个客户端。若要使用多点广播，则需要让一个数据报标有一组目标主机地址，当数据报发出后，整个组的所有主机都能收到该数据报，IP多点广播实现了将单一信息发送到多个接收者的广播，其思想是设置一组特殊网络地址作为多点广播地址，每个多点广播地址都被看作一个组，当客户端需要发送、接收广播信息时，加入到该组即可；IP协议为多点广播提供了这批特殊的IP地址，这些IP地址的范围是224.0.0.0至239.255.255.255；

|MulticastSocket提供了如下三个构造器|说明|
|------|------|
|public MulticastSocket()|使用本机默认地址、随机端口来创建MulticastSocket对象|
|public MulticastSocket(int portNumber)|使用本机默认地址、指定端口来创建MulticastSocket对象|
|public MulticastSocket(SocketAddress bindaddr)|使用本机指定IP地址、指定端口来创建MulticastSocket对象|

+ 创建MulticastSocket对象后，还需要将该MulticastSocket加入到指定的多点广播地址，MulticastSocket使用joinGroup()方法加入指定组，使用leaveGroup()方法脱离一个组；

|方法|说明|
|------|------|
|joinGroup(InetAddress multicastAddr)|将该MulticastSocket加入指定的多点广播地址|
|leaveGroup(InetAddress multicastAddr)|将该MulticastSocket离开指定的多点广播地址|

+ 在某些系统中，可能有多个网络接口，这可能会给多点广播带来问题，这时候程序需要在一个指定的网络接口上监听，通过调用setInterface()方法可以强制MulticastSocket使用指定的网络接口，也可以使用getInterface()方法查询MulticastSocket监听的网络接口；如果创建仅用于发送数据报的MulticastSocket对象，则使用默认地址、随机端口即可，但如果创建接收用的MulticastSocket对象，则该MulticastSocket对象必须具有指定端口，否则发送方无法确定发送数据报的目标端口；MulticastSocket比DatagramSocket多了一个setTimeToLive(int tt1)方法，该tt1参数用于设置数据报最多可以跨过多少个网络，当tti为0时，指定数据报应停留在本地主机，当tt1为1，指定数据报发送到本地局域网，当tt1为32，意味着只能发送到本站点的网络上，当tt1为64，意味着数据报应该保留到本地区，当tt1为128，意味着数据报应保留在本大洲，当tt1为255，意味着数据报可以发送到所有地方，在默认情况下，tt1的值为1；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-182.jpg) pic-182.jpg
