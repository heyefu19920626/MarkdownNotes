# Spring boot

- [Spring boot](#spring-boot)
  - [默认首页](#默认首页)
  - [传参](#传参)
    - [命令行参数](#命令行参数)
    - [系统属性参数](#系统属性参数)
    - [jvm参数](#jvm参数)
    - [环境变量](#环境变量)
    - [spring boot参数](#spring-boot参数)
    - [getEnv与getProperty](#getenv与getproperty)
  - [打印sql日志](#打印sql日志)
  - [配置https](#配置https)
    - [https证书获取](#https证书获取)
    - [在项目中引入](#在项目中引入)
  - [RESTFUL文档](#restful文档)
  - [多module打包](#多module打包)
    - [maven](#maven)
  - [问题](#问题)
    - [application.yml不起作用](#applicationyml不起作用)
    - [controller没有自动扫描](#controller没有自动扫描)

## 默认首页

src/resource/static/index.html

## 传参

### 命令行参数

跟在jar后，以空格分隔，在main方法中读取  
> java -jar demo.jar param1 param2 param3

### 系统属性参数

系统属性参数也是供应用程序使用的，并且是以key=value这样的形式提供的，在程序的任何一个地方，都可以通过System.getProperty("key")获取到对应的value值  
用法`java -Dfoo="some string" SomeClass`,系统属性参数传入的时候需要带一个横杆和大写字母D,示例中可以使用Sytem.getProperty("foo")获取
> java -jar xxx.jar -Da1=aaa -Db1=bbb -Dc1=ccc

### jvm参数

jvm参数就是和jvm相关的参数了，比如配置gc、配置堆大小、配置classpath等等  
jvm参数分为标准参数、扩展参数和不稳定参数  
标准参数是一定有效，向后兼容的，且所有的jvm都必须要实现的，比如-classpath，这类参数是横杆直接跟参数名  
扩展参数是不保证向后向后兼容，不强值要求所有jvm实现都要支持，不保证后续版本不会取消的，这类参数的形式是-Xname，横杠和一个大写的X开头  
不稳定参数就是非常不稳定，可能只是特定版本的，特点是-XXname，横杆后带两个大写X开头  
java中的命令行参数只有option和args两类
```bash
java [-options] class [args...]
           (to execute a class)
或者
java [-options] -jar jarfile [args...]
           (to execute a jar file)
```

### 环境变量

启动前export a1=a1,启动后可以使用System.getEnv("a1)获取

### spring boot参数

可以通过@Value("${a1})获取
> java -jar xxx.jar --a1=aaa --b1=bbb

### getEnv与getProperty

Java提供了System类的以下静态方法用于返回系统相关的变量与属性
1. System.getenv() 方法是获取指定的环境变量的值，大多与系统相关
2. System.getenv(String str) 接收参数为任意字符串，当存在指定环境变量时即返回环境变量的值，否则返回null
3. System.getProperty() 是获取系统的相关属性，大多与java程序有关，包括文件编码、操作系统名称、区域、用户名等，此属性一般由jvm自动获取，不能设置
4. System.getProperty(String str) 接收参数为任意字符串，当存在指定属性时即返回属性的值，否则返回null

## 打印sql日志

1. mybatis: 在application.yml中配置需要打印的包路径
```yml
logging:
  level:
    com.heyefu.dao: debug
```

## 配置https

### https证书获取
1. 去云服务厂商那申请
2. 借用jdk工具
   1. 进入到`%JAVA_HOME%/bin`目录下，执行命令`keytool -genkey -alias tomcathttps -keyalg RSA -keysize 2048 -keystore D:\keystore -validity 365`,生成一个数字证书
      1. genkey 表示要创建一个新的密钥
      2. alias 表示 keystore 的别名
      3. keyalg 表示使用的加密算法是 RSA ，一种非对称加密算法
      4. keysize 表示密钥的长度
      5. keystore 表示生成的密钥存放位置
      6. validity 表示密钥的有效时间，单位为天
### 在项目中引入

1. 将上面申请到的证书放入resources目录下
2. 在spring boot配置文件中引入
```yml
server:
  ssl:
    #   证书名称
    key-store: classpath:keystore
    # 秘钥别名
    key-alias: tomcathttps
    # 在cmd执行过程中输入的密码
    key-store-password: 123456
```

## RESTFUL文档

1. 引入swagger2
```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
</dependency>
```
2. 创建swagger配置类
```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2).pathMapping("/").select()
            .apis(RequestHandlerSelectors.basePackage("com.heyefu")).paths(PathSelectors.any())
            .build().apiInfo(getApiInfo());
    }

    private ApiInfo getApiInfo() {
        return new ApiInfoBuilder().title("heyefu").version("1.0").description("api").build();
    }
}
```
3. 访问页面http://ip:port/swagger-ui.html

## 多module打包

### maven

[参考](https://blog.csdn.net/luo15242208310/article/details/100141368)

1. 最外层父pom
```xml 
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <!-- 是否过滤资源文件，替换maven属性 - 不过滤，否则过滤xlsx文件导致乱码，XSSFWork读取格式异常 -->
            <filtering>false</filtering>
            <includes>
                <include>**/*</include>
                <include>mapper/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```
2. SpringBoot启动类子pom
```xml
<build>
   <plugins>
      <plugin>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-maven-plugin</artifactId>
         <configuration>
            <mainClass>com.xxx.MxVehiclePartsApplication（此处替换为相应Springboot启动类）</mainClass>
         </configuration>
         <executions>
            <execution>
               <goals>
                  <goal>repackage</goal>
               </goals>
            </execution>
         </executions>
      </plugin>
   </plugins>
</build>
```
3. 其他子模块POM(非SpringBoot启动类),无需指定build















## 问题

### application.yml不起作用

1. pom.xml中`<package>pom</package>`改为`<package>jar</package>`或者直接注释

### controller没有自动扫描

1. 启动类要放在第一层，只会扫描启动类之下的包