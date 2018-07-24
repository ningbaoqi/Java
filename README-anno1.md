### 基本Annotation
+ Annotation其实是代码里的特殊标记，这些标记可以在编译、类加载、运行时被读取，并执行相应的处理，通过使用注解，程序开发人员可以在不改变原有逻辑的情况下，在原文件中嵌入一些补充的信息，代码分析工具，开发工具和部署工具可以根据这些补充的信息进行验证或进行部署；Annotation是一个接口，`程序可以通过反射来获取指定的程序元素的Annotation对象`，然后通过Annotation对象来取得注解里的元数据，`Annotation能被用来为程序元素（类、方法、成员变量等）设置元数据`，Annotation不影响程序代码的执行，无论添加、删除Annotation，代码都始终如一的执行；Annotation必须使用工具来处理，工具负责提取Annotation里包含的元数据，工具还会根据这些元数据增加额外的功能，java提供的5个基本的Annotation的用法`使用Annotation时要在其前面增加@符号`，并把该Annotation当成一个修饰符来使用，用于修饰他所支持的程序元素；

|Annotation|描述|
|------|------|
|`@Override`|用来指定方法覆载的，他强制一个子类必须覆盖父类的方法，，只能修饰方法，不能修饰其他程序元素|
|`@Deprecated`|用于表示某个程序元素（类、方法等）已过时，当其他程序使用已过时的类、方法时，编译器将会给出警告；直接用于修饰程序中的程序单元如方法、类、接口等|
|`@Suppress Warnings`|指示被该Annotation修饰的程序元素（以及该程序元素中的所有子元素）取消显示指定的编译器警告，当使用这个Annotation时一定要在括号里使用@Suppress Warnings（value= "unchecked"）的形式为Annotation的成员变量设置值|
|`@Safe Varargs`|java7新增的；当把一个不带泛型的对象赋给一个带泛型的变量时，往往就会发生堆污染，使用该Annotation来抑制堆污染警告|
|`@FunctionalInterface`|是java8新增的；用来指定某个接口必须是函数式接口，只能修饰接口，不能修饰其他程序元素|

### JDK的元Annotation
+ JDK除了在java.lang下提供了5个基本的Annotation之外，还在java.lang.annotation包下提供了6个Meta Annotation，其中5个元Annotation都用于修饰其他的Annotation定义，其中`@Repeatable`专门用于定义java8新增的注解；
#### 使用@Retention
+ `@Retention`只能用于修饰Annotation定义，用于指定被修饰的Annotation可以保留多长时间，`@Retention`包含一个RetentionPolicy类型的value成员变量，所以使用`@Retention`时必须为该value成员变量指定值；

|value成员变量的值只能是这三个|描述|
|------|------|
|RetentionPolicy.CLASS|编译器将把Annotation记录在class文件中，当运行java程序时，JVM不可获取Annotation信息，这是默认值|
|RetentionPolicy.RUNTIME|编译器将把Annotation记录在class文件中，当运行java程序时，JVM也可以获取Annotation信息，程序可以通过反射获取该Annotation信息|
|RetentionPolicy.SOURCE|Annotation之保留在源代码中，编辑器直接丢弃这种Annotation|

+ 使用`@Retention`元Annotation可以采用两种方式为value设置指定值；

|方式|
|------|
|@Retention（value=RetentionPolicy.RUNTIME）|
|@Retention（RetentionPolicy.SOURCE）|

#### 使用@Target
+ `@Target`只能修饰一个Annotation定义，用于指定被修饰的Annotation能用于修饰哪些程序单元，`@Target`元Annotation包含了一个value的成员变量，该成员变量的值只能是一下的值：

|值|说明|
|------|------|
|ElementType.ANNOTATION_TYPE|指定该策略的Annotation只能修饰Annotation|
|ElementType.CONSTRUCTOR|指定该策略的Annotation只能修饰构造器|
|ElementType.FIELD|指定该策略的Annotation只能修饰成员变量|
|ElementType.LOCAL_VARIABLE|指定该策略的Annotation只能修饰局部变量|
|ElementType.METHOD|指定该策略的Annotation只能修饰方法定义|
|ElementType.PACKAGE|指定该策略的Annotation只能修饰包定义|
|ElementType.PARAMETER|指定该策略的Annotation只能修饰参数|
|ElementType.TYPR|指定该策略的Annotation可以修饰类、接口（包括注解类型）或枚举定义|

+ 使用方法：`@Target（ElementType.PARAMETER）`；

#### 使用@Documented
+ `@Documented`用于指定被该元Annotation修饰的Annotation类将被javadoc工具提取成文档，如果定义Annotation类时使用了`@Documented`修饰，则所有使用该Annotation修饰的程序元素的API文档中将会包含该Annotation说明；

#### 使用@Inherited
+ `@Inherited`元Annotation指定被他修饰的Annotation将具有继承性，如果某个类使用了`@Xxx`注解（定义该Annotation时使用了`@Inherited`修饰）修饰，则其子类将自动被`@Xxx`修饰；
