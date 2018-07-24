### 引用类型建议
#### 警惕自增的陷阱
```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        int count = 0;
        for (int i = 0; i < 10; i++) {
            count = count++;
        }
        System.out.print("count = " + count);//输出count=0
    }
}
```
+ `count++是一个表达式，是有返回值的，它的返回值就是count自加前的值`，`java对自加是这样处理的，首先把count的值（注意是值，不是引用）拷贝到一个临时变量区，然后对count变量加一，最后返回临时变量区的值`；

|程序第一次循环时的详细处理步骤|
|------|
|`JVM把count值（其值为0）拷贝到临时变量区`|
|`count值加一，这时候count的值是1`|
|`返回临时变量区的值，注意这个值是0，没有修改过`|
|`返回值赋值给count，此时count值被重置成0`|

#### 养成良好习惯，显式声明UID
+ `类实现Serializable接口的目的是为了可持久化`，比如`网络传输或本地传输，为系统的分布和异构部署提供先决支持条件，若没有序列化，现在我们熟悉的远程调用、对象数据库都不可能存在`；`通过SerialVersionUID，也叫做流标识符`，即类的版本定义的，它`可以显式声明也可以隐式声明`；`显式声明serialVersionUID可以避免对象不一致，但尽量不要以这种方式向JVM撒谎`；
```
public class Test {
    public static void main(String[] args) {
        Person person = new Person();
        person.setName("dasd");
        SerializationUtils.writeObject(person);//序列化保存到磁盘上
    }
}

class Person implements Serializable {
    private static final long serialVersionUID = 111L;//最好加上这个
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

class SerializationUtils {
    private static String FILE_NAME = "C:/obj.bin";

    public static void writeObject(Serializable serializable) {
        ObjectOutputStream objectOutputStream;
        try {
            objectOutputStream = new ObjectOutputStream(new FileOutputStream(FILE_NAME));
            objectOutputStream.writeObject(serializable);
            objectOutputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static Object readObject() {
        Object object = null;
        ObjectInput input;
        try {
            input = new ObjectInputStream(new FileInputStream(FILE_NAME));
            object = input.readObject();
            input.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return object;
    }
}
```

#### 避免用序列化类在构造函数中为不变量final赋值
+ `反序列化时构造函数是不会执行的`；`反序列化的执行过程：JVM从数据流中获取一个Object对象，然后根据数据流中的类文件描述信息（在序列化时，保存到磁盘上的对象文件中包含的类描述信息，注意是类描述信息，不是类）查看`，`发现是final变量，需要重新计算`，`于是引用Person类中的name值，而此时JVM又发现name竟然没有赋值，不能引用`，`于是它很聪明的不再初始化，保持原始状态；在序列化类中，不使用构造函数为final变量赋值`；
#### 避免为final变量复杂赋值
+ `为final变量赋值还有一种方式，通过方法赋值，即直接在声明时通过方法返回值赋值`；
```
public class Test {
    public static void main(String[] args) {
        Person person = new Person();
        System.out.print(person.name);
    }
}

class Person implements Serializable {
    private static final long serialVersionUID = 1111L;
    public final String name = initName();

    public String initName() {
        return "nbq";
    }
}
```

+ `name属性是通过initName方法的返回值赋值的，这在复杂类中经常用到，这比使用构造函数赋值更简洁，易修改`；`但是在序列化时候，就会有问题；当修改返回值的时候，反序列化生成的对象的name还是原来的值，上面所说的final会被重新赋值，其中的值指的是简单对象`，简单对象包括：`8个基本类型，以及数组、字符串（字符串情况很复杂，不通过new关键字生成的String对象的情况下，final变量的赋值与基本类型相同）但是不能方法赋值`；

|保存到磁盘或网络的对象文件有两部分|
|------|
|`类描述信息；包括包路径，继承关系，访问权限，变量描述，变量访问权限，方法签名，返回值，以及变量的关联类信息，要注意的一点是：它并不是class文件的翻版，它不记录方法，构造器，static变量等的具体实现，之所以类描述会被保存，很简单，是因为能去也能回，这保证反序列化的健壮运行`|
|`非瞬态(transient关键字)和非静态的实例变量值；注意：这里的值如果是一个基本类型，就是一个简单值保存下来，如果是复杂对象，连该对象和关联类信息一起保存，并且持续递归下去（关联类也必须实现Serializable接口，否则会出现序列化异常），也就是说递归到最后，其实还是基本数据类型的保存`|

