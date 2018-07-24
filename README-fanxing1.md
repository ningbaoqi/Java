### 泛型建议
#### 数组的真实类型必须是泛型类型的子类型
+ `主要是为了防止类型转换的时候会发生ClassCastException异常`；
#### 建议采用的顺序是`List<T>、List<?>、List<Object>`
+ `List<T>、List<?>、List<Object>`这三者都可以容纳所有的对象，但使用的顺序应该是首选 `List<T>`，次之`List<?>`，最后选择`List<Object>`；原因是：`List<T>`是确定的某一个类型，可以进行读写操作；`List<?>`是只读类型的，不能进行增加，修改操作，因为编辑器不知道List中容纳的是什么类型的元素，也就无法校验类型是否安全了，而且`List<?>`读取出的元素都是Object类型的，需要主动转型，所以它经常用于泛型方法的返回值，注意，`List<?>`虽然无法增加、修改元素，但是却可以删除元素；`List<Object>`也可以读写操作，但是它执行写入操作时需要向上转型，在读取数据后需要向下转型，而此时已经失去了泛型存在的意义；
#### 警惕泛型是不能协变和逆变的
+ 在编程语言的类型框架中，`协变和逆变是指宽类型和窄类型在某种情况下（如参数、返回值、泛型）替换或交换的特性`；简单的说，`协变是用一个窄类型替换宽类型`，`而逆变则是用宽类型覆盖窄类型`；
#### 不同的场景使用不同的泛型通配符
+ `Java泛型支持通配符`，可以单独使用一个`？`表示任意类，也可以使用`entends`关键字表示某个类或接口的子类型，还可以使用`super`关键字表示某一个类或接口的父类型；
#### 不能初始化泛型参数和数组
```
class F1<T> {
    private T t = new T();//报错
    private T[] array = new T[5];//报错
    private List<T> list = new ArrayList<T>();
}
```
+ `因为编译器在编译时需要获取T类型，但泛型在编译期间类型已经被擦除了，所以会报错`；但是我们有时候需要使用泛型数组该怎么办？
```
class F2<T> {
    private T t;
    private T[] array;
    private List<T> list = new ArrayList<>();

    public F2() {
        try {
            Class<?> type = Class.forName("");
            t = (T) type.newInstance();
            array = (T[]) Array.newInstance(type, 5);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        }
    }
}
```
+ `只要在F2构造函数中传入一个参数就可以获得type的值，然后生成各种对象`；`类的成员变量是在类初始化前初始化的，所以要求在初始化前它必须具有明确的类型，否则就只能声明，不能初始化`；
#### 强制声明泛型的实际类型
```
class C {
    public static void main(String[] args) {
        List<String> list = ArrayUtils.asList("A", "B");
        List<Integer> list1 = ArrayUtils.<Integer>asList();//参数为空，强制声明泛型类型，避免后续麻烦，指定就是一个Integer类型的集合
        List list2 = ArrayUtils.asList(1, 2, 3.1);//该集合就是一个Object类型的集合
    }
}

class ArrayUtils {
    public static <T> List<T> asList(T... ts) {
        List<T> list = new ArrayList<>();
        Collections.addAll(list, ts);
        return list;
    }
}
```
+ `在无法从代码中推断出泛型类型的情况下，即可强制声明泛型类型`；
#### 严格限定泛型类型采用多重界限
```
class Client {
    public static void main(String[] args) {
        discount(new Me());
    }
    public static <T extends Staff & Passenger> void discount(T t) {
        if (t.getSalary() < 2500 && t.isStanding()) {
            System.out.print("打折");
        }
    }
}

interface Staff {
    int getSalary();
}

interface Passenger {
    boolean isStanding();
}

class Me implements Staff, Passenger {

    @Override
    public int getSalary() {
        return 2000;
    }

    @Override
    public boolean isStanding() {
        return true;
    }
}
```
#### Java的泛型是类型擦除的
+ `Java的泛型在编译期有效，在运行期被删除，也就是说所有的泛型参数类型在编译后都会被清除掉`；
```
class Client {
    public static void main(String[] args) {
        Foo foo = new Foo();
    }
}

class Foo {
    public void arrayMethod(String[] strArray) {

    }

    public void arrayMethod(Integer[] intArray) {

    }

    public void listMethod(List<String> stringList) {//报错

    }

    public void listMethod(List<Integer> integers) {//报错

    }
}
```
+ `在编译后所有的泛型类型都会做相应的转化；如下为转化规则`；

|泛型编译后的转化规则|
|------|
|`List<String> 、List<Integer> 、List<T>`擦除后的类型为List|
|`List<String>[]`擦除后的类型为List[]|
|`List<? extends E> 、List<? super E>`擦除后的类型为List<E>|
|`List<T extends Serializable & Cloneable>`擦除后的类型为List<Serializable>|
+ `在编译后的字节码中已经没有泛型的任何信息了，也就是说一个泛型类和一个普通类在经过编译后都指向了同一个字节码文件`，如`Foo<T>`类，经过编译后将只有一个Foo.class类，不管是`Foo<String>还是Foo<Integer>`引用的都是同一个字节码文件，这样避免了JVM大换血，`C++的泛型生命期延续到了运行期，而java是在编译器擦除掉的`，如果JVM把泛型延续到运行期，那么JVM就会需要进行大量的重构工作；版本兼容，在编译期擦除掉可以更好的支持原生类型，`在java1.5或java1.6平台上，即使声明一个List这样的原生类型也是可以正常编译通过的，只是会产生警告信息而已；泛型的class对象是相同的`；
```
class Client {
    public static void main(String[] args) {
        List<String> ls = new ArrayList<>();
        List<Integer> li = new ArrayList<>();
        boolean equal = li.getClass() == ls.getClass();//返回true
    }
}
```
+ 泛型数组初始化时不能声明泛型类型；可以声明一个带有泛型参数的数组，但是不能初始化该数组，因为执行了类型擦除操作，`List<Object>[]`与`List<String>[]`就是同一回事了，编译器拒绝如此声明；
```
List<String>[] lists = new List<String>[];//报错
```
+ `instanceof不允许存在泛型参数`；
```
List<String> list = new ArrayList<>();
System.out.print(list instanceof  List<String>);//报错
```
