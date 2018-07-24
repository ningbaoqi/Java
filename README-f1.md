### 通过反射查看类信息
#### 获取Class对象
+ `每个类被加载之后，系统就会为该类生成一个对应的Class对象，通过该Class对象就可以访问到JVM中的这个类`，在java程序中获得Class对象通常有如下三种方法：

|方法|说明|
|------|------|
|`使用Class类的forName(String clazzName)静态方法`|`该方法需要传入字符串参数，该字符串参数的值是某个类的全限定类名（必须添加完整包名）`|
|`调用某个类的class属性来获取该类对应的Class对象`|`Person.class将会返回Person类对应的Class对象`|
|`调用某个对象的getClass()方法`|`该方法是java.lang.Object类中的一个方法，所以所有的java对象都可以调用该方法，所以所有的对象都可以调用该方法，该方法将会返回该对象所属类的对应的Class对象`|

+ `对于第一种方式和第二种方式都是直接根据类来取得该类的Class对象，相比之下，第二种方式有两种优势`：

|优势|
|-------|
|`代码更安全，程序在编译阶段就可以检查需要访问的Class对象是否存在`|
|`程序性能更好，因为这种方式无需调用方法，所以性能更好`|

+ `就是说，大部分时候都应该使用第二种方式来获得指定类的Class对象`；`一旦获得了某个类所对应的Class对象之后，程序就可以调用Class对象的方法来获得该对象和该类的真实信息了`；

#### 从Class中获取信息
+ Class类提供了大量的实例方法来获取该Class对象所对应类的详细信息；
##### 获取Class对应类所包含的构造器

|方法|说明|
|------|------|
|`Connstructor<T> getConnstructor(Class<?>...parameterTypes)`|`返回此Class对象对应类的、带指定形参列表的public构造器`|
|`Connstructor<T>[] getConnstructors()`|`返回此Class对象对应类的全部public构造器`|
|`Connstructor<T> getDeclaredConnstructor(Class<?>........parameterTypes)`|`返回此Class对象对应类的、在指定形参列表的构造器，与构造器的访问权限无关`|
|`Connstructor<T>[] getDeclaredConnstructors()`|`返回此Class对象所对应类的所有构造器，与构造器的访问权限无关`|

##### 获取Class对应类的所包含的方法

|方法|说明|
|------|------|
|`Method getMethod(String name,Class<?>.... parameterTypes)`|`返回此Class对象对应类的、带指定形参列表的public 方法`|
|`Method[] getMethods()`|`返回此Class对象对应类的所有public 方法`|
|`Method getDeclaredMethod(String name,Class<?>.... parameterTypes)`|`返回此Class对象对应类的、带指定形参列表 方法，与方法的访问权限无关`|
|`Method getDeclaredMethods()`|`返回此Class对象对应类的全部方法，与方法的访问权限无关`|

##### 访问Class对应类所包含的成员变量

|方法|说明|
|-------|-------|
|`Field getField(String name)`|`返回此Class对象对应类的、指定名称的public成员变量`|
|`Field[] getFields()`|`返回此Class对象对应类的所有public成员变量`|
|`Field getDeclareField(String name)`|`返回此Class对象对应类的、指定名称的成员变量，与成员变量的访问权限无关`|
|`Field[] getDeclareFields()`|`返回此Class对象对应类的全部成员变量，与成员变量的访问权限无关`|

##### 访问Class对应类上所包含的Annotation

|方法|说明|
|------|------|
|`<A entends Annotation>A getAnnotation(Class<A> annotationClass)`|`尝试获取该Class对象对应类上存在的、指定类型的Annotation，如果该类型的注解不存在，则返回null`|
|`<A entends Annotation>A getDeclareAnnotation(Class<A> annotationClass)`|`这是java8新增的方法，该方法尝试获取直接修饰该Class对象对应类的、指定类型的Annotation，如果该类型的注解不存在，则返回null`|
|`Annotation[] getAnnotations()`|`返回修饰该Class对象对应类上存在的所有Annotation`|
|`Annotation[] getDeclareAnnotations()`|`返回直接修饰该Class对应类的所有Annotation`|
|`<A entends Annotation>A[] getAnnottationsByType(Class<A> annotationClass)`|`该方法的功能和前面介绍的getAnnotations方法基本类似，但由于java8增加了重复注解功能，因此需要使用该方法获取修饰该类的、指定类型的多个Annotation`|
|`<A entends Annotation>A[] getDeclareAnnottationsByType(Class<A> annotationClass)`|`该方法的功能和前面的getDeclareAnnotations方法基本类似，但由于java8增加了重复注解功能，因此需要使用该方法直接获取修饰该类的、指定类型的多个Annotation`|

##### 访问该Class对象对应类包含的内部类

|方法|说明|
|------|------|
|`Class<?> getDeclareClasses()`|`返回该Class对象对应类里包含的全部内部类`|

##### 访问该Class对象对应类所在的外部类

|方法|说明|
|------|------|
|`Class<?> getDeclareClass()`|`返回该Class对象对应类所在的外部类`|

##### 访问该Class对象对应类所实现的接口

|方法|说明|
|------|------|
|`Class<?> getInterfaces()`|`返回该Class对象对应类所实现的全部接口`|

