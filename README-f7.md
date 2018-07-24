### 类加载机制及反射建议
#### 注意Class类的特殊性
+ `Java使用一个元类（MetaClass）来描述加载到内存中的类数据，这就是Class类，它是一个描述类的类对象`，比如：`Dog.class文件加载到内存后就会有一个Class实例描述它`，`因为Class类是类中类，也就有预示着它有很多特殊的地方`：`无构造函数`。Java中的类一般都有构造函数，用于创建实例对象，`但是Class类却没有构造函数，不能实例化，Class对象是在加载类时由Java虚拟机通过调用类加载器中的defineClass方法自动创建的`；`可以描述基本类型，虽然8个基本类型在JVM中并不是一个对象，他们一般存在于栈内存中，但是Class类仍然可以描述他们`，例如`可以使用int.class表示int类型的类对象`；`其对象就是单例模式，一个Class的实例对象描述一个类，并且只描述一个类，反过来也成立`，`一个类只有一个Class实例对象`；`Class类是Java的反射入口，只有在获得了一个类的描述对象后才能动态的加载、调用`，一般获得一个Class对象有三种途径：`类属性方式：String.class`；`对象的getClass方法`：如`new String().getClass()`；`forName方法加载`，如`Class.forName("java.lang.String")`；
#### 适时选择getDeclaredxxx和getxxx
+ `Java的Class类提供了很多的getDeclarexxx方法和getxxx方法`，例如：`getDeclareMethod和getMethod成对出现`，`getDeclareConstructors和getConstructors也是成对出现的`，`getMethod方法获得的是所有的public访问级别的方法，包括从父类继承的方法，而getDeclareMethod获得是自身类的所有方法，包括public、private等，而且不受限于访问权限`；
#### 反射访问属性或方法时将Accessible设置为true
+ Java中通过反射执行一个方法的过程如下：`获取一个方法对象，然后根据isAccessible返回值确定是否能够执行，如果返回值为false，则需要调用setAccessible（true），最后再调用invoke执行方法`；

```
Method method = .......;
//检查是否可以访问
if(!method.isAccessible()){
       method.setAccessible(true);
}
//执行方法
method.invoke(obj , args);
```
+ `这就可以大幅度提升系统性能（当然了，由于取消了安全检查，也可以运行private方法、访问private私有属性了），经过测试，在大量的反射情况下，设置Accessible为true可以提升性能20倍以上`；

#### 不需要太多关注反射效率
+ `反射的效率相对于正常的代码执行确实低很多（经过测试，相差15倍左右），但是它是一个非常有效的运行期工具类，只要代码结构清晰，可读性好那就先开发起来，等到进行性能测试时证明此处性能确实有问题时再修改也不迟（一般情况下反射并不是性能的终极杀手，而代码结构混乱，可读性差则很可能会埋下性能隐患），反射效率低是个真命题，但是因为这一点而不使用它就是一个假命题`；

#### 使用forName动态加载类文件
+ `动态加载是指在程序运行时加载需要的类库文件`，对Java程序来说，一般情况下，`一个类文件在启动时或首次初始化时会被加载到内存中，而反射则可以在运行时决定是否要加载一个类`，比如从Web上接收一个String参数作为类名，然后在JVM中加载并初始化，这就是动态加载，此动态加载通常是通过`Class.forName(String)`实现的；`一个类文件只有在被加载到内存中后才可能生成实例对象，也就是说一个对象的生成必然经历以下两个步骤：加载到内存中生成Class的实例对象；通过new关键字生成实例对象`；`动态加载的意义：加载一个类即表示要初始化该类的static变量，特别是static代码块`；

```
public class Common {
    public static void main(String[] agrs) throws NoSuchFieldException, ClassNotFoundException {
        Class.forName("Utils");//动态加载
    }
}

class Utils {
    static {
        System.out.print("a");//将会调用
    }
}
```
+ `forName只是加载类，并不执行任何代码`；

#### 动态加载不适合数组
+ `forName要加载一个类，那它首先必须是一个类，其次，它必须具有可追索的类路径，否则就会报ClassNotFoundException异常`；`在Java中数组是一个非常特殊的类，虽然它是一个类，但没有定义类路径`；

|数组编译对应的关系表|编译后的类型|
|------|------|
|`byte[]`|`[B`|
|`char[]`|`[C`|
|`Double[]`|`[D`|
|`Float[]`|`[F`|
|`Int[]`|`[I`|
|`Long[]`|`[J`|
|`Short[]`|`[S`|
|`Boolean[]`|`[Z`|
|`String[]`引用类型数组|`[L引用类型`（如：`[Ljava.lang.String；`）|

```
public class Common {
    public static void main(String[] agrs) throws NoSuchFieldException, ClassNotFoundException {
        String[] strings = new String[10];
        Class.forName("[Ljava.lang.String;");
        Class.forName("[J");
    }
}
```

+ `虽然上面的代码可以动态加载一个数组类，但是这没有任何意义，因为它不能生成一个数组对象，也就是说以上代码只是把一个String类型的数组和long类型的数组类加载到了内存中（如果内存中没有这个类的话），并不能通过newInstance方法生成一个实例对象，因为它没有定义数组的长度，在Java中数组是定长的，没有长度的数组是不允许存在的；动态加载一个数组，可以使用Array数组反射类来动态加载`；

