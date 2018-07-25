### Calendar
+ Calendar是也一抽象类，不能被实例化；

|方法|说明|
|------|------|
|Calendar.getInstance()|创建一个默认的Calendar对象|
|void add (int field,int amount)|根据日历的规则，为给定日历字段添加或减去指定的时间量；当修改的字段超出他的范围会发生进位|
|int get(int field)|返回指定日历字段的值|
|int getActualMaximum(int field)|返回指定日历字段可能拥有的最大值如：月最大值为11|
|int getActualMinimum(int field)|返回指定日历字段可能拥有的最小值如：月最小值为0|
|void roll(int field , int amount)|与add类似，区别在于加上amount后超出了该字段所能表示的最大范围时，也不会向上一个字段进位|
|void set(int field , int value)|将给定的日历字段设置成给定值|
|void set(int year, int month，int date)|设置Calendar对象的年、月、日三个字段|
|void set(int year,int month,int date,int hourofday,int minute,int second)|设置日历对象的年、月、日、时、分、秒的值|
|setLenient（boolean boo）|设置容错性|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-110.jpg) pic-110.jpg

+ 使用set方法将月设置成了15，在打开容错性的时候不会报错，会进位，但是关闭容错性就会报错：关于容错性的方法为：`setLenient(boolean boo)`；
