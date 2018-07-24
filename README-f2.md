### 使用反射生成并操作对象
#### 创建对象

|通过反射来生成对象的方式|
|------|
|`使用Class对象的newInstance()方法来创建该Class对象对应类的实例，这种方式要求Class对象的对应类有默认构造器`，`而执行newInstance()方法时实际上是利用默认构造器来创建该类的实例`|
|`先使用Class对象获取指定的Constructor对象，再调用Constructor对象的newInstance方法来创建该Class对象对应类的实例，通过这种方式可以选择使用执行的构造器来创建实例`|

```
Class<?> clazz = Class.forName("Person");
return clazz.newInstance();
```

+ `如果不想利用默认构造器来创建java对象，而想利用指定的构造器来创建java对象，则需要利用Construcor对象，每个Construcor对应一个构造器，为了利用指定的构造器来创建java对象`，需要如下三个步骤：

|步骤|
|------|
|`获取该类的Class对象`|
|`利用Class对象的getConstrucor()方法来获取指定的构造器`|
|`调用Construcor的newInstance()方法来创建java对象`|

```
Class<?> clazz = Class.forName("Person");
Constructor con = clazz.getConstructor(String.class);
Object obj = con.newInstance("hello");
```

+ `只有当程序需要动态创建某个类的对象时才会考虑使用反射，通常在开发通用性比较广的框架，基础平台时可能会大量使用反射`；
#### 调用方法
+ `当获得某个类对应的Class对象后，就可以通过该Class对象的getMethods()、getMethod()方法来获取全部或指定的方法，这两个方法的返回值是Method数组或Method对象；每个Method对应一个方法，获得Method对象后，程序可以通过该Method来调用它对应的方法，在Method里有一个invoke方法`，该方法的签名为：

```
Object invoke(Object obj , Object...... args)//该方法中的obj是执行该方法的主调，也就是该类的对象，后面的args是执行该方法时传入该方法的实参
```

```
public class Common {
    public static void main(String[] agrs) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
        Class clazz = Class.forName("Person");
        Object obj = clazz.newInstance();
        Method method = clazz.getMethod("info", String.class);
        method.invoke(obj, "caonima");
    }
}
```
+ `当通过Method的invoke()方法来调用对应的方法时，java会要求程序必须有调用该方法的权限，如果程序确实需要调用某个对象的private方法，则可以先调用Method对象的如下方法`：

```
setAccessible(boolean flag);//将Method对象的accessable设置为指定的布尔值，值为true，指示该Method在使用时应该取消java语言的访问权限检查，值为false，则表示该Method在使用时要实施java语言的访问权限检查
```

#### 访问成员变量值

+ `通过Class对象的getFields()、getField()方法可以获取该类所包括的全部成员变量或指定的成员变量`；Field提供了如下两组方法来获取和设置成员变量值：

|方法|说明|
|------|------|
|`getXxx(Object obj)`|`获取obj对象的该成员变量的值，此处的Xxx为8种基本类型，如果是引用变量，则去掉Xxx`|
|`setXxx(Object obj , Xxx value)`|`将obj对象的该成员变量的值设置成value值，此处的Xxx为8种基本类型，如果是引用变量，则去掉Xxx`|

+ `使用这两个方法可以随意的访问指定的所有成员变量，包括private修饰的成员变量`；

```
public class Common {
    public static void main(String[] agrs) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
        Class clazz = Class.forName("Person");
        Object obj = clazz.newInstance();
        Field field = clazz.getDeclaredField("age");
        field.setAccessible(true);
        field.setInt(obj, 100);
        System.out.print(obj);//输出的是100
    }
}

class Person {
    private int age = 23;

    public void info(String s) {
        System.out.print("输出" + s);
    }

    @Override
    public String toString() {
        return "" + age;
    }
}
```

#### 操作数组
+ `在java.lang.reflect包下提供了一个Array类，Array对象可以代表所有的数组，程序可以通过使用Array来动态的创建，操作数组元素等`；

|Array提供的几个类方法|说明|
|------|------|
|`static Object newInstance(Class<?> componentType ,int .....  length)`|`创建一个具有指定元素类型的指定维度的新数组`|
|`static xxx getXxx(Object array,int index)`|`返回array数组中第index个元素其中xxx指的是基本数据类型，如果是引用类型则去掉方法的xxx`|
|`static xxx setXxx(Object array,int index)`|`将array数组的第index个元素其中xxx指的是基本数据类型，如果是引用类型则去掉方法的xxx`|

```
public class Common {
    public static void main(String[] agrs) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
        Object arr = Array.newInstance(String.class, 10);
        Array.set(arr, 5, "SB");
        Array.set(arr, 6, "CAONIMA");
        Object book1 = Array.get(arr, 5);
        Object book2 = Array.get(arr, 6);
        System.out.print(book1);//输出SB
        System.out.print(book2);//输出CAONIMA
    }
}
```
