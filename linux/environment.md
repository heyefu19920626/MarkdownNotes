
### java环境变量
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