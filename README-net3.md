### 加入多线程
+ 在使用传统的BufferedReader的readLine()方法读取数据时，在该方法成功返回之前，线程被阻塞，程序无法继续执行，考虑到这个原因，服务器应该为每个socket单独启动一个线程，每个线程负责与一个客户端通信；客户端读取服务器数据的线程同样会被阻塞，所以系统应该单独启动一个线程，该线程专门负责读取服务器端数据；
#### 服务器端程序

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-160.jpg) pic-160.jpg

#### 客户端程序

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-161.jpg) pic-161.jpg

### 半关闭的Socket

|Socket提供了如下两个半关闭的方法，只关闭Socket的输入流或输出流，用以表示输出数据已经发送完成|说明|
|------|------|
|shutdownInput()|关闭该Socket的输入流，程序还可通过该socket的输出流输出数据|
|shutdownOutput()|关闭该Socket的输出流，程序还可以通过该socket的输入流读取数据|

+ 当调用以上两个方法关闭socket的输入流或输出流之后，该socket就进入了半关闭的状态，Socket可通过isInputShutdown()方法判断该socket是否处于半读状态，通过isOutputShutdown()方法判断该Socket是否处于半写状态；即使同一个Socket实例先后调用了shutdownInput、shutdownOutput方法，该Socket实例仍然没有关闭，只是该Socket既不能输出数据，也不能读取数据而已；当调用socket的shutdownOutput或shutdownInput方法关闭了输出流或输入流之后，该socket无法再次打开输出流或输入流，因此这种做法通常不适合保持持久通信状态的交互式应用，只适用于一站式的通信协议；如HTTP协议；

### 使用NIO实现非阻塞Socket通信
+ java的NIO为非阻塞式Socket通信提供了如下几个特殊类：

|类|说明|
|------|------|
|Selector|它是SelectableChannel对象的多路复用器，所有希望采用非阻塞方式进行通信的Channel都应该注册到Selector对象，可以通过调用此类的open()静态方法来创建Selector实例，，该方法将使用系统默认的Selector来返回新的Selector|

+ Selector可以同时监控多个SelectableChannel的IO状况，是非阻塞IO的核心，一个Selector实例有三个SelectionKey集合；

|集合|说明|
|------|------|
|所有的SelectionKey集合|代表了注册在该Selector上的Channel，这个集合可以通过keys()方法返回|
|被选择的SelectionKey集合|代表了所有可通过select()方法获取的、需要进行IO处理的Channel，这个集合可以通过selectedKeys()方法返回|
|被取消的SelectionKey集合|代表了所以被取消注册关系的Channel，在下一次执行select()方法时，这些Channel对应的SelectionKey会被彻底删除，程序通常无需直接访问该集合|

+ 除此之外，Selector还提供了一系列和select()相关的方法，如下所示：

|方法|说明|
|------|------|
|int sselect()|监控所有注册的Channel，当他们中间有需要处理的IO操作时，该方法返回，并将对于的SelectionKey加入被选择的SelectionKey集合中，该方法返回这些Channel的数量|
|int selectNow()|执行一个立即返回的select()操作，相对于无参数的select()方法而言，该方法不会阻塞线程|
|int select(long timeout)|可以设置超时时长的select()操作|
|Selector wakeup()|使一个还未返回的select()方法立即返回|
|SelectableChannel|他代表可以支持非阻塞IO操作的Channel对象，他可被注册到Selector上，这种注册关系由SelectionKey实例表示，Selector对象提供了一个Select()方法，该方法允许应用程序同时监控多个IO Channel|

+ 应用程序可调用SelectableChannel的register()方法将其注册到指定Selector上，当该Selector上的某些SelectableChannel上有需要处理的IO操作时，程序可以调用Selector实例的select()方法获取他们的数量，并可以通过selectedKeys()方法返回他们对应的SelectionKey集合，通过该集合就可以获取所需要进行IO处理的SelectableChannel集；SelectableChannel对象支持阻塞和非阻塞两种模式（所有的Channel默认都是阻塞模式），必须使用非阻塞模式才可以利用非阻塞IO操作，SelectableChannel提供了如下两个方法来设置和返回个该Channel的模式状态：

|方法|说明|
|------|------|
|SelectableChannel confihureBlocking(boolean block)|设置是否采用阻塞模式|
|boolean isBlocking()|返回该Channel是否是阻塞模式|

+ 不同的SelectableChannel所支持的操作不一样，例如ServerSocketChannel代表一个ServerSocket，他就只支持OP_ACCEPT操作，SelectableChannel提供了如下方法来返回它支持的所有操作：

|方法|说明|
|------|------|
|int validOps()|返回一个整数值，表示这个Channel所支持的IO操作|

+ 在SelectionKey中，用静态常量中定义了4种IO操作；OP_READ(1)、OP_WRITE(4)、OP_CONNECT(8)、OP_ACCEPT(16)这个值任意2个、3个、4个进行按位或的结果和相加的结果相同，而且他们任意2个、3个、4个相加的结果总是互不相同，所以系统可以根据validOps()方法的返回值确定该SelectableChannel支持的操作，例如但会5，就知道他支持（1）和（4）；

|SelectableChannel还提供了如下几个方法来获取他的注册状态|说明|
|------|------|
|boolean isRegistered()|返回该Channel是否注册在一个或多个Selector上|
|SelectionKey keyFor(Selector sel)|返回该Channel的sel Selector之间的注册关系，如果不存在注册关系则返回null|
|ServerSocketChannel|支持非阻塞操作，对应于java.net.ServerSocket这个类，只支持OP_ACCEPT操作，该类也提供了accept方法，功能相当于ServerSocket提供的accept方法|
|SocketChannel|支持非阻塞操作，对应于java.net.Socket类，支持OP_READ、OP_CONNECT、OP_WRITE操作，该类还实现了ByteChannel接口，ScatteringByteChannel接口和GatheringByteChannel接口，所以可以直接通过SocketChannel来读写ByteBuffer对象|

+ 当Selector上注册的所有Channel都没有需要处理的IO操作时，select()方法将被阻塞，调用该方法的线程被阻塞；创建一个可用的Server SocketChannel需要采用如下代码片段：

```
//通过open方法来打开一个未绑定的ServerSocketChannel实例
ServerSocketChannel server = ServerSocketChannel.open();
InetSocketAddress isa = new InetSocketAddress("127.0.0.1" , 30000);
//将该ServerSocketChannel绑定到指定的IP地址
server.bind(isa);
```

+ 如果需要使用非阻塞方式来处理该ServerSocketChannel，还应该设置它的非阻塞方式，并将其注册到指定的Selector：

```
//设置ServerSocket以非阻塞方式工作
server.configureBlocking(false);
//将server注册到指定的selector对象
server.register(selector,SelectionKey.OP_ACCEPT);
```

+ 经过上面的步骤后，该ServerSocketChannel可以接受客户端的连接请求，该需要调用Selector的select()方法来监听所有的Channel上的IO操作；
