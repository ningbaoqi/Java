### 国际化和格式化
#### java支持的国家和语言
+ 如果需要获取java所支持的国家和语言，则可调用Locale类的getAvalibleLocales()方法，该方法返回一个Locale数组，该数组里包含了java所支持国家和语言；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-102.jpg) pic-102.jpg

#### 完成程序国际化
+ 为程序提供两个文件：第一个文件mess.properties文件，内容为key=value的形式；第二个文件：名为mess_en_US.properties文件内容也是键值对；使用native2ascii mess.properties mess_zh_CN.properties：将会生成mess_zh_CN.properties文件；然后使用如下代码：

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-103.jpg) pic-103.jpg

