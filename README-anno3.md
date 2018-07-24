### 自定义Annotation
#### 定义Annotation
+ 定义新的Annotation类型使用`@interface`关键字：

```
public @interface Test1{}
```

+ Annotation不仅可以是简单的Annotation；还可以是带成员变量，Annotation的成员变量在Annotation定义中以无形参的方法形式来声明，其方法名和返回值定义了该成员变量的名称和类型：

```
public @interface Test1{
       String name();
       int age();
}
```

+ 一旦在Annotation里定义了成员变量之后，使用该Annotation时就应该为该Annotation的成员变量指定值：

```
public class Test{
       @Test1(name="xx" , age=6)
       public static void main(String[] args) throws IOException , ParseException{}
}
```

+ 也可以在定义Annotation的成员变量时为其指定初始值（默认值），指定成员变量的初始值可使用default关键字：

```
public @interface Test1{
       String name() default "sb";
       int age() default 32;
}
```

+ 如果为Annotation的成员变量指定了默认值，使用该Annotation时则可以不为这些成员变量指定值了；而是使用默认值；根据Annotation是否可以包含成员变量，可以把Annotation分为如下两类：一、标记Annotation：没有定义成员变量的Annotation类型被称为标记，这种Annotation仅利用自己的存在与否来提供信息，如前面的@Override等Annotation；二、元数据Annotation：包含成员变量的Annotation，因为他们可以接受更多的元数据，所以被称为元数据Annotation；

#### 提取Annotation信息
+ 使用Annotation修饰了类、方法、成员变量等成员之后，这些Annotation不会自己生效，必须由开发者提供相应的工具来提取并处理Annotation信息；AnnotationElement接口，该接口代表程序中可以接受注解的程序元素，该接口主要有如下几个实现类：

|实现类|说明|
|------|------|
|Class|类定义|
|Constructor|构造器定义|
|Filed|类的成员变量定义|
|Method|类的方法定义|
|Package|类的包定义|

+ java.lang.reflect包下主要包含一些实现反射功能的工具类；AnnotationElement接口是所有程序元素（如：Class、Filed、Method等）的父接口，所以程序通过反射获取了某个类的AnnotationElement（如：Class、Filed、Method等）对象之后，程序就可以调用该对象的如下方法来访问Annotation信息：

|方法|说明|
|------|------|
|`<A extends Annotation> A getAnnotation(Class<A> annotationClass)`|返回该程序元素上存在的、指定类型的注解，如果该类型的注解不存在，则返回null|
|`<A extends Annotation> A getDeclaredAnnotation(Class<A> annotationClass)`|这是java8新增的方法，该方法尝试获取直接修饰该程序元素、指定类型的Annotation，如果该类型的注解不存在，则返回null|
|`Annotation[] getAnnotations()`|返回该程序元素上存在的所有注解|
|`Annotation[] getDeclaredAnnotations()`|返回直接修饰该程序元素的所有Annotation|
|`boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)`|判断该程序元素上是否存在指定类型的注解，如果存在返回true，否则返回false|
|`< A extends Annotation> A[] getAnnotationByType(Class<A> annotationClass )`|该方法的功能与前面介绍的getAnnotations方法基本相似，但由于java8增加了重复注解功能，因此需要使用该方法获取修饰该程序元素、指定类型的多个Annotation|
|`<A extends Annotation> A[] getDeclaredAnnotationsByType(Class<A> annotationClass)`|该方法的功能与前面介绍的getDeclaredAnnotations方法基本相似，但由于java8增加了重复注解功能，因此需要使用该方法获取直接修饰该程序元素、指定类型的多个Annotation|

#### Java8新增的Type Annotation
+ java8为`ElementType枚举`增加了`TYPE_PARAMETER、TYPE_USE`两个枚举，这样就允许定义枚举时使用@Target(ElementType.TYPE_USE)修饰，这种注解被成为Type Annotation类型注解，Type Annotation也用在任何用到类型的地方；java8之前，只能定义各种程序元素（定义类、接口、方法、成员变量等）时使用注解；从java8开始，Type Annotation可以在任何用到类型的地方使用：如：

|位置|
|------|
|创建对象（用new关键字创建）|
|类型转换|
|使用implements实现接口|
|使用throws声明抛出异常|

```
@NotNull
public class MyClass implements @NotNull Serializable{
       public static void main(String[] args) throws @NotNull FileNotFoundException{
              String str = (@NotNull String)obj;
              Object win = new @NotNull Button();
       }
       public void Fol(List<@NotNull String> info){}
}
```
