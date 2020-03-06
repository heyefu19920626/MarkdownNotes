# Maven的一些配置

- [Maven的一些配置](#maven的一些配置)
  - [maven配置spring仓库与阿里云仓库](#maven配置spring仓库与阿里云仓库)
  - [Spring-boot编译可执行jar包](#spring-boot编译可执行jar包)
  - [IDEAZ中maven编译可执行jar包](#ideaz中maven编译可执行jar包)
  - [maven配置本地jar包](#maven配置本地jar包)


## maven配置spring仓库与阿里云仓库 

在pom中添加

```xml
<repository>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <layout>default</layout>
    <snapshots>
        <enabled>false</enabled>
    </snapshots>
</repository>

<repository>
    <id>spring-milestones</id>
    <name>Spring Milestones</name>
    <url>https://repo.spring.io/libs-milestone</url>
    <snapshots>
        <enabled>false</enabled>
    </snapshots>
</repository>
```

## Spring-boot编译可执行jar包

1. 在parent中引入spring boot
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.4.RELEASE</version>
    <relativePath/>
</parent>
```
2. 在build中配置插件
```xml
<plugins>
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>2.3.2</version>
        <configuration>
            <!-- 配置编译版本，如果 properties 和 build 里面都有配置的话，那么 properties 会覆盖掉 build 里面的配置，即以 properties 里面的配置为准 -->
            <source>1.8</source>
            <target>1.8</target>
            <encoding>UTF-8</encoding>
        </configuration>
    </plugin>
</plugins>
```


## IDEAZ中maven编译可执行jar包

将所有依赖和资源文件一起打包至一个jar包中,需要先在pom.xml中设置属性,再在编译器右侧的工具条中点击maven，继续展开本项目下的->Lifecycle，双击package即可以打包成可执行jar包

```xml

<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!-- 此处配合build中的configuration中的source和target属性，将java的编译和语法版本等设为1.8 -->
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>


<build>
    <!-- 此处打包资源文件等 -->
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*</include>
            </includes>
            <excludes>
                <exclude>**/.svn/*</exclude>
            </excludes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*</include>
            </includes>
            <excludes>
                <exclude>**/.svn/*</exclude>
            </excludes>
            <filtering>false</filtering>
        </resource>
    </resources>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>2.3.2</version>
            <configuration>
                <!-- 配置编译版本，如果 properties 和 build 里面都有配置的话，那么 properties 会覆盖掉 build 里面的配置，即以 properties 里面的配置为准 -->
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
                <compilerArguments>
                    <verbose/>
                    <bootclasspath>${java.home}\lib\rt.jar;${java.home}\lib\jce.jar</bootclasspath>
                </compilerArguments>
            </configuration>
        </plugin>

        <plugin>
            <artifactId>maven-assembly-plugin</artifactId>
            <configuration>
                <appendAssemblyId>false</appendAssemblyId>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
                <archive>
                    <manifest>
                        <!-- 主函数 -->
                        <mainClass>com.inspur.fabric.SDKTestMain</mainClass>
                    </manifest>
                </archive>
            </configuration>
            <executions>
                <execution>
                    <id>make-assembly</id>
                    <phase>package</phase>
                    <goals>
                        <goal>assembly</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

## maven配置本地jar包

```xml
<dependency>
    <groupId>com.baidu</groupId>
    <artifactId>ueditor</artifactId>
    <version>1.1.2</version>
    <!-- 本地jar -->
    <scope>system</scope>
    <systemPath>${basedir}/src/main/webapp/WEB-INF/lib/ueditor-1.1.2.jar</systemPath>
</dependency>
```
