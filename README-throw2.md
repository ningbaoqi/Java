### 使用throw抛出异常
#### 抛出异常
+ 如果需要在程序中自行抛出异常，则应使用throw语句，throw语句可以单独使用，throw语句抛出的不是异常类，而是一个异常实例，而且每次只能抛出一个异常实例；throw语句的语法格式为：
```
throw ExceptionInstance;
```
+ 如果throw语句抛出的异常是Checked异常，则该throw语句要么处于try块中，显式捕获该异常，要么放在一个带throws声明抛出的方法中，即把该异常交给该方法的调用者处理，如果throw语句抛出的异常是Runtime异常，则该语句无需放在try快中，也无需放在带throws声明抛出的方法中，程序既可以显式使用try...catch来捕获并处理该异常，也可以完全不理会该异常，把该异常交给方法调用者处理；
#### 自定义异常类
+ 用户自定义异常都应该继承Exception基类，如果希望自定义Runtime异常，则应该继承RuntimeException基类，定义异常类时通常需要提供两个构造器，一个是无参构造器，一个是带有一个字符串参数的构造器，这个字符串将作为该异常对象的描述信息（也就是异常对象的getMessage()方法的返回值）；
```
class MyOwnerException extends Exception{
       public MyOwnerException(){}
       public MyOwnerException(String str){
              super(str);
       }
}
```
#### catch和throw同时使用
+ 为了实现这种通过多个方法协作处理同一个异常的情况，可以在catch块中结合throw语句来完成；
```
try{
       //业务实现
}catch(Exception e){
       e.printStackTrace();
       throw new MyOwnerException("sb");
}
```
