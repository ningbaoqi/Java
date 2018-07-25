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

+ java8新增了一个URLPermission工具类，用于管理HttpURLConnection 的访问权限，如果在HttpURLConnection安装了安全管理器，通过该对象打开连接时就需要先获得权限；

|通常创建了一个和URL的连接，并发送请求、读取此URL引用的资源需要如下步骤|
|------|
|通过调用URL对象的openConnection()方法来创建URLConnection对象|
|设置URLConnection的参数和普通请求属性|
|如果只是发送get方法请求，则使用connect()建立和远程资源之间的实际连接即可；如果需要发送post方式的请求，则需要获取URLConnection实例对应的输出流来发送请求参数|
|远程资源变为可用，程序可以访问远程资源的头字段或通过输入流来读取远程资源的数据|

+ 在创建和远程资源的实际连接之前，程序可以通过如下方法来设置请求头字段：

|方法|说明|
|------|------|
|setAllowUserInteraction()|设置该URLConnection的allowUserInteraction请求头字段的值|
|setDoInput()|设置该URLConnection的doInput请求头字段的值|
|setDoOutput()|设置该URLConnection的doOutput请求头字段的值|
|setIfModifiedSince()|设置该URLConnection的ifModifiedSince请求头字段的值|
|setUseCaches()|设置该URLConnection的useCaches请求头字段的值|

+ 除此之外，还可以使用如下方法来设置或增加通用头字段：

|方法|说明|
|------|------|
|setRequestProperty(String key,String value)|设置该URLConnection的key请求头字段的值为value如：`con.setRequestProperty("Accept"，"*/*")`|
|addRequestProperty(String key,String vale)|为该URLConnection的key请求头字段增加value值，该方法不会覆盖原请求头字段的值，而是将新值追加到原请求头字段中|

+ 当远程资源可用之后，程序可以使用以下方法来访问头字段和内容：

|方法|说明|
|------|------|
|Object getContent()|获取该URLConnection的内容|
|String getHeaderField(String name)|获取指定响头字段的值|
|getInputStream()|返回该URLConnection对应的输入流，用于获取URLConnection响应的内容|
|getOutputStream()|返回该URLConnection对应的输出流，用于向URLConnection发送请求参数|

+ getHeaderField方法用于根据响应头字段来返回对应的值，而某些头字段由于经常需要方法，所以java提供了以下方法来访问特定响应头字段的值：

|方法|说明|
|------|------|
|getContentEncoding()|获取content-encoding响应头字段的值|
|getContentLength()|获取content-length响应头字段的值|
|getContentType()|获取content-type响应头字段的值|
|getDate()|获取date响应头字段的值|
|getExpiration()|获取expires响应头字段的值|
|getLastModified()|获取last-modified响应头字段的值|

+ 如果即要使用输入流读取URLConnection响应的内容，又要使用输出流发送请求参数，则一定要先使用输出流，再使用输入流：

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-153.jpg) pic-153.jpg
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-154.jpg) pic-154.jpg

+ 如果程序可以确定远程响应的是字符流，则可以使用字符流来读取，如果程序无法确定远程响应的是字符流，则使用字节流读取即可；
