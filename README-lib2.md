### String类
+ 不可变类，一旦一个String 对象被创建以后，包含在这个对象中的字符序列是不可以改变的，直至这个对象被销毁；Java会使用常量池来管理曾经用过的字符串常量，如：`String a = "java"`，常量池就会缓存一个字符串“Java”，如果再次执行`String b = “Java”`，系统就会让b直接指向常量池中的“Java”字符串，因此a==b返回true，系统不会回收这个字符串直接量；

|方法|说明|
|------|------|
|String()|创建一个包含0个字符串序列的String对象|
|String(byte[] bytes , Charset charset)|使用指定的字符集将指定的byte[]数组解码成一个新的String对象|
|String(byte[] byte , int offset , int length)|使用平台的默认字符集将指定的byte[]数组从offset 开始长度为length的子数组解码成一个新的String对象|
|String(byte[] bytes , int offset , int length  , String charseName)|使用指定的字符集将指定的byte[]数组从offset开始长度为length的子数组解码成一个新的String对象|
|String(byte[] bytes , String charsetName)|使用指定的字符集将指定的byte[]数组解码成一个新的String对象|
|String(char[] value , int offset , int count)|将指定的字符数组从offset开始长度为count的字符元素连接成字符串|
|String(String original )|根据字符串直接量来创建一个String对象，新创建的String对象是该参数字符串的副本|
|String(StringBuffer buffer)|根据StringBuffer对象来创建对应的String对象|
|String(StringBuider builder)|根据StringBuilder对象来创建对应的String对象|
|char charAt(int index)|获取字符串中指定位置的字符|
|int compareTo(String anotherString)|比较两个字符串的大小，如果两个字符串的字符序列相等返回0|
|String concat(String str)|将该String对象和str连接在一起，与+相同|
|boolean contentEquals（StringBuffer sb）|将该String对象与StringBuffer对象sb进行比较，当序列相同的时候返回true|
|static String copyValueOf（char[] data)|将字符数组连接成字符串|
|static String copyValueOf(char[] data , int offset , int count )|将char数组的子数组连接成字符串|
|boolean endsWith(String suffix)|返回该String对象是否以suffix结尾|
|boolean equals(Object anoObject)|比较两个String对象的字符串序列|
|boolean equalsIgnoreCase(String str)|与下个方法相同指示忽略大小写|
|byte[] getBytes()|将String对象转换成byte数组|
|void getChars(int srcBegin , int srcEnd , char[] dst , int dstBegin)|将字符串中从srcBegin开始到srcEnd结束的字符赋值到dst字符数组中|
|int indexOf(int ch)|找出ch字符在该字符串中第一次出现的位置|
|int indexOf(int ch，int fromIndex) |找出ch字符在该字符串中从fromIndex开始后第一次出现的位置|
|int indexOf(String str)|找出str子字符串在该字符串中第一次出现的位置|
|int indexOf(String str，int fromIndex)|找出str子字符串在该字符串中从fromindex开始第一次出现的位置|
|int lastIndexOf(int ch)|找出ch字符在该字符串中最后一次出现的位置|
|int lastIndexOf(int ch , int fromIndex)|找出ch字符在该字符串中从fromIndex开始后最后一次出现的位置|
|int lastIndexOf(String str)|找出str子字符串在该字符串中最后一次出现的位置|
|int lastIndexOf(String str，int fromIndex)|找出str子字符串从fromIndex开始最后一次出现的位置|
|int length()|返回当前字符串的长度|
|String replace(char oldChar , char newChar)|将字符串中的第一个oldChar替换成newChar|
|boolean startsWith(String str)|该String对象是不是以str开头|
|boolean startsWith(String str , int offset)|该String对象从offset位置算起是不是以str开始|
|String subString(int beginIndex)|获取从beginIndex位置开始到结束的子字符串|
|String subString(int beginIndex，int endIndex)|获取从beginIndex到endIndex位置的子字符串|
|char[] toCharArray()|将该String对象转化成为char数组|
|char[] toLowerCase()|将字符串转换成小写|
|char[] toUpperCase()|将字符串转换成大写|
|static String valueOf(X x)|一系列用于将基本数据类型值转换成String对象的方法|
|String trim()|返回字符串并且去掉空格|

#### 推荐使用String直接量赋值
+ 使用直接量赋值如：`String str = "a";`即是通过直接量a进行赋值的，对于String对象来说，这种方式是极力推荐的，但不建议使用`new String("a")；`方式赋值；

```
public class Common {
    public static void main(String[] str){
        String str1 = "china";
        String str2 = "china";
        String str3 = new String("china");
        String str4 = str3.intern();
        boolean b1 = (str1 == str2);//两个直接量相等
        boolean b2 = (str1 == str3);//直接量和对象不相等
        boolean b3 = (str1 == str4);//经过intern处理后的对象与直接量相等
    }
}
```

+ 注意：使用`==`判断的是两个对象的引用地址是否相同，即判断是否是同一个对象，可以看出，直接量是同一个对象，经过intern处理后的String与直接量是同一个对象，因为Java为了避免在一个系统中大量产生String对象，于是就设计了字符串池（也叫做`字符串常量池`，`String Pool`或`String Constant Pool`或`String Literal Pool`），在字符串池中所容纳的都是String字符串对象，它的创建机制是这样的，创建一个字符串时，首先检查池中是否有字面值相等的字符串，如果有，则不再创建，直接返回池中该对象的引用，若没有则创建它，然后放到池中，并返回新建对象的引用；`直接使用new声明的String对象是不会检查字符串池的`，也不会把对象放到池中；`intern会检查当前的对象在对象池中是否有字面值相同的引用对象，如果有则返回池中对象，如果没有则放置到对象池中，并返回当前对象`；String类是一个不可变类：String类是final类，不可继承，不可能产生一个String的子类，String类提供的所有方法中，如果有String返回值，就会新建一个String对象，不对原对象进行修改，这也保证原对象是不可改变的；字符串池非常特殊，它在编译期已经决定了其存在JVM的常量池，`垃圾回收机制是不会对它进行回收的`；String类是不可改变的量，也就是创建后就不能再修改了；`StringBuffer是一个可变字符序列`，它与String一样，在内存中保存的都是一个有序的字符序列（char类型的数组），不同点是`StringBuffer对象的值是可以改变的`；StringBuilder与StringBuffer基本相同，都是可变字符序列，不同点是：`StringBuffer是线程安全的`，`StringBuilder是线程不安全的`，这也是StringBuffer在性能上远低于StringBuilder的原因；在性能方面，由于String类的操作都是产生新的String对象，而StringBuilder和StringBuffer只是一个字符数组的再扩展而已，所以String类的操作要远慢于StringBuilder和StringBuffer；

|不同场景下使用不同的字符序列|
|------|
|使用String类的场景：在字符串不经常变化的场景中可以使用String类，例如常量的声明、少量的变量运算等|
|使用StringBuffer类的场景：在频繁进行字符串的运算（如拼接、替换、删除等），而且运行在多线程的环境中，则可以考虑使用StringBuffer，例如XML解析、HTTP参数解析和封装等|
|使用StringBuilder类的场景：在频繁进行字符串的运算（如拼接、替换、删除等），并且运行在单线程的环境中，则可以考虑使用StringBuilder，如SQL语句的拼装、JSON封装等|

+ 在适当的场景选用字符串类型；
