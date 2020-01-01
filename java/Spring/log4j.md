# Log4j


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