### 面向对象建议
#### 构造代码块会想你所想
```
public class Test {
    public static void main(String[] args) {
        new Base();
        new Base("");
        new Base(1);
        Base.getNum();//输出3
    }
}

class Base {
    private static int num = 0;
    {
        num++;
    }

    public Base() {

    }

    public Base(String string) {
        this();
    }

    public Base(int i) {

    }
    public static int getNum(){
        return num;
    }
}
```
+ `编译器会把构造代码块插入到每一个构造函数中，但是有一个例外的情况没有说明：如果遇到this关键字（也就是构造函数调用自身其他的构造函数时）则不插入构造代码块`；
#### 使用静态内部类提高封装性
+ `Java中的嵌套类分为两种，静态内部类，内部类；静态内部类有两个优点：加强了类的封装性和提高了代码的可读性`；
```
public class Test {
    public static void main(String[] args) {
        Person person = new Person("zhangsan");
        person.setHome(new Person.Home("shanghai" , "021"));
    }
}

class Person {
    private String name;
    private Home home;

    public Person(String _name) {
        this.name = _name;
    }

    public void setHome(Home _home) {
        this.home = _home;
    }

    public static class Home {
        private String address;
        private String tel;

        public Home(String _address, String _tel) {
            this.address = _address;
            this.tel = _tel;
        }
    }
}
```
+ `提高的封装性，从代码位置来看，静态内部类放置在外部类内`，其代码层意义就是：`静态内部类是外部类的子行为或子属性，两者之间保持着一定的关系；提高的代码的可读性，相关联的代码放在了一起，可读性当然提高了；形似内部，神则外部，静态内部类虽然存在于外部类内，而且编译后的类文件名也包含外部类（格式为：外部类+$+内部类），但是他可以脱离外部类存在，也就是说我们仍然可以通过new Home()声明一个Home对象，只是需要导入Person.Home而已`；`静态内部类不持有外部类的引用，可以访问外部类的静态方法和静态属性（如果是private权限也能访问，这是由代码位置所决定的），其他则不能访问`；`静态内部类不依赖外部类`：`普通内部类与外部类之间是相互依赖的关系，内部类实例不能脱离外部类实例，也就是说他们会同生共死，一起声明，一起被垃圾回收器回收，而静态内部类是可以独立存在的，即使外部类消亡了，静态内部类还是可以存在的`；
#### 匿名类的构造函数很特殊
```
public class Test {
    public static void main(String[] args) {
        Calculator C = new Calculator(1, 2) {
            {
                setOps(Ops.ADD);
            }
        };
    }
}

enum Ops {
    ADD , SUB
}

class Calculator {
    private int i , j , result;
    public Calculator(){}

    public Calculator(int i, int j) {
        this.i = i;
        this.j = j;
    }

    protected void setOps(Ops ops) {
        result = ops.equals(Ops.ADD) ? i + j : i - j;
    }

    public int getResult() {
        return result;
    }
}
```
+ `匿名类的构造函数特殊处理机制`，`一般类（也就是具有显式名字的类）的所有构造方法默认都是父类的无参构造的，而匿名类因为没有名字，只能由构造代码块代替，也就无所谓的有参还是无参构造方法了，它在初始化时直接调用了父类的同参数构造器，然后再调用了自己的构造代码块`；
#### 让多重继承成为现实
```
interface Father {
    int strong();
}

interface Mother {
    int kind();
}

class FatherImpl implements Father {

    @Override
    public int strong() {
        return 8;
    }
}

class MotherImpl implements Mother {

    @Override
    public int kind() {
        return 8;
    }
}

class Son extends FatherImpl implements Mother {

    @Override
    public int kind() {
        return new MotherSpecial().kind();
    }

    @Override
    public int strong() {
        return super.strong() + 1;
    }

    private class MotherSpecial extends MotherImpl {
        @Override
        public int kind() {
            return super.kind() - 1;
        }
    }
}
```
+ `内部类可以继承一个与外部类无关的类，保证内部类的独立性，正是基于这一点，多重继承才会成为可能；诸位在需要用到多重继承时，可以思考一下内部类`；
### 覆写equals方法时不要识别不出自己
```
public class Test {
    public static void main(String[] args) {
        Person p = new Person("zhangsan");
        Person p2 = new Person("zhangsan ");//后面由一个空格
        List<Person> list = new ArrayList<>();
        list.add(p);
        list.add(p2);
        list.contains(p);//返回true
        list.contains(p2);//返回false
    }
}

class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Person) {
            Person p = (Person) obj;
            return name.equalsIgnoreCase(p.getName().trim());
        }
        return false;
    }
}
```
+ `List检查是否包含元素时通过调用对象的equals方法来判断，也就是说contains(p1)传递进去，会一次执行p2.equals(p1)、p2.equals(p2)，只要有一个返回true，结果就是true，以为添加进集合的name是带有空格的，但是在equals算法中将空格去掉了，所以就找不到了，即返回false`；
#### equals应该考虑null值情景
```
public class Test {
    public static void main(String[] args) {
        Person p = new Person("z");
        Person p2 = new Person(null);
        List<Person> list = new ArrayList<>();
        list.add(p);
        list.add(p2);
        list.contains(p);//返回true
        list.contains(p2);//报出空指针异常
    }
}

class Person {
    private String name;

    public Person(String _name) {
        this.name = _name;
    }

    public String getName() {
        return name;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Person) {
            Person p = (Person) obj;
            return name.equalsIgnoreCase(p.getName().trim());
        }
        return false;
    }
}
```
#### 覆写equals方法必须覆写hashCode方法
```
public class Test {
    public static void main(String[] args) {
        Map<Person, Object> map = new HashMap<Person, Object>() {
            {
                put(new Person("z"), new Object());
            }
        };
        List<Person> list = new ArrayList<Person>() {
            {
                add(new Person("z"));
            }
        };
        boolean b1 = list.contains(new Person("z"));//输出true
        boolean b2 = map.containsKey(new Person("z"));//输出false
    }
}

class Person {
    private String name;

    public Person(String _name) {
        this.name = _name;
    }

    public String getName() {
        return name;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj != null && obj.getClass() == this.getClass()) {
            Person p = (Person) obj;
            if (p.getName() == null || name == null) {
                return false;
            } else {
                return name.equalsIgnoreCase(p.getName())
            }
        }
        return false;
    }
}
```
+ `Map中不包含是因为HashMap的底层处理机制是以数组的方式保存Map条目的，这其中的关键是这个数组下标的处理机制，依据传入元素的hashcode方法的返回值决定其数组的下标，如果该数组位置上已经有了Map条目，且与传入的键值相等则不处理，如不相等则覆盖，如果数组位置没有条目，则插入，并加入到Map条目中的链表里，同理，检查键是否存在也是根据哈希码确定位置，然后遍历查找键值的；哈希码是由Object类的本地方法生成的，确保每个对象有一个哈希码，由于我们没有重写hashcode方法，两个zhangsan对象的hashcode方法返回值肯定不同，在HashMap的数组中就找不到对应的Map条目了，于是返回了false`；
```
@override
public int hashCode(){
       return HashCodeBuilder().append(name).toHashCode();
}
```
+ `其中HashCodeBuilder是org.apache.commons.builder包下的一个哈希码生成工具`；
#### 在equals中使用getClass进行类型判断
```
public class Test {
    public static void main(String[] args) {
        Employee e = new Employee("z", 100);
        Employee e1 = new Employee("z", 101);
        Person p = new Person("Z");
        p.equals(e);//返回true
        p.equals(e1);//返回true
        e.equals(e1);//返回false
    }
}

class Person {
    private String name;

    public Person(String _name) {
        this.name = _name;
    }

    public String getName() {
        return name;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Person) {
            Person p = (Person) obj;
            return name.equalsIgnoreCase(p.getName());
        }
        return false;
    }
}

class Employee extends Person {
    private int id;
    public Employee(String _name , int _id) {
        super(_name);
        this.id = _id;
    }

    private int getId() {
        return this.id;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Employee) {
            Employee e = (Employee) obj;
            return super.equals(obj) && e.getId() == id;
        }
        return false;
    }
}
```
+ `在类型判断的时候应该使用getClass进行`，如下正确的判断方式；`这样三个都返回false`；
```
@Override
public boolean equals(Object obj) {
    if (obj != null && obj.getClass() == this.getClass()) {
        Person p = (Person) obj;
        if (p.getName() == null || name == null) {
            return false;
        } else {
            return name.equalsIgnoreCase(p.getName());
        } 
    }
    return false;
}
```
+ `总之，在覆写equals方法时建议使用getClass进行类型判断，而不要使用instanceof`；
#### 使用package-info类为包服务
+ `Java中有一个特殊的类，pakcage-info类，它是专门为包服务的`；
##### package-info主要体现

|package-info主要体现|
|------|
|他不能随便被创建；在一般的IDE中，Eclipse、package-info等文件是不能随便被创建的，会报type name is notvalid错误，类名无效，在Java变量定义规范中规定如下字符是允许的：字母、数字、下划线、以及$，不过中划线可不再之列，可以使用记事本创建，然后拷贝进去再该一下就成了，更直接的方法就是从别的项目中拷贝过来|
|它服务的对象很特殊；一个类是一类或一组事物的描述，但是package-info是描述和记录包信息的|
|package-info类不能有实现代码；pacakge-info类再怎么特殊也是一个类，也会被编译成package-info.class，但是在package-info.java文件中不能声明package-info类，package-info类还有几个特殊的地方就是不可以继承、没有接口、没有类间关系|

##### package-info的作用

|package-info的作用|
|------|
|声明友好类和包内访问常量；比如一个包中有很多内部访问的类或变量，就可以统一放在pacakge-info中，这样很方便，而且便于集中管理，可以减少友好类到处游走的情况|
|为在包上标注注解提供方便|
|提供包的整体注释说明|
