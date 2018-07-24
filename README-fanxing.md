### 泛型入门
#### 使用泛型
+ `List<String>`表明`该List只能保存字符串类型的对象`，java的参数化类型被称为泛型；`在集合接口、类后面添加尖括号，尖括号里放一个数据类型，即表明这个集合接口，集合类只能保存特定类型的对象`；
#### Java7泛型的菱形语法
+ 在java7之前，如果使用带泛型的接口、类定义变量，那么调用构造器创建对象时构造器的后面也必须带泛型如：
```
List<String> strList = new ArrayList<String>();
Map<String , Integer> map = new HashMap<String , Integer>();
```
+ 从java7开始，java允许在构造器后不需要带完整的泛型信息，只要给出一对尖括号即可，java推断尖括号里面应该是什么泛型信息；
```
List<String> strList = new ArrayList<>();
Map<String , Integer> map = new HashMap<>();
```
```
public class Common {
    public static void main(String[] args) {
        List<String> books = new ArrayList<>();
        books.add("a");
        books.add("b");
        Map<String, List<String>> school = new HashMap<>();
        List<String> scho = new ArrayList<>();
        scho.add("sb");
        school.put("caonima", scho);
    }
}
```
### 泛型与数组
+ 只能声明`List<String>[]`形式的数组，但是不能创建`ArrayList<String>[10]`这样的数组对象；
### 泛型方法
#### 定义泛型方法
+ `所谓的泛型方法就是在声明方法时定义一个或多个类型形参`：格式如下：
```
修饰符 <T , S> 返回值类型 方法名(参数列表){
       方法体
}
static <T> void fromArrayToCollection(T[] a , Collection<T> c){
       方法体
}
static <T> void test(Collection<? extends T> from , Collection<T> to){}
```
```
class Te {
    public static void main(String[] args) {
        Object[] objects = new Object[100];
        Collection<Object> collection = new ArrayList<>();
        fromArrayToCollection(objects , collection);

        String[] strings = new String[100];
        Collection<String> collection1 = new ArrayList<>();
        fromArrayToCollection(strings, collection1);
        fromArrayToCollection(strings , collection);
    }
    static <T> void fromArrayToCollection(T[] a, Collection<T> collection) {
        for (T t:a) {
            collection.add(t);
        }
    }
}
```
#### java7的菱形语法与泛型构造器
+ `java也允许在构造器签名中声明类型形参，这样就产生了所谓的泛型构造器`；
```
class T1 {
    public static void main(String[] args) {
        new Foo("js");
        new Foo(200);
        new <String>Foo("ja");
        new <String>Foo(123);//报错
    }
}
class Foo {
    public <T> Foo(T t) {

    }
}
```
#### 设定通配符下限
+ `java允许设定通配符的下限：< ? super Type>，这个通配符表示它必须是Type本身或者是Type的父类`：
```
class F {
    public static void main(String[] args) {
        List<Number> list = new ArrayList<>();
        List<Integer> list1 = new ArrayList<>();
        list1.add(5);
        Integer la = copy(list, list1);
    }

    public static <T> T copy(Collection<? super T> dest, Collection<T> src) {
        T last = null;
        for (T ele : src) {
            last = ele;
            dest.add(last);
        }
        return last;
    }
}
```
#### Java8改进的类型推断

|java8改进了泛型方法的类型判断能力，类型判断主要有如下两个方面|
|------|
|可通过调用方法的上下文来判断类型参数的目标类型|
|可在方法调用链中，将推断得到的类型参数传递到最后一个方法|

