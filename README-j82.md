### Java8新增的Lambda表达式
+ Lambda表达式支持将代码块作为方法参数，Lambda表达式允许使用更简洁的代码来创建只有一个抽象方法的接口（这种接口被称为函数式接口）的实例；
![image](https://github.com/ningbaoqi/Java/blob/master/gif/pic-29.jpg) 
### Lambda表达式和函数式接口
+ 函数式接口代表只含有一个抽象方法的接口；但是可以有多个默认方法，类方法；
```
Runnable r = () -> {//可以进行赋值
       for(int i = 0 ; i < 100 ; i++){
              System.out.println("sb");
       }
}
Object ob = (Runnable)() -> {for(int i = 0 ; i < 100 ; i++){System.out.println("sb");}}//需要进行强制类型转换才能赋值
```
+ Lambda表达式的目标类型必须是明确的函数式接口；Lambda表达式只能为函数式接口创建对象；Lambda只能实现一个方法；
