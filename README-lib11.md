### System类
#### 需要使用native来完成工作
+ `java`程序中声明`native`修饰的方法，只有方法签名，没有实现，编译该java程序生成class文件；用`javah`编译上一步生成的class文件，将产生一个`.h`文件；写一个cpp文件实现native方法，这一步需要包含.h文件（这个.h文件中有包含了JDK自带的jni.h文件）；将上一步的.cpp文件编译成动态链接库文件；在java中用system类的LoadLibrary()方法或者Runtime类的loadLibrary()方法加载这个动态链接库文件，java程序中就可以调用这个native方法了；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-140.jpg) pic-140.jpg
