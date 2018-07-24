### 异常处理建议
#### 一、提倡异常封装
##### 优点一：提高系统的友好性
+ 如打开一个文件，如果文件不存在，则会报`FileNotFoundException`，如果该方法的编写者不做任何处理，直接抛到上层，则会降低系统的友好性；
```
public static void doStuff() throws Exception{
       InputStream is = new FileInputStream("无效文件.txt");
}
```
+  解决办法就是`封装异常`，可以把异常的阅读者分为两类：`开发人员`和`用户`，开发人员查找问题，需要打印堆栈信息，而用户则需要了解具体的业务原因，如文件太大；
```
public static void doStuff2() throws MyBussinessException{
       try{
              InputStream is = new FileInputStream("无效文件.txt");
       }catch(FileNotFoundException e){
              //为了方便开发人员和维护人员而设置的异常信息
              e.printStrackTrace();
              //抛出业务异常
              throw new MyBussinessException(e);
       }
}
```
##### 优点二：提高系统的可维护性
```
public void doStuff(){
       try{
              //do something
       }catch(Exception e){
              e.printStrackTrace();
       }
}
```
+ 以上捕捉所有类型的异常，这样不好应该细化，所以需要使用如下的方式：
```
public void doStuff(){
       try{
              //do something
       }catch(FileNotFoundException e){
              log.info("文件没有找到，使用默认的配置文件");
       }catch(SecurityException e){
              log.err("无法访问，可能原因是.....");
              e.printStachTrace();
       }
}
```
##### 优点三：解决java异常机制自身的缺陷
+ Java中的异常一次只能抛出一个，其实可以自行封装的异常来实现多异常抛出；
```
class MyException extends Exception{
    //容纳所有的异常
    private List<Throwable> causes = new ArrayList<Throwable>();
    //构造函数，传递一个异常对象
    public MyException(List<? extends Throwable> _causes){
        causes.addAll(_causes);
    }
    //读取所有异常
    public List<Throwable> getExceptions(){
        return causes;
    }
}
```
+ MyException异常只是一个异常容器，可以容纳多个异常，但他本身并不代表任何异常含义，他所解决的就是一次抛出多个异常的问题；
```
public static void doStuff() throws MyException{
    List<Throwable> list = new ArrayList<Throwable>();
    try{
        //do something
    }catch(Exception e){
        list.add(e);
    }
    try{
        //do something
    }catch(Exception e){
        list.add(e);
    }
    if(list.size() > 0){
        throw new MyException(list);
    }
}
```
+ 抛出多异常需要的场景，注册用户时需要将所有的用户信息填写完成，然后再进行校验，如果输入一个校验一个将会造成非常差的用户体验；

#### 二、采用异常链传递异常

+ 项目开发分为三层：持久层（数据库）->逻辑层（逻辑实现）->展现层（UI）；处理异常的正确方法就是先封装，然后传递，过程如下：

|传递异常过程|
|------|
|把FileNotFoundException封装为MyException|
|抛出到逻辑层，逻辑层根据异常代码（或者自定义的异常类型）确定后续处理逻辑，然后抛出到展现层|
|展现层自行解决要展现什么，如果是管理员则可以展现低层级的异常，如果是普通用户则展现封装后的异常|

+ 使用异常链进行传递；以IOException为例子：

```
public class IOException extends Exception{
       //定义异常原因
       public IOException(String message){
              super(message);
       }
       //定义异常原因，并携带原始异常
       public IOException(String message , Throwable cause){
              super(message , cause);
       }
       //保留原始异常信息
       public IOException(Throwable cause){
              super(cause);
       }
}
```

+ 在IOException的构造函数中，上一层的异常可以通过异常链进行传递，在实际项目中不要吞噬异常，也不要赤裸裸的抛出异常，封装后再抛出，或者通过异常链传递，可以达到系统更加健壮、友好；

#### 三、使用Throwable获得栈信息
```
public class Test{
    public static void main(String[] args){
        System.out.println(Foo.m());
        m1();
    }
    public static void m1(){
        System.out.println(Foo.m());
    }
}

class Foo{
    public static boolean m(){
        //获取当前栈信息
        StackTraceElement[] sts = new Throwable().getStackTrace();
        //检查是否是m1方法调用
        for(StackTranceElement  stackTranceElement :sts){
            if(stackTranceElement.getMethodName().equals("m1")){
                return true;
            }
        }
        return false;
    }
}
```
+ JVM在创建一个Throwable类及其子类时会把当前线程的栈信息记录下来，以便在输出异常时准确定位异常原因；
