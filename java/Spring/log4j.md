# Log4j

- [Log4j](#log4j)
  - [slf4j+log4j2](#slf4jlog4j2)
  - [错误](#错误)
    - [java.lang.NoSuchMethodError: org.slf4j.impl.StaticLoggerBinder.getSingleton](#javalangnosuchmethoderror-orgslf4jimplstaticloggerbindergetsingleton)


## slf4j+log4j2

slf4j仅仅是一个为Java程序提供日志输出的统一接口，并不是一个具体的日志实现方案，就比如JDBC一样，只是一种规则而已，所以单独的slf4j是不能工作的，必须搭配其他具体的日志实现方案，比如log4j或者log4j2

需要添加依赖的jar包有：
1. slf4j的api接口包：slf4j-api
2. log4j2的核心包：log4j-core
3. log4j2的api接口包：log4j-api
4. slf4j对应log4j2日志框架的桥接驱动包：log4j-slf4j-impl
5. log4j2的异步日志功能包：com.lmax.disruptor
6. 解决web项目log4j可能出现警告的jar包：log4j-web

```xml
<!-- Log4j2支持 -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>${log4j-version}</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>${log4j-version}</version>
</dependency>
<!-- slf4j核心包-->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>${slf4j-version}</version>
</dependency>
<!--log4j2异步包-->
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.4.2</version>
</dependency>
<!-- 桥接：告诉Slf4j使用Log4j2 -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>${log4j-version}</version>
</dependency>
<!--log4j2 web工程需要包含log4j-web，非web工程不需要-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-web</artifactId>
    <version>${log4j-version}</version>
</dependency>
```

配置文件log4j2.xml放在src/main/resouce目录下,web.xml中配置如下：
```xml
<!-- 加载log4j配置文件 -->
<context-param>
    <param-name>log4jConfiguration</param-name>
    <param-value>classpath:log4j2.xml</param-value>
</context-param>
<!-- 如果使用spring5+,配置log4j2监听器,Log4j2的监听器要放在spring监听器前面-->
<listener>
    <listener-class>org.apache.logging.log4j.web.Log4jServletContextListener</listener-class>
</listener>
```
代码中使用
```java
// 通过slf4j接口创建Logger对象
private static final Logger LOGGER = LoggerFactory.getLogger(logTest.class);
```

##  错误

### java.lang.NoSuchMethodError: org.slf4j.impl.StaticLoggerBinder.getSingleton
导入依赖
```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-nop</artifactId>
    <version>1.7.25</version>
    <scope>test</scope>
</dependency>`
```