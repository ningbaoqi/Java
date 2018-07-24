### 线程建议---启动线程前stop方法是不可靠的
```
class Client {
    public static void main(String[] args) {
        while (true) {//部分昼夜分制造垃圾短信
            SpamMachine sm = new SpamMachine();
            if (true) {
                sm.stop();//将线程停止，
            }
            sm.start();//如果线程是stop状态，则不会启动，但是却一直在制造大量的短信
        }
    }
}

class SpamMachine extends Thread {
    @Override
    public void run() {
        super.run();
        System.out.print("大量制造短信");
    }
}
```
+ `所有的线程在启动前都执行stop方法，虽然它是一个已经过时的方法，但它的运行逻辑还是正常的，况且stop方法在此处的目的并不是停止一个线程，而是设置线程为不可启动状态，但是运行结果却出现了奇怪的现象，部分线程还是启动了，也就是在某些线程（没有规律）中的start方法正常执行了`。这是因为线程启动start方法有一个缺陷，Thread类的stop方法会根据线程状态来判断是终结线程还是设置线程为不可运行状态，对于未启动的线程（线程状态为NEW）来说，会设置其标志位为不可启动，而其他的状态则是直接停止`；以下是修改；
```
class Client {
    public static void main(String[] args) {
        while (true) {//部分昼夜分制造垃圾短信
            if (true) {
                new SpamMachine().start();
            }
        }
    }
}

class SpamMachine extends Thread {
    @Override
    public void run() {
        super.run();
        System.out.print("大量制造短信");
    }
}
```
+ `不再使用stop方法进行状态的设置`，直接通过判断条件来决定线程是否可启动，对于start方法的该缺陷，一般不会引起太大的问题，只是增加了线程启动和停止的精度而已；
