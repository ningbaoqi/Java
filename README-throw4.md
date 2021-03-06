### 异常类的继承体系
+ try块和catch块的花括号不能省略，try块中声明的变量是代码块的局部变量，他只有在try块中有效，在catch块中不能访问该变量；java常见的异常类的继承关系如下：
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-1.jpg) 

+ java把所有的非正常的情况分成了两种：异常和错误，他们都继承了Throwable父类；Error错误，一般是指与虚拟机相关的问题，如系统崩溃、虚拟机错误、动态链接库失败等，这些错误无法恢复或不可能捕获，将导致应用程序中断，通常应用程序无法处理这些错误，因此应用程序不应该试图使用catch块来捕获Error对象；

|Java运行时的异常处理逻辑可能有如下几种情况|
|------|
|定义的数组长度很小，但是往不存在的数组索引中操作数据，会有数组越界异常，java运行时将调用IndexOfBoundsException对应的catch块处理该异常|
|如果需要操作的参数类型是字母，但是输入的是数字，将发生数字格式异常，java运行时将调用NumberFormatException对应的catch块处理该异常|
|如果计算除法，除数为0，则发生除0异常；java运行时将调用ArithmeticException对应的catch块处理该异常|
|当调用一个null对象的成员的时候将出现空指针异常，NullPointerException异常|
|如果程序运行时出现其他异常，该异常对象总是Exception类或其子类的实例，java运行时将调用Exception对于的catch块处理该异常|

### java7提供的多异常捕获
+ 在java7之前，每个catch块中只能捕获一种类型的异常，但是从java7开始，一个catch块可以捕获多种类型的异常；使用一个catch块捕获多种类型的异常时需要注意如下两个地方：一、捕获多种类型的异常时，多种异常类型之间用竖线`|`隔开；二、捕获多种类型的异常时，异常变量有隐式的final修饰，因此程序不能对异常变量重新赋值；但是捕获一种类型的异常时，异常变量没有final修饰；
```
try{
       //业务实现
}catch(ArithmeticException | IndexOfBoundException | NumberFormatException e){
       //处理异常
}
```
### 访问异常信息

|有的异常对象都包含了如下几个常用的方法|说明|
|------|------|
|getMessage()|返回该异常的详细描述字符串|
|printStackTrace()|将该异常的跟踪栈信息输出到标准错误输出|
|printStackTrace(PrintStream s)|将该异常的跟踪栈信息输出到指定输出流|
|getStackTrace()|返回该异常的跟踪栈信息|

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-2.jpg) 

### 使用finally回收资源
+ 程序在try块里打开了一些物理资源（例如数据库连接、网络连接、和磁盘文件等），这些资源都必须显式回收；异常处理语法结构中只有try块是必须的，也就是或如果没有try块，则不能有后面的catch块和finally块，catch块和finally块都是可选的，但catch和finally块至少出现其中之一，也可以同时出现，finally必须位于所有的catch块之后；在异常处理代码中使用`System.exit(1)`语句来`退出虚拟机`，则`finally块将失去执行的机会`；`其余情况finally块都会得到执行`；`catch块`中抛出异常，将会查找处理该异常的代码块，然后执行`finally代码块`;如果finally之前的代码块中使用了`return`，`finally将在return之前执行`；`finally`虽然是在`return`语句之前调用的，但是`finally`不能给成员变量赋值来改变`return`的返回值；不要在`finally`代码块中使用`return`语句；
