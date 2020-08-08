# Spring boot

- [Spring boot](#spring-boot)
  - [默认首页](#默认首页)
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
```xml <build>
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