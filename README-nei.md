### 内部类
+ 访问修饰符同样可以使用于内部类，可以将它想象成一个函数来处理；

|内部类主要有如下作用|
|------|
|内部类提供更好的封装，可以把内部类隐藏在外部类之内，不允许同一个包中的其他类访问该类；如有个Cow类该类需要一个CowLeg对象，但是CowLeg类只有在Cow类里才有效，所以可以将CowLeg放在Cow类的内部；不允许其他类访问CowLeg|
|内部类成员可以直接访问外部类的私有成员，但外部类不能访问内部类的实现细节如内部类的成员变量|
|匿名内部类适用于创建那些仅需要一次使用的类|
|内部类可以通过private、protected、static修饰，外部类不能使用这三种修饰符|
|非静态内部类不能拥有静态成员|

#### 非静态内部类

```
public class Test{
       private double weight;
       public Test(){}
       public Test(double weight){
              this.weight = weight;
       }
       private class TestLeg{
              private double lenght;
              private String color;
              public TestLeg(double lenght , String color){
                     this.lenght = lenght;
                     this.color = color;
              }
       }
       public void test(){
              TestLeg cl = new TestLeg(1.12 , "黑色");
       }
       public static void main(String[] args){
              Test test = new Test(12312.12);
               test.test();
       }
}
```

+ 非静态内部类不能拥有静态成员； 在非静态内部类对象里，保存了一个它所寄生的外部类对象的引用（当调用非静态内部类的实例方法时，必须有一个非静态内部类实例，非静态内部类实例必须寄生在外部类实例里）如果外部类成员变量、内部类成员变量与内部类里的方法的局部变量同名，可以通过this（内部类里的）、外部类类名.this来限定区分（外部类里的）；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-25.jpg) 

+ 非静态内部类的成员可以访问外部类的private成员，但是反过来不行，如果非要访问非静态内部类的成员，必须创建非静态内部类对象来访问其实例成员；

```
public class Test{
       private int weight;
       private class TestLeg{
              private int in = 5;
       }
       public static void main(String[] args){
              Test test = new Test();
              test.test();
       }
       public void test(){
              TestLeg t = new TestLeg();
              int a = t.in;
       }
}
```
+ 继承内部类形式：
```
class withinner{
       class Inner{}
}
public class Test extends withinner.Inner{
       test(withinner wi){
              wi.super();
       }
}
```

### 静态内部类
+ 只能访问外部类成员（static），不能访问外部类实例成员；
### 使用内部类
#### 在外部类内部使用内部类
+ 不要在外部类的静态成员（包括静态方法和静态代码块）中使用非静态内部类，因为静态成员不能访问非静态成员；
#### 在外部类以外使用非静态内部类
+ 在外部类以外的地方定义内部类（包括静态内部类和非静态内部类）变量格式为：`OutClass.InClass instance`；在外部类以外的地方创建非静态内部类实例的语法：`OutClassinstance.new InClass()`；
#### 在外部类以外使用静态内部类
+ 创建静态内部类实例的方法：`new OutClass.InClass()`；
### 局部内部类
+ 在方法内创建一个完整的类，被称为局部内部类；
### 匿名内部类
+ 匿名内部类适合创建那种只需要一次使用的类；需要在某处创建一个类 content，或者接口content，这个匿名内部类的含义就是返回实现这个接口子类的对象如果匿名内部类需要使用在其外部定义的元素，那么编译器会要求这个参数使final 类型的（该特性java8去除了）；

![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-26.jpg) 
