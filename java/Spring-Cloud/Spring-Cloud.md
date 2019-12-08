# Spring Cloud

- [Spring Cloud](#spring-cloud)
  - [Spring boot配置文件顺序](#spring-boot配置文件顺序)
  - [父子项目](#父子项目)
  - [Eureka 服务注册中心](#eureka-服务注册中心)
  - [Ribbon](#ribbon)
  - [Feign](#feign)
  - [zipkin服务链追踪](#zipkin服务链追踪)
  - [配置服务器](#配置服务器)
  - [配置客户端](#配置客户端)
  - [配置客户端手动更新](#配置客户端手动更新)
  - [消息总线bus](#消息总线bus)
  - [断路器 Hystrix](#断路器-hystrix)

## Spring boot配置文件顺序

后加载的会覆盖先加载的

1. application.yml
2. application.yaml
3. application.properties

## 父子项目

1.子项目pom.xml中添加parent信息与自己的信息
```xml
<parent>
  <artifactId>springcloud</artifactId>
  <groupId>com.heyefu.springcloud</groupId>
  <version>1.0-SNAPSHOT</version>
</parent>
<artifactId>view-service</artifactId>
<name>view-service</name>
```
2. 在父项目中添加子项目信息
```xml
<modules>
  <module>eureka-server</module>
  <module>data-service</module>
  <module>view-service</module>
</modules>
```

## Eureka 服务注册中心

1. 在子项目Eureka注册中心中添加依赖
```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
  </dependency>
</dependencies>
```
2. 在启动类上添加@EnableEurekaServer注解，表示这是个Enreka Server
3. 配置文件application.yaml
```yaml
eureka:
  instance:
    #   主机名称
    hostname: localhost
  client:
    #   是否注册到服务器,本身就是服务器，不需要注册
    registerWithEureka: false
    # 获取服务器的注册信息
    fetchRegistry: false
    serviceUrl:
      # 自己作为服务器，公布出来的地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
 
spring:
  application:
    name: eureka-server
```

## Ribbon

1. 引入相关依赖
```xml
<dependencies>
  <!-- eureka 客户端 -->
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
</dependencies>
```
2. 在启动类上添加@EnableEurekaClient激活Eureka 客户端
3. 在启动类上添加@EnableDiscoverClient发现Eureka 客户端
4. application.yaml
```yaml
spring:
  application:
    #   微服务名称
    name: product-data-service
eureka:
  client:
    serviceUrl:
      # 注册中心地址
      defaultZone: http://localhost:8761/eureka/
```
4. Ribbon客户端
```java
@Component
public class ProductClientRibbon {
 
    @Autowired
    RestTemplate restTemplate;
 
    public List<Product> listProdcuts() {
        // 此处地址是在eureka注册中心注册的名称+/接口名称
        return restTemplate.getForObject("http://PRODUCT-DATA-SERVICE/products",List.class);
    }
 
}
```

## Feign

Feign是对Ribbon的封装，使用注解的方式

1. 引入jar包
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```
2. 创建Feign客户端接口
```java
// 这里value是需要访问的微服务在eureka注册的中心的名称
@FeignClient(value = "PRODUCT-DATA-SERVICE")
public interface ProductClientFeign {
 
    //  在对应微服务中的接口
    @GetMapping("/products")
    public List<Product> listProdcuts();
}
```
3. 在启动类上添加@EnableFeignClients
4. 在application.yaml中添加eureka注册中心地址
```yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

## zipkin服务链追踪

[可以参考](https://www.jianshu.com/p/f177a5e2917f)

1. 在要追踪的微服务中添加项目依赖
2. 在微服务配置中添加zipkin的base-url
3. 在启动类中配置Sample抽样策略
4. 下载并运行zipkin的可执行jar包
5. 访问http://localhost:9411/zipkin/查看链路

## 配置服务器

1. 准备好git上的配置文件，为properties或yml文件,必须按照约定的命名{application}-{profile}.properties或{application}-{profile}.yml,之后可以通过{application}/{profile}访问配置文件(如果配置文件有多个-，则以最后一个-分隔)
3. 准备好git，引入jar
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!--配置服务器-->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```
3. 启动类添加@EnableConfigServer注解,表示该Spring boot这是个配置服务器
4. 修改application.yaml,添加配置服务器信息与注册中心信息
```yaml
spring:
  application:
    name: config-server
  cloud:
    config:
      #      label表示分支
      label: master
      server:
        git:
          #          uri表示git地址
          uri: https://github.com/heyefu19920626/MarkdownNotes/
          #          目录
          searchPaths: java/Spring-Cloud
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```
5. 访问配置文件localhost:config-port/{application}/{profile}


## 配置客户端

1. 添加依赖
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-config</artifactId>
</dependency> 
```
2. 新建bootstrap.yml文件，并配置相关属性,该文件会先于application.yml加载,删除application中的注册中心信息
```yml
spring:
  cloud:
    config:
      # label 表示 git上的分支
      label: master
      discovery:
        enabled:  true
        # 对应配置中心的 spring.application.name
        serviceId:  config-server
      #  name 表示配置文件的 {application} 部分
      name: spring
      # profile 表示配置文件的 {profile}部分
      profile: cloud
# bootstrap中有了注册中心就删除application中的
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```
3. 重启服务就可以在其中使用@Value("${属性名}")来给变量赋值了@RefreshScope

## 配置客户端手动更新

1. 添加依赖
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
2. 在application.yml中暴露更新接口
```yml
management:
  endpoints:
    web:
      exposure:
        include: refresh
```
3. 在希望能够进行配置更新的地方，添加注解 @RefreshScope
4. 修改git上的配置文件后，在本地使用$\color{red}{POST}$调用http://localhost:port/actuator/refresh来刷新配置文件

## 消息总线bus

基于上一步手动更新客户端

1. 引入依赖，注意此包版本，要与spring boot匹配，不然不能启动，经测试spring boot 2.0.3与spring cloud Finchley.RELEASE版本的才能正常启动
```xml
<!-- 支持rabbitmq -->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>  
```
2. 在bootstrap.yml中新增bus总线配置支持
```yml
spring:
  cloud:
    bus:
      enabled: true
      trace:
        enabled: true
```
3. 在application.yml中新增rabbit支持与接口路径访问支持
```yml
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
management:
  endpoints:
    web:
      exposure:
        # * 这个符号暴露所有接口
        include: "*"
      cors:
        allowed-origins: "*"
        allowed-methods: "*"  
```
4. 启动多个服务后采用$\color{red}{POST}$任意服务的/acturtor/bus-refresh访问即可刷新配置信息
5. 增加rabbit后zipkin不能追踪调用链，启动zipkin的jar包时增加rabbitmq参数即可``--zipkin.collector.rabbitmq.addresses=localhost``
```cmd
java -jar zipkin-server-2.10.1-exec.jar --zipkin.collector.rabbitmq.addresses=localhost
```

## 断路器 Hystrix

当被访问的微服务无法使用的时候，当前服务能够感知这个现象，并且提供一个备用的方案.

1. 引入依赖
```xml
<!--断路器Hystrix支持-->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```
2. 创建一个实现了Feign客户端接口的实现类,例如此处新建为FeignClientHystrix，并实现其中的函数
3. 修改Feign客户端接口的@FeignClient注解的参数,添加属性fallback为第2步创建的实现类的FeignClientHystrix.class
4. 在application.yml中开启断路器,对应类HystrixFeignConfiguration
```yml
feign:
  hystrix:
    enabled: true
```