### 类型通配符
```
class Test {
    public static void main(String[] args) {
        List<String> list;
        test(list);//报错
    }
    public void test(List<Object> objects) {

    }
}
```
+ `如果Sub是Main的一个子类或子接口，而G是具有泛型声明的类或接口`，`G<Sub>`并不是`G<Main>`的子类型，一定要注意！！！！！！！！
#### 使用类型通配符
+ `为了表示各种泛型List的父类，可以使用类型通配符，类型通配符是一个问号？`，将该问号作为类型实参传入List集合，写作`List<?>`（`意思是元素类型未知的List`），这个问号被称为通配符，`它的元素类型可以匹配任意类型`：`带有通配符的list仅表示他是各种泛型List的父类，并不能把元素加入到其中`：
```
public void test(List<Object> objects) {
    List<?> list = new ArrayList<String>();
    list.add(new String());//报错
}
```
#### 设定类型通配符上限
+ 程序不希望这个`List<?>`是任何泛型List的父类，只希望他代表某个些类泛型List的父类；可以使用如以下的形式；表示所有Shape类及子类的泛型的父类；
```
List< ? entends Shape>
```
+ `类似的，由于程序无法确定这个受限的通配符的具体类型，所以不能把Shape类及其子类的对象加入到这个泛型集合中`；
#### 设置类型参数的上限
+ `可以在定义类型形参时设置上限，用于表示传给该类型形参的实际类型要么是该上限类型，要么是该上限类的子类`：
```
public class Apple <T extends Number>{}
```
+ `如果程序需要为类型形参设置多个上限（至多一个父类的上限，可以有很多接口上限）`，`表明该类型必须是其父类的子类，并且实现多个上限接口`：`表示T类型必须是Number类的子类或本类，并且实现了 java.io.Serializable接口`：
```
public class Apple<T extends Number & java.io.Serializable>{}
```
### 深入泛型
+ `所谓泛型，就是允许在定义类，接口，方法时使用类型形参，这个类型形参将在声明变量、创建对象、调用方法时动态的指定（即传入实际 的类型参数，也可称为类型实参）`；
#### 定义泛型接口、类
+ 泛型的实质：`允许在定义接口、类时声明类型形参，类型形参在整个接口、类体内可当成类型使用，几乎所有可使用的普通类型的地方都可以使用这种类型形参`；`可以在任何类，接口增加泛型声明（并不是只有集合类才可以使用泛型声明，虽然集合类是泛型的重要使用场所）`；
```
public class Common {
    public static void main(String[] args) {
        Apple<String> apple = new Apple<>("pingguo");
        apple.getInfo();
        Apple<Double> apple1 = new Apple<>(45.5);
        apple1.getInfo();
    }
}
class Apple<T>{
    private T info;

    public Apple() {

    }

    public Apple(T info) {
        this.info = info;
    }

    public void setInfo(T info) {
        this.info = info;
    }

    public T getInfo() {
        return this.info;
    }
}
```
+ `当创建带泛型声明的自定义类，为该类定义构造器时，构造器名还是原来的类名，不要增加泛型声明，调用该构造器时却可以使用泛型形式`；
#### 从泛型类派生子类
+ `当使用这些泛型类或接口作为父类时，不能再包含类形参数了：如下面的代码是错误的`：
```
class A3 extends Apple<T> {//报错
    
}
```
+ `如果想从Apple类派生子类`，可以改写为：如果从`Apple<String>`类派生子类，`则在Apple类中所有使用T类型参数的地方都被替换成了String类型，即他的子类将会继承到的方法T都变为了String方法`，如果子类需要重写父类的方法需要注意这一点；
```
class A2 extends Apple<String> {
    
}
```
+ `如果使用Apple类时没有传入实际的类型参数，java编辑器可能发出警告，使用未经检查或不安全的操作，但是不会报错，此时，系统会把Apple<T>类中的T形参当成Object类型来处理`：
```
class A1 extends Apple {
    
}
```
#### 并不存在泛型类
+ `无论泛型类的T参数使用的是什么，其实都是一个类，使用对象.getClass()方法就可以判断，只不过是使用T是加了一个限制；因此在静态方法、静态初始化块、静态变量的声明和初始化中不允许使用类型形参T`；
```
class Apple1<T> {
    static T info;//报错

    public static void bar(T t) {//报错
        
    }
}
```
