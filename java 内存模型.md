### java 内存模型

1. 线程共享
   1. java heap
   2. 方法区
2. 线程独享
   1. 虚拟机栈
   2. 本地方法栈
   3. 程序计数器

   
### java对象的保存
1. 对象的引用保存在java栈的本地变量表
2. 实例化对象 保存在java 堆，
3. 实例化对象的地址信息 保存在方法区(对象类型，父类，实现的方法接口)


### 初始化
1. 类初始化，父类全要初始化
2. 接口初始化，只有用到接口的相对应的常量时 才需要去初始化


### 类的生命周期
1. :加载、验证、准备、解析、初始 化、使用和卸载
2.  绑定(把方法的调用和方法所在主体关联起来)
   1. 静态绑定：程序编译器绑定 static final相关
   2. 运行时 绑定 运行时根据对象的类型进行绑定
3. 加载
 	1. 加载时需要完成的三件事情
 	 	* 通过类的名称来获取类的二进制字节流 	 	* 讲字节流所代表的静态数据结构转化为运行时的动态的数据结构
 	 	* 在java堆中生成相对应的class对象，作为对方法区数据访问的接口
4. 启动器
   1. 启动类启动器
   2. 扩展类启动器
   3. 应用程序类启动器
5. 类加载流程
   
   1.   当类加载器收到一个加载请求时，它自己不会去加载，而是把请求委托给父类去加载，所有的加载类请求都会传到启动类加载器中，当父加载器未能在其范围内找到需要加载的类，才会让子加载器去加载
   2.  双亲模型的好处，使java具有优先级的层次感，且保证加载的是同一个类
6.  验证
   1. 文件格式验证
   2. 元数据验证
   3. 字节码验证
   4. 符号引用验证
7. 准备
    准备阶段主要 为类变量分配内存，内存分配都在方法区，
    
    1. 此时分配的变量仅限于类变量(static),不包括实例变量，实例变量会随着变量实例化，在java堆中实例化
    2. 这是设置的变量初始值大部分是数据类型的默认值(0,false,null)，而不是在代码中被显式赋值的
    3. 对于基础数据类型(static，全局变量),如果不显式的为其赋值，系统在引用时 则为数据基本类型默认数据
    4. 针对static final 编译时必须为其进行赋值
    5. 针对引用类型 默认值为nul
    6. 数组初始化 默认值为0
    7. static final 在编译时就已经放入编译池
8. 解析
    解析主要是将常量池的符号引用转化为直接引用的过程，虚拟机会进行判断，在初始化之前进行解析，还是在初始化之后进行解析，对同一符号多次解析，针对解析结果进行缓存，主要是针对
    
    解析主要针对四类符号引用进行解析，主要是 类或接口，字段，类方法，接口方法。
    1. 类或接口的解析
        
        判断其需要转化为直接引用，或者 数组引用或者对 对象的引用，分类进行解析
    2. 字段解析

        对字段进行解析时，会首先在本类中去查找是否有简单名称和字段描述都与字段匹配的，如果有则寻找结束，若没有则去其父类和继承的接口去寻找， 顺序为 本类-> 接口-> 父接口-> 父类 ->祖父类
    3.  类方法解析
        首先去解析父类 而后是接口
    4.  接口解析
        直接去找父接口
        
9. 初始化
    初始化 是执行构造器方法的过程
    
    1. 在自雷初始化时，不需要显式的去调用父类的初始化方法，因父类已经完全初始化完成。
    2. 初始化方法在  不是必须的，若无静态代码块，无对类变量的赋值操作，则编译器不为其生成() 方法
    3. 接口中不能使用静态代码块，因为接口和类一样会生成（方法
    4. 虚拟机会保证一个类的初始化方法在多线程环境下可以正确的加锁和同步

10. 多态的实现
    1. 静态多分派 动态单分派
        方法重载 和方法复写
        
## java语法糖
1. java代码中的方法签名只包括方法参数、参数顺序、方法名称


## gc
1. 垃圾回收一般都是在java堆中进行，因为java堆中存放了java中所有对象的实例，
2. 引用
    1. 强引用：java中普遍存在的，只要在强引用 就不会被回收
    2. 软引用：当内存不够时 会被回收
    3. 弱引用：只能生存到下次gc前
    4. 虚引用：本次gc需要回收的对象

3. 垃圾对象判定
    1. 引用计数
    2. 根搜索
4. 垃圾回收算法
    1. 标记清除算法

5. 垃圾回收 (young gc full gc)
    1. 对象优先在eden中分配
    2. 大对象直接进入老年代
    3. 长期存活对象进入老年代

    
6. 性能调优
   1. 可以给java虚拟机 配置超大堆
   2. direct memory直接在堆内存外分配
   3. 线程 内存分配 -Xss stackoverflow  无法分配堆栈 OOM 无法分配内存
   4. socket换缓冲区  每个socket都有接受和发送两个缓冲区，分别占用37 25kb
   5. jni代码 如果调用了jni内存也不在虚拟机
   6. 虚拟机和gc需要运行也需要内存
7.  内存分配和回收策略
    1. 对象优先在eden中分配
    2. 大对象直接进入 老年代
    3. 长期存活对象 进入 老年代
        1. age 计数器 默认15 可以通过设置 你暗恋过来进入老年代
        2. 动态对象
    4. gc的条件
        1. 系统调用
        2. 老年代空间不足
        3. 持久代空间不足
        4.  gc 担保失败
        5.  Cocurrent mode failure


