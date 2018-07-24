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