# Spring

- [..](spring-catalog.md)

- [Spring](#spring)
  - [SpringMVC的运行流程](#springmvc的运行流程)
  - [SpringMVC九大组件](#springmvc九大组件)
  - [Spring事务](#spring事务)
  - [web.xml的加载顺序](#webxml的加载顺序)
  - [跨域设置](#跨域设置)
  - [Spring的启动关键](#spring的启动关键)
  - [mybatis打印Sql语句](#mybatis打印sql语句)
  - [Spring注入静态变量](#spring注入静态变量)
  - [Spring boot 发起POST请求](#spring-boot-发起post请求)
  - [Spring Boot 集成kafka](#spring-boot-集成kafka)

## SpringMVC的运行流程

1. 用户发送请求至前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping处理器映射器
3. 处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet
4. DispatcherServlet通过HandlerAdapter处理器适配器调用处理器
5. 执行处理器(Controller，也叫后端控制器)
6. Controller执行完成返回ModelAndView
7. HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover解析后返回具体View
10. DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）
11. DispatcherServlet响应用户。从上面可以看出，DispatcherServlet有接收请求，响应结果，转发等作用。有了DispatcherServlet之后，可以减少组件之间的耦合度

![](pic/spring-mvc.webp)

## SpringMVC九大组件

```java
protected void initStrategies(ApplicationContext context) {  
//用于处理上传请求。处理方法是将普通的request包装成 MultipartHttpServletRequest，后者可以直接调用getFile方法获取File.
 initMultipartResolver(context);  
//SpringMVC主要有两个地方用到了Locale：一是ViewResolver视图解析的时候；二是用到国际化资源或者主题的时候。
 initLocaleResolver(context); 
//用于解析主题。SpringMVC中一个主题对应 一个properties文件，里面存放着跟当前主题相关的所有资源、
//如图片、css样式等。SpringMVC的主题也支持国际化， 
 initThemeResolver(context);  
//用来查找Handler的。
 initHandlerMappings(context);  
//从名字上看，它就是一个适配器。Servlet需要的处理方法的结构却是固定的，都是以request和response为参数的方法。
//如何让固定的Servlet处理方法调用灵活的Handler来进行处理呢？这就是HandlerAdapter要做的事情
 initHandlerAdapters(context);  
//其它组件都是用来干活的。在干活的过程中难免会出现问题，出问题后怎么办呢？//这就需要有一个专门的角色对异常情况进行处理，在SpringMVC中就是HandlerExceptionResolver。
 initHandlerExceptionResolvers(context);  
//有的Handler处理完后并没有设置View也没有设置ViewName，这时就需要从request获取ViewName了，
//如何从request中获取ViewName就是RequestToViewNameTranslator要做的事情了。
 initRequestToViewNameTranslator(context);
//ViewResolver用来将String类型的视图名和Locale解析为View类型的视图。
//View是用来渲染页面的，也就是将程序返回的参数填入模板里，生成html（也可能是其它类型）文件。
 initViewResolvers(context);  
//用来管理FlashMap的，FlashMap主要用在redirect重定向中传递参数。
 initFlashMapManager(context); 
}
```


## Spring事务
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


## [web.xml的加载顺序](https://blog.csdn.net/ahou2468/article/details/79015251)  

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

## [跨域设置](https://www.cnblogs.com/asfeixue/p/4363372.html)

- spring4.0+
```xml
<mvc:cors>
    <mvc:mapping path="/**" allowed-origins="*" allow-credentials="true" max-age="1800" allowed-methods="GET,POST,OPTIONS"/>
</mvc:cors>
```

## [Spring的启动关键](https://blog.csdn.net/u010013573/article/details/86547687)

- ContextLoaderListener | load-on-startup Servlet -> ContextLoadServlet
- DispatcherServlet

## mybatis打印Sql语句

- 在mybatis-config.xml中配置加一个setting
```xml
<configuration>
    <settings>
        <!-- 打印查询语句 -->
        <setting name="logImpl" value="STDOUT_LOGGING" />
    </settings>
</configuration>
```

## Spring注入静态变量

静态变量、类变量不是对象的属性，而是一个类的属性，所以静态方法是属于类（class）的，普通方法才是属于实体对象（也就是New出来的对象）的，spring注入是在容器中实例化对象，所以不能使用静态方法

注入方法：
1. 类上使用@Componet
2. 静态变量使用set方法注入(set方法不能为静态的)

```java
@Component
public final class DocImageUtils {
    private static ImageFileDao imageFileDao;
 
    @Autowired(required = true)
    public  void setImageFileDao(ImageFileDao imageFileDao) {
        DocImageUtils.imageFileDao = imageFileDao;
    }
}
```

## Spring boot 发起POST请求

```java
        String url = "https://heyefu.wsl.com/"+path;
        //使用Restemplate来发送HTTP请求
        RestTemplate restTemplate = new RestTemplate();
        // json对象
        JSONObject jsonObject = new JSONObject();
        // LinkedMultiValueMap 有点像JSON，用于传递post数据，网络上其他教程都使用 
        // MultiValueMpat<>来传递post数据
        // 但传递的数据类型有限，不能像这个这么灵活，可以传递多种不同数据类型的参数
        LinkedMultiValueMap<String, String> body = new LinkedMultiValueMap<>();
        body.add("price",price);
        body.add("type",type);
        body.add("uid",uid);
        //设置请求header 为 APPLICATION_FORM_URLENCODED
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
        // 请求体，包括请求数据 body 和 请求头 headers
        HttpEntity<MultiValueMap<String, String>> httpEntity = new HttpEntity<>(body, headers);
        try {
            //使用 exchange 发送请求，以String的类型接收返回的数据
            //ps，我请求的数据，其返回是一个json
            ResponseEntity<String> strbody = restTemplate.exchange(url,HttpMethod.POST,httpEntity,String.class);
			//解析返回的数据
            JSONObject jsTemp = JSONObject.parseObject(strbody.getBody());
            System.out.println(jsonObject.toJSONString());
            return jsTemp;

        }catch (Exception e){
            System.out.println(e);
        }
```

## Spring Boot 集成kafka

1. kafka的启动与监听
   1. kafka配置经常需要修改hosts
```sh
# 启动zooker
./zkCli.sh -server 127.0.0.1:2181
# 启动kafka
sudo  /bin/kafka-server-start.sh /config/server.properties
# kafka提供的模拟收发消息的模板，可以简单的测试kafka的功能，测试后可以关闭
## 发送消息
sudo /usr/frankxulei/Kafka/kafka_2.12-2.1.0/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic Java
## 监听消息
sudo /usr/frankxulei/Kafka/kafka_2.12-2.1.0/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic Java --from-beginning
```


```yaml
spring:
  kafka:
    # 指定kafka代理地址，brokers集群。多个服务器以英文逗号分隔
    bootstrap-servers: ssh.qianxunclub.com:9092
    producer:
      # 发送失败重试次数。
      retries: 0
      # 每次批量发送消息的数量 批处理条数：当多个记录被发送到同一个分区时，生产者会尝试将记录合并到更少的请求中。这有助于客户端和服务器的性能。
      batch-size: 16384
      # 32MB的批处理缓冲区。
      buffer-memory: 33554432
      # 指定消息key和消息体的编解码方式。
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
    consumer:
      # 消费者群组ID，发布-订阅模式，即如果一个生产者，多个消费者都要消费，那么需要定义自己的群组，同一群组内的消费者只有一个能消费到消息。
      group-id: kafka_order_group
      auto-offset-reset: earliest
      # 如果为true，消费者的偏移量将在后台定期提交。
      enable-auto-commit: true
      # 自动提交周期
      auto-commit-interval: 100
      # 指定消息key和消息体的编解码方式。
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
```
