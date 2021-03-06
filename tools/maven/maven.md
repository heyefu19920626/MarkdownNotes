# Maven的一些配置

- [Maven的一些配置](#maven的一些配置)
  - [maven package传参](#maven-package传参)
  - [maven配置spring仓库与阿里云仓库](#maven配置spring仓库与阿里云仓库)
  - [maven设置docker化](#maven设置docker化)
  - [Spring-boot编译可执行jar包](#spring-boot编译可执行jar包)
  - [IDEAZ中maven编译可执行jar包](#ideaz中maven编译可执行jar包)
  - [maven配置本地jar包](#maven配置本地jar包)
  - [maven编译级别设置](#maven编译级别设置)
  - [问题](#问题)
    - [was cached in the local repository](#was-cached-in-the-local-repository)

## maven package传参

1. 在maven package后加-Dfilename=test
2. 在pom文件中使用`${filename}`使用


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

## maven设置docker化

1. 在pom文件中，设置docker服务器
```xml
<profiles>
    <!-- 实验室环境 -->
    <profile>
        <id>idmlab</id>
        <properties>
            <config.prefix/>
            <config.ip>192.168.11.106</config.ip>
            <config.port>8080</config.port>
            <config.project>fcm/</config.project>
            <config.dockerhost>192.168.11.106</config.dockerhost>
        </properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
</profiles>
```
2. 在build中的plugins内添加docker整合插件，并配置
```xml
<!--docker-maven整合插件-->
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>1.2.1</version>
    <configuration>
        <serverId>docker-hub</serverId>
        <imageName>
            ${config.ip}:${config.port}/${config.project}${config.prefix}${project.artifactId}:${project.version}
        </imageName>
        <dockerHost>http://${config.dockerhost}:2375</dockerHost>
        <!-- 本地docker文件的目录,dockerFile文件在此编写,下面resource配置的一些文件一会先放到这中转，后续再推送到服务器 -->
        <dockerDirectory>${project.basedir}/src/main/docker</dockerDirectory>
        <skipDockerBuild>false</skipDockerBuild>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
            <resource>
                <!-- 这里是将项目根目下的crypto-config文件夹拷贝到target目录下的docker文件夹内 -->
                <targetPath>/</targetPath>
                <directory>${project.basedir}</directory>
                <include>crypto-config/**</include>
            </resource>
            <resource>
                <!-- 这里是将项目根目下的以yaml结尾的文件拷贝到target目录下的docker文件夹内 -->
                <targetPath>/</targetPath>
                <directory>${project.basedir}</directory>
                <include>*.yaml</include>
            </resource>
        </resources>
    </configuration>
</plugin>
```
3. 在maven的setting.xml中添加docker服务器的用户名密码
```xml
<server>
    <id>docker-hub</id>
    <username>username</username>
    <password>password</password>
</server>
```
4. 在src/main/docker目录(没有新建)下，新建并编写Dockerfile文件
```dockerfile
FROM anapsix/alpine-java
ADD *.jar fcm-blockchain-client.jar
ADD connection-org1.yaml connection-org1.yaml
ADD crypto-config /crypto-config #将docker目录下的文件夹拷贝到镜像根目录下
ENTRYPOINT java -Xms512m -Xmx512m -jar fcm-blockchain-client.jar
```
5. 推送到镜像仓库
   1. 在pom.xml所在目录执行`mvn clean package -P idmlab docker:build -DpushImage`推送镜像到镜像仓库
   2. idmlab为pom中配置的profile名


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
        <executions>
            <execution>
                <goals>
                    <goal>repackage</goal>
                </goals>
                <configuration>
                    <!-- 主函数入口 -->
                    <mainClass>com.heyefu.Main</mainClass>
                </configuration>
            </execution>
        </executions>
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


<!-- 把所有依赖打入 -->
<build>
<plugins>
    <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <configuration>
            <appendAssemblyId>false</appendAssemblyId>
            <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
            </descriptorRefs>
            <archive>
                <manifest>
                    <!-- 此处指定main方法入口的class -->
                    <mainClass>com.huawei.cloud.callback.CloudTransferMain</mainClass>
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


<!-- 这种只打自己 -->
<build>
    <!-- 此处打包资源文件等 -->
    <resources>
        <resource>
            <!-- 将java目录下的xml等文件打包 -->
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

## maven编译级别设置

1. 插件
```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```
2. 属性
```xml
<properties>
    <java.version>1.8</java.version>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>

```

## 问题

### was cached in the local repository

删除本地仓库中的*.lastUpdated文件