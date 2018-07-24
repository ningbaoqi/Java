### 对象序列化
+ 对象序列化的目标是将对象保存到磁盘中，或允许在网络中直接传输对象，对象序列化允许把内存中的java对象转换为平台无关的二进制流；
### 序列化的含义和意义
+ 序列化机制允许将实现序列化的Java对象转换成字节序列，这些字节序列可以保存在磁盘上，或通过网络传输，以备以后重新恢复成原来的对象，序列化机制使得对象可以脱离程序的运行而独立存在，对象的序列化Serialize指将一个java对象写入IO流中，与此对应的是，对象的反序列化Deserialize则指从IO流中恢复该java对象；如果需要让某个对象支持序列化机制，则必须让它的类是可序列化的，为了让某个类是可序列化的，该类必须实现以下两个接口之一：`Serializable`、`Externalizable`；java的很多类已经实现了Serializable，该接口是一个标记接口，实现该接口无需实现任何方法，他只是说明该类的实例是可序列化的；所有可能在网络上传输的对象的类都必须是可序列化的，否则程序将会出现异常；
### 使用对象流实现序列化
#### 程序实现序列化对象的步骤：
+ 创建一个ObjectOutputStream，这个输出流是一个处理流，必须建立在其他节点流的基础上：

```
//创建ObjectOutputStream输出流
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("Object.txt"));
```
+ 调用ObjectOutputStream对象的writeObject()方法输出可序列化对象：
```
//将一个Person对象per输出到输出流中
oos.writeObject(per);
```
+ 这样在Object.txt文件中的内容就是per对象；
#### 从二进制流中回复java对象，需要使用反序列化，反序列化的步骤：
+ 创建一个ObjectInputSream输入流，这个输入流是一个处理流，则必须建立与之关联的节点流的基础上：

```
//创建ObjectInputStream输入流
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("Object.txt"));
```
+ 调用ObjectInputSream对象的readObject()方法读取流中的对象，该方法返回一个Object类型的java对象，如果程序知道该java对象的类型，可以使用强制类型转换转换成其真实的类型：

```
Person per = (Person)ois.readObject();
```
+ 必须指出的是，反序列化读取的仅仅是java对象的数据，而不是java类，因此采用反序列化恢复java对象时，必须提供该java对象所属类的.class文件，否则将会报ClassNotFoundException；反序列化机制无须通过构造器来初始化java对象如果使用序列化机制向文件中写入了多个java对象，使用反序列化机制恢复对象时必须按实际写入的顺序读取，当一个序列化类有多个父类时（包括直接父类和间接父类），这些父类要么有无参构造器，要么也是序列化的，否则反序列化时将抛出InvalidClassException异常，如果父类是不可序列化的只有无参构造器，则该父类中定义的成员变量值不会序列化到二进制流中；
### 对象引用的序列化
+ 如果上述Person类的成员变量的类型不是基本类型或String类型，而是另一个引用类型，那么这个引用类必须是可序列化的，否则拥有该类型成员变量的类也是不可序列化的；

|java序列化机制采用了一种特殊的序列化算法，其算法内容如下|
|------|
|所有保存到磁盘中的对象都有一个序列化编号|
|当程序试图序列化一个对象时，程序将先检查该对象是否已经被序列化过，只有该对象从未（在本次虚拟机中）被序列化过，系统才会将该对象转换成字节序列并输出|
|如果某个对象已经被序列化过了，程序将只是直接输出一个序列化编号，而不是重新序列化该对象|

+ 当使用java序列化机制序列化可变对象时一定要注意，只有第一次调用writeObject()方法来输出对象时才会将对象转换成字节序列，并写入在ObjectOutputStream；在后面程序中即使该对象的实例变量发生了变化，再次调用writeObject()方法输出该对象时，改变后的实例变量也不会被输出；

### 自定义序列化
+ 通过在实例变量前面使用`transient`关键字修饰，可以指定java序列化时无须理会该实例变量；transient关键字只能用于修饰实例变量，不可修饰java程序中的其他成分；但是被transient修饰的实例变量将被完全隔离在序列化机制之外，这样导致在反序列化恢复java对象时无法取得该实例变量值；java还提供了一种自定义序列化机制，通过这种自定义序列化机制可以让程序控制如何如何序列化各实例变量，甚至完全不序列化某些实例变量；在序列化与反序列化过程中需要特殊处理的类应该提供如下特殊签名的方法，这些特殊的方法用以实现自定义序列化：

