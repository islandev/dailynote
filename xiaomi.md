# 面试题
1. topk 分布式
2. serverlet 来了100个请求 有多少个实例  1个
3. jsp forward方法 参数传递
	是在服务器端起作用,当使用forward()时,Servlet engine传递HTTP请求从当前的Servlet or JSP到另外一个Servlet,JSP 或普通HTML文件,也即你的form提交至a.jsp,在a.jsp用到了forward()重定向至b.jsp,此时form提交的所有信息在 b.jsp都可以获得,参数自动传递. 但forward()无法重定向至有frame的jsp文件,可以重定向至有frame的html文件,同时forward()无法在后面带参数传递,比如servlet?name=frank,这样不行,可以程序内通过response.setAttribute("name",name)来传至下一个页面。
	
4. 数据库同步 同时 保证数据库可用
5. sleep  和wait区别
6. 父类和子类 静态变量 和非静态变量的初始化顺序 
	首先初始化 静态变量，父类 子类， 而后初始化 非静态变量 父类 子类
7. 方法 调用方法自身 可以调用 stackoverflow
8. 在类中 对方法加锁 可以执行
9. 单例的实现方法
10. aop  代理模式 实现
11. volitile
12. 数据库 事物 数据库 隔离性
	原子性 一致性 持久性 隔离性
13. tcp状态 closed—wait tcp状态转移图
14. mysql的隔离级别
	