##### 访问该Class对象对应类所继承的父类

|方法|说明|
|------|------|
|`Class<? super T> getSuperclass()`|`返回该Class对象对应类的超类的Class对象`|

##### 获取Class对象对应类的修饰符、所在包、类名等基本信息

|方法|说明|
|------|------|
|`int getModifiers()`|`返回此类或接口的所有修饰符，修饰符由public、protected、private、final、static、abstract等对应的常量组成，返回的整数应使用Modifier工具类的方法来解码，才可以获取真是的修饰符`|
|`Package getPackage()`|`获取此类的包`|
|`String getName()`|`以字符串形式返回此Class对象所表示的类的名称`|
|`String getSimpleName()`|`以字符串形式返回此Class对象所表示的类的简称`|

##### 判断方法来判断该类是否为接口、枚举、注解类型等

|方法|说明|
|------|------|
|`boolean isAnnotation()`|`返回此Class对象是否表示一个注解类型`|
|`boolean isAnnotationPresent(Class< ? extends Annotation>annotationClass)`|`判断此Class对象是否使用了Annotation修饰`|
|`boolean isAnonmousClass()`|`返回此Class对象是否是一个匿名类`|
|`boolean isArray()`|`返回此Class对象是否表示一个数组类`|
|`boolean isEnum()`|`返回此Class对象是否表示一个枚举`|
|`boolean isInterface()`|`返回此Class对象是否表示一个接口`|
|`boolean isInstance(Object obj)`|`判断obj是否是此Class对象的实例，该方法可以完全替代instanceof修饰符`|

+ 上面的getMethod、getConnstructor方法中需要传入多个类型为`Class<?>`的参数，用于获取指定的方法或指定的构造器，关于这个参数的作用，假设某个类内包含了如下三个info方法签名：需要使用如下方式获取该Class对象所指定类的相应方法：

|方法|
|------|
|`public void info()`|
|`public void info(String str)`|
|`public void info(String str,Integer num)`|

```
//前一个参数指定方法名，后面的个数可变的Class参数指定参数类型列表
clazz.getMethod("info" , String.class);
clazz.getMethod("info" , String.class , Integer.class);
```

+ 获取构造器时无需传入构造器名，因为同一个类的构造器名相同，所以要确定一个构造器只要指定形参列表即可；

##### 例子及结果
```
@Retention(value = RetentionPolicy.RUNTIME)
@interface Annos {
    Anno[] value();
}

@SuppressWarnings(value = "unchecked")
@Deprecated
@Anno
public class Caonima {
    private Caonima() {

    }

    public Caonima(String name) {
        System.out.print("执行有参数的构造器");
    }

    public void info() {
        System.out.print("执行无参数的info");
    }

    public void info(String s) {
        System.out.print("执行有参数的info");
    }

    class Inner {

    }

    public static void main(String[] args) throws NoSuchMethodException, ClassNotFoundException {
        Class<Caonima> clazz = Caonima.class;
        Constructor[] constructors = clazz.getDeclaredConstructors();
        System.out.print("caonima的全部构造函数");
        for (Constructor c : constructors) {
            System.out.print(c);
        }
        Constructor[] publicC = clazz.getConstructors();
        System.out.print("caonima的public构造器");
        for (Constructor constructor : publicC) {
            System.out.print(constructor);
        }
        Method[] methods = clazz.getMethods();
        System.out.print("全部public方法");
        for (Method method : methods) {
            System.out.print(method);
        }
        System.out.print("caonima带一个参数的info方法" + clazz.getMethod("info", String.class));
        Annotation[] annotations = clazz.getAnnotations();
        System.out.print("全部Anmotations");
        for (Annotation an : annotations) {
            System.out.print(an);
        }
        Class<?>[] inners = clazz.getDeclaredClasses();
        System.out.print("全部内部类");
        for (Class c : inners) {
            System.out.print(c);
        }
        Class inClass = Class.forName("Caonima$Inner");
        System.out.print("外部类" + inClass.getDeclaringClass());
        System.out.print("包" + clazz.getPackage());
        System.out.print("父类" + clazz.getSuperclass());
    }
}
```

#### Java8新增的方法参数反射
+ `新增了Executable抽象基类，该对象代表可执行的类成员，该类派生了Constructor、Method两个子类`；

|Executable提供了几个方法|说明|
|------|------|
|`isVarArgs()`|`用于判断该方法或构造器是否包含数量可变的形参`|
|`getModifiers()`|`获取该方法或构造器的修饰符`|
|`int getParameterCount()`|`获取该构造器或方法的形参个数`|
|`Parameter[] getParameters()`|`获取该构造器或方法的所有形参`|

+ `上面的Parameter类是java8新增的，每个Parameter对象代表方法或构造器的一个参数`，Parameter也提供了大量的方法：

|方法|说明|
|------|------|
|`getModifiers()`|`获取修饰该形参的修饰符`|
|`String getName()`|`获取形参名`|
|`Type getParameterizedType()`|`获取带泛型的形参类型`|
|`Class<?> getType()`|`获取形参类型`|
|`boolean isNamePresent()`|`该方法返回该类的class文件中是否包含了方法的形参名信息`|
|`boolean isVarArgs()`|`该方法用于判断该参数是否是个数可变的形参`|
