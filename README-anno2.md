### 编译时处理Annotation
+ `APT（Annotation Processing Tool）`是一种注解处理工具，他对源代码文件进行检测，并找出原文件所包含的Annotation信息，然后针对该Annotation信息进行额外的处理；APT工具就可以根据该Annotation生成另一个XML文件，这就是Annotation的作用；
### 小心注解继承
+ Java从1.5版本开始引入了注解（Annotation），其目的是在不影响代码语义的情况下增强代码的可读性，并且不改变代码的执行逻辑；

```
class Test {
    public static void main(String[] args) {
        Bird bird = BirdNest.Sparrow.reproduct();
        Desc.Color color = bird.getColor();
    }
}

@Retention(RetentionPolicy.RUNTIME)//表示的是该注解的保留级别
@Target(ElementType.TYPE)//表示该注解可以标注在什么地方
@Inherited//表示该注解会被自动继承
@interface Desc {
    enum Color {WHITE, YELLOW, BLUE}

    Color c() default Color.WHITE;
}
@Desc(c = Desc.Color.BLUE)
abstract class Bird {
    public abstract Desc.Color getColor();
}

class Sparrow extends Bird {
    private Desc.Color color;

    public Sparrow() {
        color = Desc.Color.BLUE;
    }

    public Sparrow(Desc.Color _color) {
        color = _color;
    }
    @Override
    public Desc.Color getColor() {
        return color;
    }
}

enum BirdNest {
    Sparrow;
    public Bird reproduct(){
        Desc bd = Sparrow.class.getAnnotation(Desc.class);
        return bd == null ? new Sparrow() : new Sparrow(bd.c());
    }
}
```

 + @Inherited注解，它表示的意思是我们只要把注解@Desc加到父类Bird上，它的所有子类都会自动从父类继承@Desc注解，不需要显式声明；

### 注意@Override不同版本的区别
+ @Override不同版本有区别，如果是Java1.6版本的程序移植到1.5版本环境中，就需要删除实现接口方法上的@Override注解；
### 枚举和注解结合使用威力更大

```
class Test {
    public static void main(String[] args) {
        Foo b = new Foo();//初始化商业逻辑
        Access access = b.getClass().getAnnotation(Access.class);//获取注解
        if (access == null || !access.level().identify()) {//如果没有Access注解或者鉴权失败
            System.out.print(Identifier.REFUSE_WORD);
        }
    }
}

interface Identifier {//鉴权人接口，定义了一个常量和一个鉴权方法
    String REFUSE_WORD = "您无法访问";//无权访问时的礼貌语
    public boolean identify();//鉴权
}

enum CommonIdentifier implements Identifier {//定义一个通用鉴权者，使用的是枚举类型，并且实现了鉴权者接口
    Reader , Author , Admin;//权限级别
    @Override
    public boolean identify() {//实现鉴权
        return false;
    }
}
//定义出权限级别的注解，该注解是标注在类上的，并且会保留到运行期
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Access {
    CommonIdentifier level() default CommonIdentifier.Admin;//什么级别可以访问，默认是管理员
}

@Access(level = CommonIdentifier.Author)
class Foo {}
```
