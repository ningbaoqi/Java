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
