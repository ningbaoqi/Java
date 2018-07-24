### 枚举类
```
public enum EnumTest{
       SPRING , SUMMER , FALL , WINTER;
}
```

+ 枚举类默认提供了一个values()方法，该方法可以方便遍历所有的枚举值；

|方法|说明|
|------|-------|
|int compareTo(E o)|指定枚举对象比较顺序，同一个枚举实例只能与相同类型的枚举实例进行比较，如果该枚举对象位于指定枚举对象之后，返回正数；之前返回负数，否则返回0|
| String name()|返回枚举实例的名称，这个名称就是定义枚举类时列出的所有枚举值之一|
| int ordinal()|返回枚举值在枚举中的索引|
| String toString()|返回枚举常量的名称|
| `public static <T extends Enum<T>>T valueOf(Class<T>enumType ,String name)`|返回指定枚举类中指定名称的枚举值，名称必须与在该枚举类中声明枚举值时所用的标识符完全匹配，不可以使用额外的空白字符|
|Color.Blue.toString()|输出Blue|
|valueOf("Blue")|输出Color.Blue|

#### 枚举建议--枚举项的数量限制在64个以内
+ 枚举项的数量不要超过64，否则建议拆分；
#### 枚举建议--推荐使用枚举定义常量
+ 在1.5版本以后改进了枚举，即`新增了一种常量声明方式，枚举声明常量`；

```
public class Test {
    public static void main(String[] str) {
        for (Season s : Season.values()) {//通过Values方法获取所有的枚举值
            System.out.print(s);
        }
        System.out.print(Season.getComfortableSeason());
        description(Season.SPRING);
    }

    public static void description(Season season) {
        switch (season) {
            case SPRING:
                break;
            case SUMMER:
                break;
            case AUTUMN:
                break;
            case WHINTER:
                break;
        }
    }
}

enum Season {
    SPRING , SUMMER , AUTUMN , WHINTER;

    public static Season getComfortableSeason() {//获取最舒服的季节
        return SPRING;
    }
}
```

|枚举的优点主要体现|
|------|
|枚举常量更简单|
|枚举常量属于稳态型|
|枚举具有内置方法|
|枚举可以自定义方法|

+ `枚举类型不能有继承，在项目开发中，推荐使用枚举常量代替接口常量或类常量`；

#### 枚举建议--使用构造函数协助描述枚举项

+ `通过枚举的构造函数，声明每个枚举项（也就是枚举的实例）必须具有的属性和行为，这是对枚举项的描述和补充，目的是使枚举项表述的意义更加清晰明确`；

```
public class Test {
    public static void main(String[] str) {
        Season.SPRING.getDesc();
    }
}

enum Season {
    SPRING("春") , SUMMER("夏") , AUTUMN("秋") , WHINTER("冬");
    private String desc;
    Season(String _desc) {
        desc = _desc;
    }
    public String getDesc() {
        return desc;
    }
}
```

+ 推荐在枚举定义中为每个枚举项定义描述，特别是在大规模的项目开发中，大量的常量项定义使用枚举项描述比在接口常量或类常量中增加注释的方式友好很多，简洁很多；

#### 枚举建议--使用valueOf前必须进行校验

```
public class Test {
    public static void main(String[] str) {
        List<String> params = Arrays.asList("DEBUG", "info");
        for (String s : params) {
            Season sea = Season.valueOf(s);
            if (s != null) {
                System.out.print(sea);
            } else {
                System.out.print("无相关枚举项");
            }
        }
    }
}
//将会报出非法参数异常
enum Season {
    DEBUG, INFO, WARN, ERROR;
}
```

+ `valueOf方法会把一个String 类型的名称转变为枚举项，也就是在枚举项中查找出字面值与该参数相等的枚举项；因为大小写的问题，找不到，所以在执行该函数的时候，出现了非法参数异常，所以在使用该方法的时候，可以加上一个判断，然后再决定是不是执行该方法`；

#### 枚举建议--在switch的default代码块中增加AssertionError错误

```
public class Test {
    public static void main(String[] str) {
        sb(Season.DEBUG);
    }

    public static void sb(Season loglevel) {
        switch (loglevel) {
            case DEBUG:
                break;
            case INFO:
                break;
            case WARN:
                break;
            case ERROR:
                break;
        }
    }
}

enum Season {
    DEBUG, INFO, WARN, ERROR;
}
```

+ `如果Season新增加了一项，所以Test类的代码还是要修改，这样是不行的，为了解决这个问题，建议在default后直接抛出一个AssertionError错误，这样就可以保证在增加一个枚举项的情况下，若其余代码没有修改，运行期间马上就会报错，这样一来很容易找到错误，方便立刻排除`；`当然也可以采用，如Eclipse上Java->Compiler->Errors/Warnings中的Enum type constant not covered on switch设置为Error级别，如果不判断所有的枚举项就不能通过编译`；

#### 枚举建议--小心switch带来的空值异常

```
public class Common {
    public static void main(String[] args) {
        doSports(null);//将报出空指针异常
    }

    public static void doSports(Season season) {
        switch (season) {
            case AUTUMN:
                break;
            case SPRINT:
                break;
            case SUMMER:
                break;
            case WINTER:
                break;
            default:
                break;
        }
    }
}

enum Season {
    SPRINT("春"), SUMMER("夏"), AUTUMN("秋"), WINTER("冬");
    private String desc;

    Season(String _desc) {
        this.desc = _desc;
    }

    public String getDesc() {
        return desc;
    }
}
```

+ `出现了空指针异常，是因为目前switch语句只能判断byte、short、char、int类型`，判断枚举实际上运行过程是如下：

```
switch (season.ordinal()) {
    case Season.SPRINT.ordinal():
        break;
    case Season.SUMMER.ordinal():
        break;
    case Season.AUTUMN.ordinal():
        break;
    case Season.WINTER.ordinal():
        break;
    default:
        break;
}
```

+ 在我们的例子中season变量是`null`值，`无法执行ordinal方法`，`于是报出了空指针异常`；

#### 枚举建议--用枚举实现工厂方法模式更简洁
##### 枚举非静态方法实现工厂方法模式

```
public class Common {
    public static void main(String[] args) {
        Car car = CarFactory.BuickCar.create();
    }
}

enum CarFactory {
    FordCar, BuickCar;//定义工厂类能生产汽车的类型

    public Car create() {//生产汽车
        switch (this) {
            case FordCar:
                return new FordCar();
            case BuickCar:
                return new BuickCar();
            default:
                throw new AssertionError("无效参数");
        }
    }
}

interface Car {
}

class FordCar implements Car {

}

class BuickCar implements Car {

}
```

+ `create是一个非静态方法，也就是只有通过FordCar、BuickCar枚举项才能访问`;

##### 通过抽象方法生成产品

```
public class Common {
    public static void main(String[] args) {
        Car car = CarFactory.BuickCar.create();
    }
}

enum CarFactory {
    FordCar{
        @Override
        public Car create(){
            return new FordCar();
        }
    },
    BuickCar{
        @Override
        public Car create() {
            return new BuickCar();
        }
    };
    public abstract Car create();
}

interface Car {

}

class FordCar implements Car {

}

class BuickCar implements Car {

}
```

+ `枚举方法虽然不能继承，但是可以使用abstract修饰其方法，此时就表示该枚举是一个抽象枚举，需要每个枚举项自行实现该方法，也就是说枚举项的类型是该枚举的子类`；


|使用枚举类型的工厂方法模式的优点|
|------|
|避免错误调用的发生|
|性能好，使用便捷|
|降低类间耦合|

+ 下一次，`使用枚举类来实现工厂方法模式`；
