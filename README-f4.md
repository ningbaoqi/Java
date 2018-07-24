### 反射和泛型
+ `从JDK5以后，java的Class类增加了泛型功能，从而允许使用泛型来限制Class类，如String.class的实际类型是Class<String>。如果Class对应的类暂时未知，则是有Class<?>。通过在反射中使用泛型，可以避免使用反射生成的对象需要强制类型转换`；
#### 使用反射来获取泛型信息
+ `为了获取指定成员变量的泛型类型，应先使用如下方法来获取该成员变量的泛型类型`：

```
//获取成员变量f的泛型类型
Type gType = f.getGenericType();
```

+ `然后将Type对象强制类型转换为ParameterizedType对象，ParameterizedType代表被参数化的类型，也就是增加了泛型限制的类型`，ParameterizedType提供了如下两个方法：

|方法|说明|
|------|-------|
|`getRawType()`|`返回没有泛型信息的原始类型`|
|`getActualTypeArguments()`|`返回泛型参数的类型`|

```
public class Common {
    private Map<String, Integer> score;

    public static void main(String[] agrs) throws NoSuchFieldException {
        Class<Common> clazz = Common.class;
        Field field = clazz.getDeclaredField("score");
        Class<?> a = field.getType();
        System.out.print("score的类型是" + a);//输出interface java.util.Map

        Type type = field.getGenericType();
        if (type instanceof ParameterizedType) {
            ParameterizedType type1 = (ParameterizedType) type;
            Type type2 = type1.getRawType();
            System.out.print(type2);//输出interface java.util.Map

            Type[] types = type1.getActualTypeArguments();
            for (Type t : types) {
                System.out.print(t);//输出Class java.lang.String 与Class java.lang.Integer
            }
        }
    }
}
```
