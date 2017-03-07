#  jd面试
1. hashmap linkedhashmap hashset区别
2. java内存模型
   主内存 
3. java中锁的概念 以及应用
4. mysql优化 以及搜索引擎
5. 阐述spring 启动顺序 以及请求处理
6. 如何引入mybatis变量
7. 垃圾回收 几种常见算法 (root 查找算法)
8. jvm调优工具
9. java类加载顺序
10. java常见的类加载器 四种(basic extend application 自定义)
11. redis的常见存储类型
11. 单例模式 的几种实现 以及优劣
     1. 设置一个变量 返回即可
     2. 把getInstance设置为sync
     3. 把instance变量设置为volatile
     4. static final field
     5.  static nested class
     6. 枚举类
     7. 一般来说，单例模式有五种写法：懒汉、饿汉、双重检验锁、静态内部类、枚举。上述所说都是线程安全的实现，文章开头给出的第一种方法不算正确的写法。
12. 设计一个高并发的秒杀系统
13. 集群 一个服务部署在多个server 分布式  服务的拆分
14. aop ：这种在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程。 java过滤器 拦截器 把应用业务逻辑 和 系统服务区分开
15. ioc  Spring IOC 负责创建对象，管理对象（通过依赖注入（DI），装配对象，配置对象，并且管理这些对象的整个生命周期。这概念是说你不用创建对象，而只需要描述它如何被创建。你不在代码里直接组装你的组件和服务，但是要在配置文件里描述哪些组件需要哪些服务，之后一个容器（IOC容器）负责把他们组装起来。
16. spring mvc Spring 配备构建Web 应用的全功能MVC框架。Spring可以很便捷地和其他MVC框架集成，如Struts，Spring 的MVC框架用控制反转把业务对象和控制逻辑清晰地隔离。它也允许以声明的方式把请求参数和业务对象绑定 Spring的MVC框架是围绕DispatcherServlet来设计的，它用来处理所有的HTTP请求和响应。WebApplicationContext 继承了ApplicationContext  并增加了一些WEB应用必备的特有功能，它不同于一般的ApplicationContext ，因为它能处理主题，并找到被关联的servlet。控制器提供一个访问应用程序的行为，此行为通常通过服务接口实现。控制器解析用户输入并将其转换为一个由视图呈现给用户的模型。Spring用一个非常抽象的方式实现了一个控制层，允许用户创建多种用途的控制器
17. redis 数据类型  string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。


     