```
public class Common {
    public static void main(String[] agrs) throws NoSuchFieldException, ClassNotFoundException {
        String[] strings = (String[]) Array.newInstance(String.class, 8);//动态创建数组
        int[][] ints = (int[][]) Array.newInstance(int.class, 2, 3);//创建一个多维数组
    }
}
```
+ `通过反射操作数组使用Array类，不要采用通用的反射处理API`；

#### 反射让模版方法模式更强大

```
abstract class AbsPopulator {
    public final void dataInit() throws InvocationTargetException, IllegalAccessException {
        Method[] methods = getClass().getMethods();
        for (Method m : methods) {
            if (isInitDataMethod(m)) {
                m.invoke(this);
            }
        }
    }

    private boolean isInitDataMethod(Method method) {
        return method.getName().startsWith("init") && Modifier.isPublic(method.getModifiers()) && method.getReturnType().equals(Void.TYPE) && !method.isVarArgs() && !Modifier.isAbstract(method.getModifiers());
    }
}

class UserPop extends AbsPopulator {
    public void initUser(){}//初始化用户表，如创建、加载数据等
    public void initPassword(){} //初始化密码
    public void initJobs(){} //初始化工作任务
}
```
+ `决定使用模版方法模式时，请尝试使用反射方法实现，它会让程序更灵活、更强大`；

#### 使用反射增加装饰模式的普适性

+ `装饰模式`的定义是：`动态给某一个对象添加一些额外的职责，就增加功能来说，装饰模式相比于生成子类更加灵活`；
```
public class Common {
    public static void main(String[] agrs) {
        Animal jerry = new Rat();
        jerry = new DecorateAnimal(jerry, FlyFeature.class);//为jerry增加钻地能力
        jerry = new DecorateAnimal(jerry, DigFeature.class);
        jerry.doStuff();
    }
}

interface Animal {
    void doStuff();
}

class Rat implements Animal {
    @Override
    public void doStuff() {
        System.out.print("jerry will play with tom");
    }
}
//目前只是临时的为Rat类增加这些能力，使用装饰模式更符合此处的场景，以下为装饰类

interface Feature {
    void load();
}

class FlyFeature implements Feature {
    @Override
    public void load() {
        System.out.print("增加一只翅膀");
    }
}

class DigFeature implements Feature {
    @Override
    public void load() {
        System.out.print("增加钻地能力");
    }
}
//现在将两种属性赋予到Jerry身上，就需要一个包装动作类；如下所示

class DecorateAnimal implements Animal {
    private Animal animal;
    private Class<? extends Feature> clz;

    public DecorateAnimal(Animal animal, Class<? extends Feature> clazz) {
        this.animal = animal;
        this.clz = clazz;
    }

    @Override
    public void doStuff() {
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                Object obj = null;
                if (Modifier.isPublic(method.getModifiers())) {
                    obj = method.invoke(clz.newInstance(), args);
                }
                animal.doStuff();
                return obj;
            }
        };
        ClassLoader classLoader = getClass().getClassLoader();
        Feature feature = (Feature) Proxy.newProxyInstance(classLoader, clz.getInterfaces(), handler);
        feature.load();
    }
}
```

#### 动态代理可以使代理模式更加灵活

+ `Java的反射框架提供了动态代理机制，允许在运行期间对目标类生成代理，避免重复开发，我们知道一个静态代理是通过代理主题角色（Proxy）和具体主题角色（Real Subject）共同实现抽象主题角色（Subject）的逻辑，只是代理主题角色把相关的执行逻辑委托给了具体主题角色而已`；
```
interface Subject {
    void request();
}

class RealSubject implements Subject {
    @Override
    public void request() {
        //实现逻辑代码
    }
}

class Proxy implements Subject {
    private Subject subject = null;

    public Proxy() {
        subject = new RealSubject();
    }

    public Proxy(Subject subject) {
        this.subject = subject;
    }
    @Override
    public void request() {
        before();
        subject.request();
        after();
    }

    private void before(){//预处理

    }
    private void after(){//善后处理

    }
}
```
#### 简单的动态代理模型
+ `Java还提供了java.lang.reflect.Proxy用于实现动态代理，只要提供一个抽象主题角色和具体主题角色，就可以动态实现其逻辑`；

```
public class Common {
    public static void main(String[] agrs) {
        Subject subject = new RealSubject();
        InvocationHandler handler = new SubjectHandler(subject);
        ClassLoader classLoader = subject.getClass().getClassLoader();
        Subject proxy = (Subject) Proxy.newProxyInstance(classLoader, subject.getClass().getInterfaces(), handler);
        proxy.request();
    }
}

interface Subject {
    void request();
}

class RealSubject implements Subject {
    @Override
    public void request() {
        //业务逻辑代码
    }
}

class SubjectHandler implements InvocationHandler {
    private Subject subject;
    public SubjectHandler(Subject subject){
        this.subject = subject;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object object = method.invoke(subject, args);
        return object;
    }
}
```

+ `动态代理`是根据`被代理的接口生成所有方法的，也就是说给定一个或多个接口，动态代理会宣布“我已经实现了该接口下的所有方法”`，`通过InvocationHandler接口的实现类来实现，所有方法都是由该Handler进行处理的，即所有被代理的方法都由InvocationHandler接管实际的处理任务`；`动态代理很容易实现通用的代理类，只要在InvocationHandler的invoke方法中读取持久化数据即可实现，而且还能实现动态切入的效果，这也是AOP编程理念`；
