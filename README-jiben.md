### 基本类型建议
#### 覆写变长方法也循规蹈矩
+ `参数列表必须与被重写方法相同`；`覆写的方法参数与父类相同，不仅仅是类型，数量，还包括显示形式；如变参`；
#### 用偶判断，不用奇判断
+ `因为Java的求余机制是余数等不等于1，如果等于1就是奇数，不是1就是偶数`；`所以应该使用偶数判断i%2==0?"偶数":"奇数"`；
#### 优先选择基本类型
+ `test.f(i)`是因为`自动装箱有一个重要的原则`：`基本类型可以先加宽，再转变成宽类型的包装类型，而不能直接转变为宽类型的包装类型`，也就是说：`int可以加宽转变为long，然后再转变为Long对象，但不能直接转变为包装类型`，注意这里指的是自动转换，不是通过构造函数生成；`test.f(Integer.valueOf(i))`是因为`Integer.valueOf(i)`返回的是Integer对象，，但是Integer和int可以互相转换，没有`f(Integer i)`方法，没关系，编译器会尝试转换成int类型的实参调用；整个过程为：生成一个Integer对象，由于没有`f(Integer i)`方法，编译器就把Integer对象转变为了int，int自然拓展为long；重申：`基本类型优先考虑`；
#### 不要随意设置随机种子
+ `在java中，随机数的产生取决与种子，随机数和种子之间的关系是：种子不同，产生不同的随机数，种子相同，即使实例不同也产生相同的随机数`；`所以若非必要，不要设置随机数种子，在设置随机数种子时，一般使用Random random = new Random(System.currentTimeMillis())`；
#### 用整数类型处理货币
```
public class Test {
    public static void main(String[] args) {
        System.out.print(10.00-9.60);//将输出0.4000000000000000036
    }
}
```
+ `在计算机中浮点数有可能是不准确的，它只能无限接近准确值，而不能完全精确，这是由于浮点数的存储规则所决定的`；
```
public class Test {
    public static void main(String[] args) {
        NumberFormat format = new DecimalFormat("#.##");
        System.out.print(format.format(10.00 - 9.60));//将输出0.4
    }
}
```

+ `这样看似解决了问题，但是隐藏一个很深的问题，就是在进行大规模计算的时候，会出现不准确的问题`；

|真正解决上面问题的方法|
|------|
|`使用BigDecimal，它是专门为弥补浮点数无法精确计算的缺陷而设计的类，并且它本身也提供了加减乘除的常用数学算法`|
|`使用整形，把参与计算的值扩大100倍，并转变为整型，然后在展现时再缩小100倍，这样处理的好处是计算简单，精确，一般在非金融行业应用较多`|
#### 不要让四舍五入亏了一方
```
public class Test {
    public static void main(String[] args) {
        System.out.print("10.5近似值：" + Math.round(10.5));//将输出11
        System.out.print("-10.5近似值：" + Math.round(-10.5));//将输出-10
    }
}
```
+ 以上是`因为Math.round采用的舍入规则所决定的（采用的是正无穷方向舍入规则）`；

|银行家舍入的近似算法规则|
|------|
|`舍去位的数值小于5时，直接舍去`|
|`舍去位的数值大于等于6时，进位后舍去`|
|`当舍去位的数值等于5时，分两种情况：5后面还有其他数字（非零）则进位后舍去，若5后面是0（即5是最后一个数字）则根据5前一位数字的奇偶性判断是否需要进位，奇数进位，偶数舍去`|
+ `要在Java5以上的版本中使用银行家算法直接使用RoundingMode类提供的Round模式即可`；
```
public class Test {
    public static void main(String[] args) {
        BigDecimal d = new BigDecimal(888888);
        BigDecimal r = new BigDecimal(0.001875 * 3);
        BigDecimal i = d.multiply(r).setScale(2, RoundingMode.HALF_EVEN);
        System.out.print(i);//将输出4999.99
    }
}
```

|RoundingMode提供的舍入方式|说明|
|------|------|
|`ROUND_UP`|远离0方向舍入|
|`ROUND_DOWN`|趋向0方向舍入|
|`ROUND_CEILING`|向正无穷方向舍入|
|`ROUND_FLOOR`|向负无穷方向舍入|
|`HALP_UP`|最近数字舍入（5进）|
|`HALP_DOWN`|最近数字舍入（5舍）|
|`HALP_EVENT`|银行家算法|
#### 优先使用整型池
```
public class Test {
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        while (input.hasNextInt()) {
            int li = input.nextInt();
            System.out.print("\n==" + li + "的相等判断");
            Integer i = new Integer(li);
            Integer j = new Integer(li);
            System.out.print(i==j);//输入127输出false；输入128输出false
            j = li;
            i = li;
            System.out.print(i==j);//输入127输出true；输入128输出false
            i = Integer.valueOf(li);
            j = Integer.valueOf(li);
            System.out.print(i==j);//输入127输出true；输入128输出false
        }
    }
}
```
+ `因为new声明的就是要生成一个新的对象，所以是两个对象，地址肯定不等，比较结果就是false`；`装箱生成的对象：首先要说明的是装箱动作是通过valueOf方法实现的，也就是说后面两个算法相同，那结果也是一样的，valueOf方法是如何生成对象的呢`？
```
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```
+ `在-128到127之间的int类型转换为Integer对象，则直接从cache数组中获得，cache是IntegerCache内部类的一个静态数组，容纳的是-128到127之间的Integer对象`，`通过valueOf方法产生包装对象时，如果int参数在-128到127之间，则直接从整型池中获取对象，不在该范围内的int类型则通过new生成包装对象`；`整型池的存在不仅仅提高了系统性能，同时也节省了内存空间，这也是使用整型池的原因`，`也就是在声明包装对象的时候使用valueOf方法而不是通过构造函数来生成的原因；通过包装类的valueOf生成包装实例可以显著提高空间和时间性能`；
