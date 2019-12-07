# Spring Cloud

- [Spring Cloud](#spring-cloud)
  - [父子项目](#父子项目)
  - [Eureka 服务注册中心](#eureka-服务注册中心)
  - [Ribbon](#ribbon)
  - [Feign](#feign)
  - [zipkin服务链追踪](#zipkin服务链追踪)
  - [配置服务](#配置服务)

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

## 配置服务

1. 准备好git，引入jar
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!--配置服务-->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```
2. 启动类添加@EnableConfigServer注解,表示该Spring boot这是个配置服务器
3. 修改application.yaml,添加配置服务器信息与注册中心信息
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
