### File类
+ 在java.io包下主要包含了输入、输出两种IO流，每种输入输出流有被分为字节流和字符流两大类：其中字节流以字节为单位来处理输入、输出操作，而字符流则以字符来处理输入、输出操作；除此之外，java的IO流使用了一种装饰器设计模式，它将IO流分为底层节点流和上层处理流，其中节点流用于和底层的物理存储节点直接关联；不同的物理节点获取节点流的方式可能存在一定的差异，但程序可以把不同的物理节点流包装成统一的处理流，从而允许程序使用统一的输入、输出代码来读取不同的物理存储节点的资源；如果希望在程序中操作文件和目录，都可以使用File类来完成，值得指出的是，不管文件还是目录都是使用File操作的，File能新建、删除、重命名文件和目录。File不能访问文件内容本身，如果希望访问文件内容本身，则需要使用输入、输出流；
### 访问文件和目录
+ File类可以使用文件路径字符串来创建File实例，该文件路径字符串既可以是绝对路径，也可以是相对路径，在默认情况下，系统总是依据用户的工作路径来解释相对路径，这个路径由系统属性`user.dir`指定，通常也就是运行java虚拟机时所在的路径；File类提供了很多方法来操作文件和目录：如下：
#### 访问文件名相关的方法

|方法|说明|
|------|------|
|String getName()|返回此file对象所表示的文件名或路径名（如果是路径，则返回最后一级子路径名）|
|String getPath()|返回此File对象所对应的路径名|
|File getAbsoluteFile()|返回此File对象的绝对路径|
|String getAbsolutePath()|返回此File对象所对应的绝对路径名|
|String getParent()|返回此对象所对应目录（最后一级目录）的父目录名|
|boolean renameTo(File newName)|重命名该Ｆile对象所对应的文件或目录，如果重命名成功则返回true，否则返回false|

#### 文件检测相关的方法

|方法|说明|
|------|------|
|boolean exists()|判断Ｆile对象所对应的文件或目录是否存在|
|boolean canWrite()|判断File对象所对应的文件或目录是否可写|
|boolean canRead()|判断File对象所对应的文件或目录是否可读|
|boolean isFile()|判断File对象所对应的是否是文件，而不是目录|
|boolean isDirectory()|判断File对象所对应的是否是目录，而不是文件|
|boolean isAbsolute()|判断File对象所对应的文件或目录是否是绝对路径，该方法消除了不同平台的差异，可以直接判断File对象是否为绝对路径。在其他系统上，如果路径名开头为一条斜线/则表明该File对象对应一个绝对路径，在Ｗindows系统上，如果路径开头是盘符，则说明它是一个绝对路径|

#### 获取常规文件信息

|方法|说明|
|------|------|
|long lastModified()|返回文件的最后修改时间|
|long length()|返回文件内容的长度|

#### 文件操作相关的方法

|方法|说明|
|-------|------|
|boolean createNewFile()|当此File对象所对应的文件不存在时，该方法将新建一个该File对象所指定的新文件，如果创建成功则返回true，否则返回false|
|boolean delete()|删除File对象所对应的文件或路径|
|static File createTempFile(String prefix , String suffix)|在默认的临时文件目录中创建一个临时空文件，使用给定的前缀、系统生成的随机数和给定后缀作为文件名，这是一个静态方法；prefix参数必须至少三个字节长，建议前缀使用一个短的、有意义的字符串，如：hjb、mail、等；suffix参数可以为null，在这种情况下，将使用默认的后缀.tmp|
|static File createTempFile(String prefix , String suffix , File directory)|在directory所指定的目录中创建一个临时文件，使用给定前缀、系统生成的随机数和给定后缀作为文件名|
|void deleteOnExit()|注册一个删除钩子，指定当java虚拟机退出时，删除File对象所对应的文件和，目录|

#### 目录操作相关的方法

|方法|说明|
|------|------|
|boolean mkdir()|试图创建一个File对象对应的目录，如果创建成功，则返回true，否则返回false，调用该方法时File对象必须对应一个路径，而不是一个文件|
|String[] list()|列出File对象的所有子文件名和路径名，返回String数组|
|File[] listFiles()|列出File对象的所有子文件和路径|
|static File[] listRoots()|列出系统所有的根目录|

### 文件过滤器
+ 在File类的list()方法中可以接收一个FilenameFilter参数，通过该参数可以只列出符合条件的文件，FilenameFilter接口里包含了一个accept(File dir , String name)方法，该方法将依次对指定File的所有子目录或者文件进行迭代，如果该方法返回true，则list()方法会列出该子目录或者文件；