##### 反序列化时final变量在以下情况下不会被重新赋值；
+ 一、`通过构造函数为final变量赋值`；二、`通过方法返回值为final变量赋值`；三、`final修饰的属性不是基本类型`；
#### 使用序列化类的私有方法巧妙解决部分属性持久化问题
+ `部分属性持久化部署，只要把不需要持久化的属性加上瞬态关键字(transient关键字)即可`；`但是这是一种解决方案，但是有时候因为业务逻辑行不通；可以实现Serializable接口的类可以实现两个私有方法：writeObject和readObject方法，以影响和控制序列化和反序列化的过程`;
```
private void writeObject(ObjectOutputStream outputStream) throws IOException {
    outputStream.defaultWriteObject();//告知JVM按照默认的规则写入对象，惯例的写法是写在第一句话里
    outputStream.writeInt(/*想要传输的数据*/1);
}

private void readObject(ObjectInputStream inputStream) throws IOException, ClassNotFoundException {
    inputStream.defaultReadObject();//告知JVM按照默认规则读入对象，惯例的写法也时写在第一句话里
}
```
#### 避免instanceof非预期结果
```
public class Test {
    public static void main(String[] args) {
        boolean b1 = 'A' instanceof Character;//报错；instanceof 只能用于对象的判断，不能用于基本类型的判断
        boolean b2 = null instanceof String;//instanceof的特殊规定，左操作数时null，结果直接返回null
        boolean b3 = new GenericClass<String>().isDateInstance("");//在泛型类中判断String对象是否时Date的实例
    }
}

class GenericClass<T> {
    public boolean isDateInstance(T t) {
        return t instanceof Date;
    }
}
```
#### 断言绝对不是鸡肋

|断言的基本用法|
|------|
|`assert <布尔表达式>`|
|`assert <布尔表达式> ：<错误信息>`|

+ `在布尔表达式为false时，抛出AssertionError错误，并附带了错误信息`；

|assert的两个特性|assert两种情况下不能用|
|------|------|
|`assert默认是不启用的，要启用就需要在编译、运行时加上相关的关键字`|`在对外公开的方法中`|
|`assert抛出的异常AssertionError是继承自Error的`|`在执行逻辑代码的情况下`|

+ 使用assert的地方；

|使用assert的地方|
|------|
|`在私有方法中放置assert作为输入参数的校验`|
|`流程控制中不可能达到的区域`|
|`建立程序探针`|

#### 易变业务使用脚本语言编写

|脚本语言的三大特征|
|------|
|`灵活；脚本语言一般都是动态类型，可以不用声明变量类型而直接使用，也可以在运行期间改变类型`|
|`便捷；脚本语言是一种解释型语言，不需要编译成二进制代码，也不需要像java一样生成字节码，它的执行是依靠解释器解释的，因此在运行期间变更代码非常容易，而且不用停止应用`|
|`简单；只能说部分脚本语言简单`|

##### 脚本文件model.js
```
function formula(var1, var2){
    return var1 + var2 * factor;
}
```
##### 执行代码
```
public class Test {
    public static void main(String[] args) {
        ScriptEngine engine = new ScriptEngineManager().getEngineByName("javascript");//获取一个javaScript的执行引擎
        Bindings bindings = engine.createBindings();//创建上下文变量
        bindings.put("factor", 1);
        engine.setBindings(bindings, ScriptContext.ENGINE_SCOPE);
        Scanner input = new Scanner(System.in);
        while (input.hasNextInt()) {
            int first = input.nextInt();
            int sec = input.nextInt();
            engine.eval(new FileReader("/home/ningbaoqi/model.js"));//执行js代码
            if (engine instanceof Invocable) {//是否可调用方法
                Invocable in = (Invocable) engine;
                Double result = in.invokeFunction("formula", first, sec);//执行js中的函数
                result.intValue();//运算结果
            }
        }
    }
}
```
##### 随时修改的脚本
```
function formula(var1, var2){
    return var1 + var2 * factor + 100;
}
```
+ `如果要变换算法，可以直接进行修改并且JVM不用退出，可继续执行`；`这就是脚本语言对系统设计最有利的地方，可以随时发布而不用重新部署`；
