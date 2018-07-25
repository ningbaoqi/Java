### Object类

|方法|说明|
|------|-------|
|boolean equals(Object obj)|判断指定对象与该对象是否相等，判断的标准是两个对象是同一个对象|
|protected void finalize（）|当系统没有引用变量引用该对象时，垃圾回收器调用此方法来清理该对象的资源|
|`Class<?>getClass（）`|返回该对象的运行时类|
|int hashCode()|返回该对象的hashCode值，默认该值是根据该对象的地址计算|
|String toString()|返回该对象的字符串表示，输出的是运行时类名@十六进制的hashcode值|
|wait()|控制线程的暂停和运行|
|notify()|控制线程的暂停和运行|
|notifyAll()|控制线程的暂停和运行|
|clone()|帮助其他对象来实现自我克隆，就是得到当前对象的副本|

### 自定义类实现克隆的步骤
+ 一、自定义类实现Cloneable接口，这是一个标记性的接口，实现该接口的对象可以实现自我克隆，接口中没有定义任何方法；二、自定义类实现自己clone方法；三、实现clone（）方法时通过super.clone()；调用Object实现的clone()方法来得到该对象的副本，并返回该副本；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-100.jpg)  pic-100.jpg
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-101.jpg)  pic-101.jpg
