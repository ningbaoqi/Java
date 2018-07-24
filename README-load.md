### 类加载器
+ `类加载器负责将class文件（可能是磁盘上，也可能是网络上的）加载到内存中`，`并为之生成对应的java.lang.Class对象`；
#### 类加载器简介
+ `类加载器负责加载所有的类，系统为所有被加载到内存中的类生成一个java.lang.Class实例，一旦一个类被加载到JVM中，同一个类就不会被再次加载了`；

|当JVM启动时会形成由三个类加载器组成的初始类加载器层次结构|说明|
|------|-------|
|`Bootstrap ClassLoader根类加载器`|被称为`引导类加载器`，`负责加载java的核心类`，`根加载器非常特殊，并不是java.lang.ClassLoader的子类，而是由JVM自身实现的`|
|`Extension ClassLoader扩展类加载器`|`负责加载JRE的扩展目录（%JAVA_HOME%/jre/lib/ext或者由java.ext.dirs系统属性指定的目录）中JAR包的类`，`通过这种方式就可以为java扩展核心类以外的新功能，只要把自己开发的类打包成JAR文件然后放在%JAVA_HOME%/jre/lib/ext路径下即可`|
|`System ClassLoader系统（应用）类加载器`|`它负责在JVM启动时加载来自java命令的-classpath选项、java.class.path系统属性或CLASSPATH环境变量所指定的JAR包和类路径，程序可以通过ClassLoader的静态方法getSystemClassLoader()来获取系统类加载器，如果没有特别指定，则用户自定义的类加载器都以该类加载器作为父加载器`|

#### 类加载机制

|JVM的类加载机制主要有如下三种|说明|
|------|------|
|`全盘负责`|所谓全盘负责，`就是当一个类加载器负责加载某个Class时，该Class所依赖的和引用的其他Class也将由该类加载器负责载入，除非显式使用另外一个类加载器来载入`|
|`父类委托`|所谓父类委托，`是先让父类加载器试图加载该Class，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类`|
|`缓存机制`|缓存机制`将会保证所有加载过的Class都会被缓存`，`当程序中需要使用某个Class时，类加载器先从缓存区中搜寻该Class，只有当缓存区中不存在该Class对象时，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓存区中`，这就是为什么修改了Class后，必须重新启动JVM，程序所做的修改才会生效的原因|

+ `自定义的类加载器通过继承ClassLoader来实现`；

|类加载器加载Class大致要经过如下8个步骤|
|------|
|`检测此Class是否载入过（即在缓存区中是否有此Class），如果有则直接进入第8步，否则接着执行第2步`|
|`如果父类加载器不存在（如果没有父类加载器，则要么parent一定是根类加载器，要么本身就是根类加载器）则跳到第四步执行，如果父类加载器存在，则执行第三步`|
|`请求使用父类加载器去载入目标类，如果成功载入则跳到第八步，否则接着执行第5步`|
|`请求使用根类加载器来载入目标类，如果成功载入则跳到第8步，否则跳到第7步`|
|`当前类加载器尝试寻找Class文件，如果找到则执行第6步，如果找不到跳到第7步`|
|`从文件中载入Class，成功载入则跳到第8步`|
|`抛出ClassNotFoundException异常`|
|`返回对应的java.lang.Class对象`|

#### 创建并使用自定义的类加载器

+ `开发者可以通过扩展ClassLoader的子类，并重写该ClassLoader所包含的方法来实现自定义的类加载器`；

|ClassLoader类有如下两个关键方法|说明|
|------|------|
|`loadClass(String name, boolean resolve)`|该方法为ClassLoader的入口点，根据指定名称来加载类，系统就是调用ClassLoader的该方法来获取指定类对应的Class对象|
|`findClass(String name)`|根据指定名称来查找类|

+ `通常推荐重写findClass方法，而不重写loadClass方法`，loadClass方法的执行步骤如下：

|步骤|
|------|
|`用findLoadedClass(String)来检查是否已经加载类，如果已经加载则直接返回`|
|`在父类加载器上调用loadClass()方法，如果父类加载器为null，则使用根类加载器来加载`|
|`调用findClass(String)方法查找类`|

+ 在ClassLoader里还有一个核心方法，`Class defineClass(String name, byte[] a,int off , int len)该方法负责将指定类的字节码文件（即class文件），读入字节数组byte[] a中`，`并把它转换为Class对象`，`该字节码文件可以来源于文件、网络等`；除此之外，ClassLoader里包含如下一些普通方法：

|方法|说明|
|------|------|
|`findSystemClass(String name)`|`从本地文件系统装入文件，它在本地文件系统中寻找类文件，如果存在，就使用defineClass()方法将原始字节转换成Class对象，以将该文件转换成类`|
|`static getSystemClassLoader()`|`这是静态方法，用于返回系统类加载器`|
|`getParent()`|`获取该类加载器的父类加载器`|
|`resolveClass(Class<?>c)`|`链接指定的类，类加载器可以使用此方法来链接类c，读者无需理会该方法的太多细节`|
|`findLoadedClass(String name)`|`如果此java虚拟机已加载了名为name的类，则直接返回该类对应的Class实例，否则返回null，该方法是Java类加载缓存机制的体现`|

+ 实际上，使用自定义的类加载器，可以实现如下常见功能：

|方法|
|------|
|`执行代码前自动验证数字签名`|
|`根据用户提供的密码解密代码，从而可以实现代码混淆器来避免反编译*.class文件`|
|`根据用户需求来动态的加载类`|
|`根据应用需求把其他数据以字节码的形式加载到应用中`|

#### URLClassLoader类

+ `URLClassLoader功能比较强大，他既可以从本地文件系统获取二进制文件来加载类，也可以从远程主机获取二进制文件来加载类`；

|URLClassLoader类提供了如下两个构造器|说明|
|------|------|
|`URLClassLoader(URL urls)`|`使用默认的父类加载器创建一个ClassLoader对象，该对象将从urls所指定的系列路径来查询并加载类`|
|`URLClassLoader(URL[] urls,ClassLoader parent)`|`使用指定的父类加载器创建一个ClassLoader对象，其他功能与前一个构造器相同`|

+ `一旦得到了URLClassLoader对象之后，就可以调用该对象的loadClass()方法来加载指定类`；
