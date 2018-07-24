### 对象拷贝建议
#### 避免对象的浅拷贝
+ `浅拷贝：也叫做影子拷贝，存在对象属性拷贝不彻底的问题`；
```
public class Test {
    public static void main(String[] args) throws CloneNotSupportedException {
        Person f = new Person("F");
        Person s1 = new Person("SON1", f);
        Person s2 = (Person) s1.clone();
        s2.setName("son2");
        s1.getFather().setName("f2");//之后s1、s2的father的名字都是f2
    }
}

class Person implements Cloneable {
    private String name;
    private Person father;

    public Person(String _name) {
        this.name = _name;
    }

    public Person(String _name, Person _parent) {
        this.name = _name;
        this.father = _parent;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Person getFather() {
        return father;
    }

    public void setFather(Person father) {
        this.father = father;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Person p = null;
        p = (Person) super.clone();
        return p;
    }
}
```
+ `出现浅拷贝，问题出现在clone方法，因为Object中clone方法是浅拷贝的，也就是说他并不会把对象的所有属性全部拷贝一份，而是有选择的拷贝`；

|拷贝规则|
|------|
|`基本类型；如果变量是基本类型，则拷贝其值`|
|`对象；如果变量是一个实例对象，则拷贝地址引用`|
|`String字符串；拷贝的也是地址，是个引用，但是在修改时，他会从字符串池中重新生成新的字符串，原有的字符串对象保持不变`|

+ `可以进行如下修改实现对象的深拷贝：深拷贝保证拷贝出来的对象自成一体，不受母体的影响，和new生成的对象没有任何区别`；
```
@Override
protected Object clone() throws CloneNotSupportedException {
    Person p = null;
    p = (Person) super.clone();
    p.setFather(new Person(p.getFather().getName()));
    return p;
}
```
+ `浅拷贝只是java提供的一种简单拷贝机制，不便于直接使用`；
#### 推荐使用序列化实现对象的拷贝
+ `可以通过序列化方式来处理，在内存中通过字节流的拷贝来实现，也就是把母体对象写到一个字节流中，再从字节流中将其读出来，这样就可以重建一个新对象了，该新对象与母体对象之间不存在引用共享的问题，也就相当于深拷贝了一个新对象`；
```
class CloneUtils {
    public static <T extends Serializable> T clone(T obj) throws IOException, ClassNotFoundException {
        T cloneObj = null;
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(byteArrayOutputStream);
        objectOutputStream.writeObject(obj);
        objectOutputStream.close();

        ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(byteArrayOutputStream.toByteArray());
        ObjectInputStream objectInputStream = new ObjectInputStream(byteArrayInputStream);
        cloneObj = (T) objectInputStream.readObject();
        objectInputStream.close();
        return cloneObj;
    }
}
```

|用此方法进行对象拷贝时需要注意两点|
|------|
|`对象的内部属性都是可序列化的；如果有内部属性不可序列化，则会抛出序列化异常`|
|`注意方法和属性的特殊修饰符；比如final static变量的序列化问题会被引入到对象的拷贝中来，同时transient变量（瞬态变量，不进行序列化的变量）也会影响到拷贝的效果`|

+ `采用序列化方法拷贝时还有一个更简单的方法，就是使用Apache下的commons工具包下的SerializationUtils类，直接使用更加简洁方便`；
