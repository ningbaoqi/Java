### 使用代理服务器
+ java在java.net包下提供了Proxy和ProxySelector类，其中Proxy代表一个代理服务器，可以在打开URLConnection连接时指定Proxy，创建Socket连接时也可以指定Proxy，而ProxySelector代表一个代理选择器，他提供了多代理服务器更加灵活的控制，它可以对HTTP、HTTPS、FTP、SOCKS等进行分别设置，而且还可以设置不需要通过代理服务器的地址和主机，通过使用ProxySelector，可以实现像在Internet Explorer、Firefox等软件中设置代理服务器类似的效果；
#### 直接使用Proxy创建连接
+ Proxy有一构造器Proxy(Proxy.Type type , SocketAddress sa)用于创建表示代理服务器的Proxy对象，其中sa参数指定代理服务器的地址，type表示该代理服务器的类型，该服务器类型有：

|类型|说明|
|------|-------|
|Proxy.Type.DIRECT|表示直接连接，不使用代理|
|Proxy.Type.HTTP|表示支持高级协议代理如：HTTP、FTP|
|Proxy.Type.SOCKS|表示SOCKS（V4或V5）代理|

+ 一旦创建了Proxy对象之后，程序就可以使用URLConnection打开连接时，或者创建Socket连接时传入一个Proxy对象，作为本次连接所使用的代理服务器；其中URL包含了URLConnection openConnection(Proxy proxy)方法，该方法使用指定的代理服务器来打开连接；而Socket则提供了一个Socket(Proxy proxy)构造器，该构造器使用指定的代理服务器来创建一个没有连接的Socket对象；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-190.jpg) pic-190.jpg

### 使用ProxySelector自动选择代理服务器
+ ProxySelector代表一个代理选择器，它本身是一个抽象类，程序无法创建它的实例，开发者可以考虑继承ProxySelector来实现自己的代理服务器，实现ProxySelector的步骤非常简单，程序只要定义一个继承ProxySelector的类，并让该类实现如下两个抽象方法：

|方法|说明|
|------|------|
|`List<Proxy> select(URI uri )`|根据业务需要返回代理服务器列表，如果该方法返回的集合中只包含一个Proxy，该Proxy将会作为默认的代理服务器|
|`connectFailed(URI uri , SocketAddress sa , IOException ioe)`|连接代理服务器失败时回调该方法|

+ 实现自己的ProxySelector类之后，调用ProxySelector的setDefault(ProxySelector ps)静态方法来注册该代理选择器即可；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-191.jpg) pic-191.jpg

+ 除此之外，java为ProxySelector提供了一个实现类，DefaultProxySelector，系统已经将DefaultProxySelector注册成默认的代理选择器，因此程序可调用ProxySelector.getDefault()方法来获得DefaultProxySelector实例；DefaultProxySelector也实现了两个抽象方法，他的实现策略是：

|方法|说明|
|------|-------|
|connectFailed()|如果连接失败，DefaultProxySelector将会尝试不使用代理服务器，直接连接远程资源|
|select()|DefaultProxySelector会根据系统属性来决定使用哪个代理服务器，ProxySelector会检测系统属性与URL之间的匹配，然后决定使用相应的属性值作为代理服务器，关于代理服务器常用的属性名有如下三个：http.proxyHost:设置HTTP访问所使用的代理服务器的主机地址，该属性名的前缀可以改为https、ftp等，分别用于设置HTTPS、FTP访问所用的代理服务器主机地址；http:proxyPort：设置HTTP访问所使用的代理服务器的端口，该属性名的前缀可以改为https、ftp等，分别用于设置HTTPS访问和FTP访问所用的代理服务器的端口；http.nonProxyHosts：设置HTTP访问中不需要使用代理服务器的主机，支持使用*通配符支持指定多个地址，多个地址之间用竖线分隔|
