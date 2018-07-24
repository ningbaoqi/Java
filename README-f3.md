### 使用反射生成JDK动态代理
+ `在java的java.lang.reflect包下提供了一个Proxy类和一个InvocationHandler接口，通过使用这个类和接口可以生成JDK动态代理类或动态代理对象`； 
#### 使用Proxy和InvocationHandler创建动态代理
+ `Proxy提供了用于创建动态代理类和代理对象的静态方法，她也是所有动态代理类的父类，如果在程序中为一个或多个接口动态的生成实现类，就可以使用Proxy来创建动态代理类；如果需要为一个或多个接口动态的创建实例，也可以使用Proxy来创建动态代理实例`；

|Proxy提供了两个方法来创建动态代理类和动态代理实例|说明|
|------|------|
|`static Class<?> getProxyClass(ClassLoader loader , Class<?>... interfaces)`|`创建一个动态代理类所对应的Class对象，该代理类将实现interfaces所指定的多个接口，第一个ClassLoader参数指定生成动态代理类的类加载器`|
|`static Object newProxyInstance(ClassLoader loader , Class<?>[] interfaces,InvocationHandler h)`|`直接创建一个动态代理对象，该代理对象的实现类实现了interfaces指定的系列接口，执行代理对象的每个方法时都会被替换指定InocationHandler对象的invoke方法`|

+ `程序中可以采用先生成一个动态代理类，然后通过动态代理类来创建代理对象的方式生成一个动态代理对象`：

```
//创建一个InvocationHandler对象
InvocationHandler handler = new MyInvocationHandler(......);
//使用Proxy生成一个动态代理类proxyClass
Class proxyClass = Proxy.getProxyClass(Foo.class.getClassLoader() , new Class[] {Foo.class});
//获取proxyClass类中带一个InvocationHandler参数的构造器
Constructor ctor = proxyClass.getConstructor(new Class[] {InvocationHandler.class});
//调用ctor的newInstance方法来创建代理对象
Foo f = (Foo) ctor.newInstance(new Object[]{handler});
```

```
public class Common {
    public static void main(String[] agrs) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
        InvocationHandler handler = new MyInvocationHandler();
        Sb sb = (Sb) Proxy.newProxyInstance(Sb.class.getClassLoader(), new Class[]{Sb.class}, handler);
        sb.walk();
        sb.sayHello("123");
    }
}

class Person {}

interface Sb {
    void walk();

    void sayHello(String name);
}

class MyInvocationHandler implements InvocationHandler {

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.print(method);//输出正在执行方法的方法签名
        if (args != null) {
            for (Object object : args) {
                System.out.print(object);
            }
        } else {

        }
        return null;
    }
}
```
+ 该invoke方法的三个参数：`proxy：代表动态代理对象`；`method：代表正在执行的方法`：`args：代表调用目标方法时传入的实参`；`不管程序是执行代理对象的walk还是sayHello方法，实际上执行的都是InvocationHandler对象的invoke方法`；

#### 动态代理和AOP
+ `由于JDK动态代理只能为接口创建动态代理`：
```
public class Common {
    public static void main(String[] agrs) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
        Dog target = new GunDog();
        Dog dog = (Dog) MyProxyFactory.getProxy(target);
        dog.info();
        dog.run();
    }
}

interface Dog {
    void info();

    void run();
}

class GunDog implements Dog {

    @Override
    public void info() {

    }

    @Override
    public void run() {

    }
}

class DogUtil {
    public void method1() {

    }

    public void method2() {

    }
}

class MyInvocationHandler implements InvocationHandler {
    private Object object;

    public void setTarget(Object object) {
        this.object = object;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        DogUtil dogUtil = new DogUtil();
        dogUtil.method1();
        Object result = method.invoke(object, args);
        dogUtil.method2();
        return result;
    }
}

class MyProxyFactory {
    public static Object getProxy(Object object) {
        MyInvocationHandler handler = new MyInvocationHandler();
        handler.setTarget(object);
        return Proxy.newProxyInstance(object.getClass().getClassLoader(), object.getClass().getInterfaces(), handler);
    }
}
```
