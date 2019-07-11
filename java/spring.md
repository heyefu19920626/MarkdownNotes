### [目录](./catalog.md)
- [Spring事务](#spring-transactional)
- [web.xml的加载顺序](#web-xml)
- [跨域设置](#cross-domain)
- [Spring的启动关键](#spring-start)
- [mybatis打印sql语句](#mybatis-show-sql)



<div id="spring-transactional"></div>

#### Spring事务
- Spring事务
    + 编码式事务，基于编码
    + 声明式事务，基于AOP
        * xml配置
        * @Transactional
- [事务隔离级别：isolation()](https://www.jianshu.com/p/5687e2a38fbc)
    + 0, 导致脏读
    + 1, 避免脏读，允许不可重复读和幻读
    + 2, 避免脏读，不可重复读，允许幻读
    + 4, 串行化读，事务只能一个个执行，执行效率慢
```
1.脏读：
脏读就是指当一个事务正在访问数据，并且对数据进行了修改，
而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。

2.不可重复读：
是指在一个事务内，多次读同一数据。
在这个事务还没有结束时，另外一个事务也访问该同一数据。
那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。
这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。（即不能读到相同的数据内容）。
例如，一个编辑人员两次读取同一文档，但在两次读取之间，作者重写了该文档。
当编辑人员第二次读取文档时，文档已更改。原始读取不可重复。
如果只有在作者全部完成编写后编辑人员才可以读取文档，则可以避免该问题。

3.幻读:
是指当事务不是独立执行时发生的一种现象，例如第一个事务对一个表中的数据进行了修改，
这种修改涉及到表中的全部数据行。
同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。
那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象
发生了幻觉一样
```
- [@Transactional 不起作用](https://blog.csdn.net/qq_20597727/article/details/84900994)
    + 方法必须是public的
    + 不能同一个类内部调用
    + 事务方法内部捕捉了异常
    + [异常类型必须是unchecked的](https://blog.csdn.net/dyl_yingmeng/article/details/81110816)(继承即RuntimeException的)或者Error
    + 数据库引擎要支持
    + [使用@Transactional修饰的方法，直接被调用，事务才会起作用，如果被间接调用，事务注解不会起作用的](https://blog.csdn.net/chenze666/article/details/80105155)
    + 多个数据源时是否正确配置了数据源
    + [事务AOP的先后顺序](https://blog.csdn.net/glory1234work2115/article/details/51817893)


<div id="web-xml"></div>

#### [web.xml的加载顺序](https://blog.csdn.net/ahou2468/article/details/79015251)  

> context-param -> listener -> filter -> servlet

- 读取web.xml配置
- 创建一个ServletContext(application),web项目所有部分都能共享这个上下文

```
启动一个web应用的时候，即在其ServletContext组件创建的时候，
首先解析web.xml获取该应用配置的listeners列表和servlet列表，
然后保存在自身的一个属性中，然后通过分发生命周期事件ServletContextEvent给这些listeners，
从而在listeners感知到应用在启动了，然后自定义自身的处理逻辑，
如spring的ContextLoaderListener就是解析spring的配置文件并创建相关的bean，
这样其实也是实现了一种代码的解耦；
其次是创建配置的servlet列表，调用servlet的init方法，这样servlet可以自定义初始化逻辑，
DispatcherServlet就是其中一个servlet
```

<div id="cross-domain"></div>

#### [跨域设置](https://www.cnblogs.com/asfeixue/p/4363372.html)

- spring4.0+
```xml
<mvc:cors>
        <mvc:mapping path="/**" allowed-origins="*" allow-credentials="true" max-age="1800" allowed-methods="GET,POST,OPTIONS"/>
</mvc:cors>
```

<div id="spring-start"></div>

#### [Spring的启动关键](https://blog.csdn.net/u010013573/article/details/86547687)

- ContextLoaderListener | load-on-startup Servlet -> ContextLoadServlet
- DispatcherServlet


<div id="mybatis-show-sql"></div>

#### mybatis打印Sql语句

- 在mybatis-config.xml中配置加一个setting
```xml
<configuration>
    <settings>
        <!-- 打印查询语句 -->
        <setting name="logImpl" value="STDOUT_LOGGING" />
    </settings>
</configuration>
```
