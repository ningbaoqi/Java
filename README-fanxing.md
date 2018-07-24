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
