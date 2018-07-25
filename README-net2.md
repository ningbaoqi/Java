### Java的基本网络支持
#### 使用InetAddress类

+ Java提供了InetAddress类来代表IP地址，InetAddress下还有两个子类：Inet4Address和Inet6Address；

|InetAddress类没有构造器，而是提供了如下两个静态方法来获取InetAddress势实例|说明|
|------|-------|
|getByName(String host)|根据主机获取对应的InetAddress对象|
|getByAddress(byte[] addr)|根据原始IP地址来获取对应的InetAddress对象|
|getLocalHost()|获取本机IP地址所对应的InetAddress实例|

+ InetAddress还提供了如下三个方法来获取InetAddress实例对应的IP地址和主机名：

|方法|说明|
|------|------|
|String getCanonicalHostName()|获取此IP地址的全限定域名|
|String getHostAddress()|返回该InetAddress实例对应的IP地址字符串|
|String getHostName()|获取此IP地址的主机名|
|isReachable()|用于测试是否可以到达该地址|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-150.jpg) pic-150.jpg
