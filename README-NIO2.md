### Java7的NIO2

|Path、Paths、和Files核心API|
|------|
|Path接口代表一个平台无关的平台路径|
|Paths包含了两个返回Path的静态工厂方法|
|Files包含了大量的静态的工具方法来操作文件|

+ Paths提供的get(String first , String second ,String.....)方法来获取Path对象，Paths会将给定的多个参数连成路径如：Paths.get("g:" , "public", "codes")就返回g:\public\codes路径；

### 使用FileVistor遍历文件和目录
+ Files类提供了如下两个方法来遍历文件和子目录：

|方法|说明|
|------|------|
|walkFIleTree(Path start , FileVisitor< ? super Path> vistor)|遍历start路径下的所有文件和子目录|
|walkFIleTree(Path start , Set< FileVisitOption> options，int maxDepth,FileVistor<? super Path> vistor)|与上一个方法的功能类似，该方法最多遍历maxDepth深度的文件|

+ FileVisitor代表一个文件访问器，walkFIleTree方法会自动遍历start路径下的所有文件和子目录，遍历文件和子目录会触发FileVisitor中的相应方法，FileVisitor中定义的如下4个方法：

|方法|说明|
|------|------|
|FileVisitResult postVisitDirectory(T dir , IOException e)|访问子目录之后触发该方法|
|FileVisitResult preVisitDirectory(T dir , BasicFileAttributes attr)|访问子目录之前触发该方法|
|FileVisitResult VisitFile(T file , BasicFileAttributes attr)|访问文件时触发该方法|
|FileVisitResult VisitFileFailed(T file ,  IOException e)|访问文件失败时触发该方法|

+ 上面的4个方法都返回FileVisitResult对象，它是一个枚举类，代表访问之后的后续行为：

|枚举值|说明|
|------|------|
|CONTINUE|代表继续访问的后续行为|
|SKIP_SIBLINGS|代表继续访问的后续行为，但不访问该文件或目录的兄弟文件或目录|
|SKIP_SUBTREE|代表继续访问的后续行为，但是不访问该文件或目录的子目录树|
|TERMUNATE|代表中止访问的后续行为|

### 使用WatchService监控文件变化

+ NIO2的Path类提供了如下方法来监控文件系统的变化：

|方法|说明|
|------|------|
|register(WatchService watcher，WatchEvent.Kind<?>.... events)|用watcher监听该path代表的目录下的文件变化，events参数指定要监听哪些类型的事件|

+ 一旦使用register方法完成注册之后，接下来就可调用WatchService的如下三个方法来获取被监听目录的文件变化事件：

|方法|说明|
|------|------|
|WatchKey poll()|获取下一个WatchKey，如果没有WatchKey发生就立即返回null|
|WatchKey poll(long timeout , TimeUnit unit)|尝试等待timeout时间去获取下一个WatchKey|
|WatchKey take()|获取下一个WatchKey，如果没有WatchKey发生就一直等待|

### 访问文件属性

+ java7的NIO2在java.nio.file.attribute包下提供了大量的工具类可以非常简单的读取、修改文件属性，这些工具类主要分为如下两类：

|类|说明|
|------|------|
|XxxAttributeView|代表某种文件属性的试图|
|XxxAttribute|代表某种文件属性的集合，程序一般通过XxxAttributeView对象来获取XxxAttribute|

#### 相关的XxxAttributeView类

|类|说明|
|------|------|
|AclFileAttributeView|通过AclFileAttributeView，开发者可以为特定的文件设置ACL及文件所有者属性，他的getAcl()方法返回List<AclEntry>对象，该返回值代表了该文件的权限集，通过setAcl()方法可以修改该文件的ACL|
|BasicFileAttributeView|它可以获取或修改文件的基本属性，包括文件的最后修改时间，最后访问时间，创建时间，大小，是否为目录、是否为符号链接等，它的readAttribute()方法返回一个BasicFileAttributes对象，对文件夹基本属性的修改是通过BasicFileAttributes来完成的|
|DosFileAttributeView|它主要用于获取或修改文件DOS相关属性，比如文件是否只读，是否隐藏，是否位系统文件，是否为归档文件等，他的readAttribute()返回一个DosFileAttributes对象，对这些属性的修改其实是由DosFileAttributes对象完成的|
|FileOwnerAttributeView|他主要用于获取或修改文件的所有者，它的getOwner()方法返回一个UserPrincipal对象来代表文件所有者，也可调用setOwner()方法来改变文件的所有者|
|UserDefineFileAttributeView|它可以让开发者为文件设置一些自定义属性|
