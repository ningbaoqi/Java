### Java8改进的接口
+ 接口里不能包含普通方法，接口里的所有方法都是抽象方法；java8对接口进行了改进。允许在接口中定义默认方法，默认方法可以提供方法实现；
#### java8中的接口定义
+ 接口定义的基本格式：
```
[修饰符] interface 接口名 extends 父接口1, 父接口2......{
       0个或多个常量定义......
       0个或多个抽象方法定义......
       0个或多个内部类、接口、枚举定义......
       0个或多个默认方法或类方法定义......
} 
```
+ 修饰符可以是public 或者省略，如果省略了public修饰符，则默认采用包访问限定控制，即只有相同的包结构下才能访问该接口；接口名和类名采用相同的命名规则；一个接口可以有多个直接父接口，但接口只能继承接口，不能继承类；接口里可以包含成员变量（只能是静态常量）、方法（只能是抽象方法、类方法、默认方法）、内部类（包括内部接口、枚举）定义；对于接口里定义的静态常量而言，他们是接口相关的，因此系统会自动为这些成员变量增加static 和final修饰符，也就是说，在接口中定义成员变量时，不管是否使用public static final修饰符，接口里的成员变量总是使用这三个修饰符来修饰，而且接口里没有构造器和初始化块，因此接口里定义的成员变量只能在定义时指定默认值；定义接口里的普通方法是不管是否使用public abstract修饰，接口里的普通方法总是使用public abstract修饰；接口里定义的内部类、内部接口、内部枚举默认都采用public static两个修饰符，不管定义时是否指定这两个修饰符，系统都会自动使用public static对他们进行修饰；java8允许在接口中定义默认方法，默认方法必须使用default修饰，该方法不能使用static修饰，无论程序是否指定，默认方法总是使用public修饰，如果开发者没有指定public，系统会自动为默认方法添加public修饰符，由于默认方法并没有static修饰，因此不能直接使用接口来调用默认方法，需要使用接口的实现类的实例来调用这些默认方法；java8允许在接口中定义类方法，类方法必须使用static修饰，该方法不能使用default修饰，无论程序是否指定，类方法总是使用public修饰，如果开发者没有指定public，系统会为其添加public修饰符；类方法可以直接使用接口来调用；一个java文件里最多只能有一个public接口，如果一个java原文件中定义了一个public 接口，则该原文件的文件名与该接口名相同；
#### 接口的继承
+ 接口完全支持多继承，即一个接口可以有多个直接父接口，子接口扩展某个父接口，将会获得父接口里定义的所有抽象方法和常量；
#### 使用接口
+ 接口不能用于创建实例，但接口可以用于声明引用类型变量，当使用接口来声明引用类型变量时，这个引用类型变量必须引用到其实现类的对象，除此之外，接口的主要用途就是被实现类实现：接口的用途：定义变量，也可用于强制类型转换；调用接口中定义的常量；被其他类实现；实现使用implements关键字，因为一个类可以实现多个接口，这也是java为单继承灵活性不足所作的补充；
```
[修饰符] class 类名 extends 父类 implements 接口1, 接口2......{
       类体部分......
}
```
+ 实现接口与实现父类类似，一样可以获得所实现接口里定义的常量（成员变量）、方法（包括抽象方法和默认方法）；一个类实现了一个或多个接口之后，这个类必须完全实现这个接口里所定义的全部抽象方法，否则这个类必须定义为抽象类；实现接口方法时，必须使用public修饰，因为接口里的方法都是public修饰的，而子类重写父类方法时访问限定符只能更大或相等；所以实现类实现接口里的方法时只能使用public；接口不能显式继承任何类，但所有接口类型的引用变量都可以直接赋给Object类型的引用变量；
#### 接口和抽象类
+ 接口和抽象类都不能被实例化，他们都位于继承树的顶端，用于被其他类实现和继承；接口和抽象类都可以包含抽象方法，实现接口或继承抽象类的普通子类都必须实现这些抽象方法；区别：接口里只能包含抽象方法、静态方法、默认方法，不能为普通方法提供方法实现，抽象类则完全可以包含普通方法；接口里只能定义静态常量，不能定义普通成员变量；抽象类可以；接口不包含构造器，抽象类包含；接口不能包含初始化块，抽象类可以；
#### 面向接口编程
#### 简单工厂模式
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-27.jpg) 
#### 命令模式
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-28.jpg) 