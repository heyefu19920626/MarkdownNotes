# Linux中各种环境变量设置

- [Linux中各种环境变量设置](#linux中各种环境变量设置)
  - [java环境变量](#java环境变量)
  - [Go环境变量](#go环境变量)
  - [Maven](#maven)
  - [Gradle](#gradle)

## java环境变量
> vim /etc/profile
> source /etc/profile
- jdk
```
#java environment
export JAVA_HOME=/usr/java/jdk1.8.0_144
export CLASSPATH=.:${JAVA_HOME}/jre/lib/rt.jar:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
export PATH=$PATH:${JAVA_HOME}/bin
```
- jre
```
export JAVA_HOME=/usr/java/jre.8.0_144
export CLASSPATH=$JAVA_HOME/lib/rt.jar:$JAVA_HOME/lib/ext
export PATH=$PATH:$JAVA_HOME/bin
```

## Go环境变量

```
 export GOROOT=/opt/basic-tools/go/go
 export GOPATH=/home/heyefu/codes/go
 export GOBIN=$GOPATH/bin
 export PATH=$PATH:$GOROOT/bin
 export PATH=$PATH:$GOPATH/bin
 ```
 
 ## Maven

```
export MAVEN_HOME=/opt/basic-tools/maven/maven-3.5.4
export PATH=${PATH}:${MAVEN_HOME}/bin
```

## Gradle

```
export GRADLE_HOME=/opt/basic-tools/gradle/gradle-5.4.1
export PATH=$PATH:${GRADLE_HOME}/bin
```