|方法|说明|
|------|------|
|private void writeObject(java.io.ObjectOutputStream out) throws IOException|该方法负责写入特定的实例状态，以便相应的readObject方法可以恢复它，通过重写该方法，程序员可以完全获得对序列化机制的控制，可以自主决定哪些实例变量需要序列化，需要怎么序列化，在默认情况下，该方法会调用out.defaultWriteObject来保存java对象的各实例变量，从而可以实现序列化java对象状态的目的|
|private void readObject(java.io.ObjectInputStream in) throws IOException , ClassNotFoundException;|该方法负责从流中读取并恢复对象实例变量，通过重写该方法，程序员可以完全获得对反序列化机制的控制，可以自主决定需要反序列化哪些实例变量，以及如何运行反序列化，在默认情况下，该方法调用in.defaultReadObject来恢复java对象的非瞬态实例变量，在通常情况下，readObject方法与writeObject方法对应，如果writeObject方法中对对象的实例变量进行了一些处理，则应该在readObject方法中对其实例变量进行相应的反处理，以便正确恢复该对象|
|private void readObjectNoData() throws ObjectStreamException|当序列化流不完整时，该方法可以用来正确的初始化反序列化对象，例如：接收方使用反序列化类的版本不同于发送方，或者接收方版本扩展的类不是发送方版本扩展的类，或者序列化流是被篡改了，系统都会调用该方法来初始化反序列化对象|

+ writeObject方法存储实例变量的顺序应该和readObject方法中恢复实例变量的顺序一致，否则将不能正常的恢复该java对象；还有一种更加彻底的自定义机制，它甚至可以在序列化对象时将该对象替换成其他对象，如果需要实现序列化某对象时替换对象，则应为序列化类提供如下特殊方法：此方法将由序列化机制调用，只要该方法存在，因为该方法可以拥有private、protected、package等访问权限，所以其子类有可能获得该方法：

```
ANY-ACCESS MODIFIER Object wroteReplace() throws ObjectStreamException
```
+ java的序列化机制保证在序列化某个对象之前，先调用该对象的writeReplace方法，在writeObject方法之前调用，如果该方法返回另一个java对象，则系统转为序列化另一个对象；与writeReplace方法相对的是，序列化机制里还有一个特殊的方法，他可以实现保护性复制整个对象：
```
ANY-ACCESS MODIFIER Object readResolve() throws ObjectStreamException
```
+ readResolve方法会紧跟在readObject方法之后被调用，该方法的返回值将会代替原来反序列化的对象，而原来readObject方法反序列化的对象将会被立即丢弃；readResolve方法在序列化单例类、枚举类时尤其有用；所有的单例类、枚举类在实现序列化时都应该实现readResolve方法，这样才能保证反序列化的对象依然正常；readResolve方法可以使用任意访问限定符修饰；反序列化机制在恢复java对象时无须调用构造器来初始化对象，从这种意义上来说，序列化机制可以用来克隆对象；

### 另一种自定义序列化机制
+ java还提供了另一种序列化机制，这种序列化方式完全由程序员决定存储和恢复对象数据，要实现该目标，java类必须实现Externalizable接口；在该接口定义了如下两个方法：

|方法|说明|
|-------|--------|
|void readExternal(ObjectInput in)|需要序列化的类实现readExternal方法来实现反序列化，该方法调用DataInput的方法来恢复基本类型的实例变量值，调用ObjectInput的readObject方法来恢复引用变量类型的实例变量值|
|void writeRxternal(ObjectOutput out)|需要序列化的类实现writeRxternal方法来保存对象的状态，该方法调用DataOutput的方法来保存基本类型的实例变量值，调用ObjectOutput的writeObject方法来保存引用类型的实例变量值|

+ 需要指出的是：当使用Externalizable机制反序列化对象时，程序先会调用public的无参构造器创建实例，然后才执行readExternal方法进行反序列化，因此实现Externalizable的序列化类必须提供public 的无参构造器；

|关于序列化注意一下几点|
|------|
|对象的类名、实例变量（包括基本类型、数组、对其他对象的引用）都会被序列化；方法、类变量（即static修饰的成员变量）、transient实例变量（也被称为瞬态实例变量）都不会被序列化|
|实现Serializable接口的类如果需要让某个实例变量不被序列化，则可在该实例变量前加transient修饰符，而不是加static关键字；虽然static关键字能够实现这样的效果|
|保证序列化对象的实例变量类型也是可序列化的，否则需要使用transient关键字来修饰该实例变量，要不然，该类无法进行序列化|
|反序列化对象时必须有序列化对象的class文件|
|当通过文件、网络来读取序列化后的对象时，必须按实际写入顺序读取|

### 版本

+ 随着项目的升级，系统的class文件也会升级，java如何保证两个class文件的兼容性呢？java序列化机制允许为序列化类提供一个private static final 的serialVersionUID值，该类变量的值用来标识该java类的序列化版本，也就是说，如果一个类升级后，只要它的serialVersionUID类变量值保持不变，序列化机制就会把他们当成同一个序列化版本；如果不显式定义serialVersionUID类变量的值，该类变量的值将由JVM根据类的相关信息计算，而修改后的类的计算结果与修改前的类的计算结果往往不同，从而造成了对象的反序列化因为类版本不兼容而失败；可以通过JDK安装路径下的bin目录下的serialver.exe工具来获得该类的serialVersionUID类变量的值：`serialver Person`；不显式指定serialVersionUID类变量的值的另一个坏处就是：不利于程序在不同的JVM之间移植，因为不同的编辑器对该类变量的计算策略可能不同，从而造成虽然类完全没有改变，但是因为JVM不同，也会出现序列化版本不兼容而无法正确反序列化的现象；
