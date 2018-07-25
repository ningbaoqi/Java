### 国际化和格式化
#### java支持的国家和语言
+ 如果需要获取java所支持的国家和语言，则可调用Locale类的getAvalibleLocales()方法，该方法返回一个Locale数组，该数组里包含了java所支持国家和语言；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-102.jpg) pic-102.jpg

#### 完成程序国际化
+ 为程序提供两个文件：第一个文件mess.properties文件，内容为key=value的形式；第二个文件：名为mess_en_US.properties文件内容也是键值对；使用native2ascii mess.properties mess_zh_CN.properties：将会生成mess_zh_CN.properties文件；然后使用如下代码：

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-103.jpg) pic-103.jpg

#### 使用MessageFormat处理包含占位符的字符串

```
如果想要输出“你好，yeeku！，今天是2014-5-30下午11：35”
因为是动态的所以在资源文件中需要使用占位符来保存msg=你好，{0}!今天是{1}
```
+ 程序需要为{0}{1}两个占位符赋值，此时需要使用MessageFormat类，该类包含以下方法：

|方法|描述|
|------|------|
|format(String pattern , Object.... values)|返回后面的多个参数值填充前面的pattern字符串，其中pattern字符串不是正则表达式，而是一个带有占位符的字符串|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-104.jpg) pic-104.jpg

#### 使用类文件替代资源文件

|使用类文件来替代资源文件必须满足一下情况|
|------|
|该类的类名必须是baseName_language_country,这与属性文件命名相同|
|该类必须继承ListResourceBundle，并重写getContens()方法，该方法返回Object数组，该数组的每一项都是键值对|

#### 使用NumberFormat格式化数字
+ NumberFormat和DateFormat都包括了format()和parse()方法，其中format方法用于将数值、日期格式化成字符串，parse用于将字符串解析成数值、日期；NumberFormat是一个抽象类，他提供了如下几个类方法来得到NumberFormat对象；

|方法|说明|
|------|------|
|getCurrentInstance()|返回默认Local的货币格式器，也可以在调用该方法时传入指定的Locale，则获取指定Locale的货币格式器|
|getIntegerInstance()|返回默认Locale的整数格式器，也可以在调用该方法时传入指定的Locale，则获取指定Locale的整数格式器|
|getNumberInstance()|返回默认Locale的通用数值格式器，也可以在调用该方法时传入指定的Locale，则获取指定Locale的通用数值格式器|
|getPacentInstance()|返回默认Locale的百分数格式器，也可以在调用该方法时传入指定的Locale，则获取指定Locale的百分数格式器|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-105.jpg) pic-105.jpg

#### 使用DateFormat格式化日期、时间

+ DateFormat也是一个抽象类，获取对象的方法为：

|方法|说明|
|------|-----|
|getDateInstance()|返回一个日期格式器，他格式化后的字符串只有日期、没有时间；该方法可以传入多个参数，用于指定日期样式和Locale等参数，如果不指定这些参数，则使用默认参数|
|getTimeInstance()|返回一个时间格式器，他格式化后的字符串只有时间，没有日期，该方法可以传入多个参数，用于指定时间样式和Locale等参数，如果不指定这些参数，则使用默认参数|
|getDateTimeInstance()|返回一个日期、时间格式器，他格式化后的字符串既有时间也有日期，该方法可以传入多个参数，用于指定时间、日期样式和Locale等参数，如果不指定这些参数，则使用默认参数|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-106.jpg) pic-106.jpg

#### 使用SimpleDateFormat格式化日期
+ SimpleDateFormat是DateFormat的子类，创建他的对象时需要传入一个pattern字符串，这个pattern不是正则表达式，而是一个日期模版字符串；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-107.jpg) pic-107.jpg
