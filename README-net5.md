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

