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

### 使用URLDecoder和URLEncoder类
+ 编程过程中可能涉及普通祖字符串和特殊字符串的相关转换，就需要使用URLDecoder和URLEncoder类；
#### URLDecoder类包含方法：

|方法|说明|
|------|------|
|static decode(String s,String enc)|他可以将看上去是乱码的特殊字符串转换成普通字符串；解码|

#### URLEncoder类包含方法：

|方法|说明|
|------|------|
|static encode(String s,String enc)|可以将普通字符串转换成application/x-www-form-urlencode MIME字符串；编码|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-151.jpg) pic-151.jpg

+ 当采用不同的字符集时，每个中文字符对应的字节数并不完全相同，所以使用URLDecoder和URLEncoder类进行转换时也需要指定字符集；

### URL、URLConnection、URLPermission类
+ URL对象代表统一资源定位器，它是指向互联网资源的指针，在通常情况下,URL可以由协议名、主机、端口和资源组成：格式如下：

```
protocal://host:port/resourceName
http://www.baidu.com/index.php
```
+ JDK中提供了一个URI类，其实例代表一个统一资源标识符，java的URI不能用于定位任何资源，它唯一的作用就是解析；

|一旦获取了URL对象之后，就可以调用如下方法访问该URL对于的资源|说明|
|------|------|
|String getFile()|获取该URL的资源名|
|String getHost()|获取该URL的主机名|
|String getPath()|获取该URL的路径部分|
|int getPort()|获取该URL的端口号|
|String getProtocal()|获取该URL的协议名称|
|String getQuery()|获取该URL的查询字符串部分|
|URLConnection openConnection()|返回一个URLConnection对象，它代表了与URL所引用的远程对象的连接|
|InputStream openStream()|打开与此URL的连接，并返回一个用于读取该URL资源的InputStream|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-152.jpg) pic-152.jpg
