### 使用jar文件
+ jar文件就是java归档文件，通常jar文件是一种压缩文件，也称为jar包，jar文件与zip文件的区别就是jar文件中默认包含了一个名为META-INF/MANIFEST.MF的清单文件，这个清单文件是在生成jar文件时由系统自动创建的；当开发一个应用程序后，这个应用程序包含了很多的类，如果需要把这个应用程序提供给别人使用，通常会将这些类文件打包成jar文件，把jar文件提供给别人使用，只要别人在系统的CLASSPATH环境变量中添加了这个jar文件，则java虚拟机就自动在内存中解压这个jar文件，把这个jar文件当成一个路径，在这个路径中查找所需要的类或包层次对应的路径结构；jar文件通常使用jar命令压缩而成，当使用jar命令压缩生成jar文件时，可以把一个或多个路径全部压缩成一个jar文件；

#### jar命令详解

|jar命令|说明|
|------|------|
|创建jar文件：jar cf test.jar test|该命令没有显示压缩过程，执行结果是将当前路径下的test路径下的全部内容生成一个test.jar文件，如果当前目录中已经存在test.jar文件，那么该文件将会被覆盖|
|创建jar文件，并显示压缩过程：jar cvf test.jar test||
|不使用清单文件：jar cvfM test.jar test|其中M选项表明不生成清单文件，因此生成的test.jar文件中没有包含清单文件|
|自定义清单文件内容：jar cvfm test.jar manifest.mf test| 其中m选项指定读取用户清单文件信息，因此在生成jar包中的 清单文件中的内容有所不同，他会在原有清单文件基础上增加MANIFEST.MF文件的内容；当开发者向MANIFEST.MF清单文件增加自己的内容时，就需要借助自己的清单文件了，清单文件只是一个普通的文本文件，使用记事本编辑即可；清单文件的内容格式为：`key:<空格>value`；（1）每行只能定义一个key-value对，每行的key-value对之前不能有空格，即key-value对必须顶格写（2）每组key-value对之间以：分隔，少写冒号或者空格都是错误的（3）文件开头不能有空行（4）文件必须以一个空行结束；如将文件保存到当前路径下，文件名为a.txt，使用命令即可将清单文件中的key-value对提取到META-INF/MANIFEST.MF文件中：jar cvfm test.jar a.txt test|
|查看jar文件内容：jar tf test.jar||
|查看jar包详细内容：jar rvf test.jar|
|解压缩到当前目录下：jar xf test.jar||
|带提示信息解压缩：jar xvf test.jar||
|更新jar文件：jar uf test.jar Hello.class|更新test.jar中的Hello.class文件，如果原来有这个文件则把新文件替换原来的文件；如果没有这个文件，就把新文件添加到test.jar文件中|
|更新时显示详细信息：jar uvf test.jar Hello.class||
|解压jar包：unzip test.jar -d test/||

#### 创建可执行的jar包

+ 当一个应用程序开发成功后，大致分为三种发布方式：

|方式|
|------|
|使用平台相关的编译器将整个应用编译成平台相关的可执行文件，这种方式常常需要第三方编译器的支持，而且编译生成的可执行文件丧失了跨平台特性，甚至可能有一定的性能下降|
|为应用编译一个批处理文件以window为例，被处理文件中只需要定义如下命令：java package.MainClass；当用户单击上面的被处理文件时，系统将执行被处理文件的java命令，从而运行程序的主类；如果不想保留运行java程序的命令行窗口可以使用：start javaw package.MainClass|
|将一个应用程序制作成可执行的jar文件，通过jar包来发布应用程序；创建可执行的jar包的关键在于：让javaw命令知道jar包中的哪个类是主类；javaw命令可以通过运行该主类来运行程序；jar命令的-e选项指定jar包中作为程序入口的主类的类名，因此，制作一个可执行的jar包只要增加-e选项即可：jar cvfe test.jar test.Test test：该命令把test目录下的所有文件都压缩到test.jar包中，并指定了使用test.Test类为程序的入口；运行上面的jar包有两种方式：使用java命令，使用java运行时语法是：java -jar test.jar；使用javaw命令：javaw test.jar|
