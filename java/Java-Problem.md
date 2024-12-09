# 问题

- [..](java-catalog.md)

- [问题](#问题)
  - [泛型中的extends与super](#泛型中的extends与super)
  - [反射](#反射)
    - [获取类的三种方式](#获取类的三种方式)
    - [调用方法](#调用方法)
  - [Thread中start()与run()](#thread中start与run)
  - [三种代理模式](#三种代理模式)
  - [Java对象不再使用时，赋值为null](#java对象不再使用时赋值为null)
  - [四大元注解](#四大元注解)
  - [File.separator](#fileseparator)
  - [finally不执行](#finally不执行)
  - [查看内存占用](#查看内存占用)
  - [org.aspectj.apache.bcel.classfile.ClassFormatException: Invalid byte tag in constant pool: 15](#orgaspectjapachebcelclassfileclassformatexception-invalid-byte-tag-in-constant-pool-15)
  - [java.lang.ClassNotFoundException: javax.xml.bind.JAXBException](#javalangclassnotfoundexception-javaxxmlbindjaxbexception)


## 泛型中的extends与super

PECS法则：Producer Extends, Consumer Super, 为你提供数据->生产者， 接收你的数据->消费者  
`<? extends T>` 只能读不能写, 因为读出来的都是T，但你确定写入的是T的哪个子类  
`<? super T>` 智能写不能读，因为可以确认写进去的都是T，但不确定读出来的是T的哪个父类  

## 反射

### 获取类的三种方式

Java9+中Class.newInstance()方法将被弃用,将被`Class.getDeclaredConstructor().newInstance()`方法取代  
该方法适用与public与private,且可以构造有参与无参,`Class.getConstructor()`适用于public  
```java
String.class.getConstructor(String.class).newInstance("Hello");
```

1. ``Class.forName(String classPath)``
   1. classPath是类的完整路径，可能产生ClassNotFoundException异常
   2. 一般用于不知道类是否存在的情况
2. ``Class clz = 类名.class``
   1. 一般用于知道有这个类的情况
3. ``Class clz = 对象.getClass``
   1. 前提是对象已经实例化了
   2. 可以用于动态调用方法


### 调用方法

1. 获取具体的方法``Method method = clz.getMethod(name, parameterTypes...)``
   1. name为方法名，parameterTypes为参数类型
   2. 调用``method.invoke(object, args...)``
   3. object为实例化的对象,args为该方法所需要的参数

## Thread中start()与run()

线程一般有五种状态：创建，就绪，运行，阻塞，死亡  
创建：生成对象后，start之前  
就绪：start后，线程调度正式运行前  
运行：执行run中的代码  
阻塞：正在运行时被暂停或等待某个时机，sleep，wait,suspend  
死亡：执行完run后，或调用stop后，无法再用start进入就绪  

1. start启动的是异步多线程
2. run只是个普通同步方法

## 三种代理模式

1. 静态代理  
   代理对象要实现与目标对象一样的接口
2. 动态代理,JDK代理,接口代理  
   使用java.lang.reflect.Proxy.newProxyInstance的方法  
   代理对象不需要实现接口，目标对象需要实现接口
```java
static Object newProxyInstance(ClassLoader loader, 
            Class[] interfaces,InvocationHandler h )
```
3. Cglib代理,子类代理

## Java对象不再使用时，赋值为null

如何确定对象可以被回收/如何确定对象是存活的？  
可达性分析算法  
JVM: 栈中引用的对象。只要堆中的对象，在栈中还存在引用，就是存活的。  
赋值操作会对栈中进行读写，会擦掉已失效的引用。
```java
if(true){
    byte[] placeHolder = new byte[64 * 1024 * 1024]
    System.out.println(placeHolder.length / 1024);
}
//或者换位其它的赋值操作,如int replace = 1;也能达到同样效果
placeHolder = null;
System.gc()
```

## 四大元注解

1. @Target ：用于描述注解的使用范围，也就是说使用了@Target去定义一个注解，那么可以决定定义好的注解能用在什么地方
2. @Retention：用于描述注解的生命周期，也就是说这个注解在什么范围内有效，注解的生命周期和三个阶段有关：源代码阶段、CLASS文件中有效、运行时有效，故其取值也就三个值，分别代表着三个阶段
3. @Documented：表示该注解是否可以生成到 API文档中。在该注解使用后，如果导出API文档，会将该注解相关的信息可以被例如javadoc此类的工具文档化。 注意：Documented是一个标记注解，没有成员。
4. @Inherited：使用@Inherited定义的注解具备继承性
假设一个注解在定义时，使用了@Inherited，然后该注解在一个类上使用，如果这个类有子类，那么通过反射我们可以从类的子类上获取到同样的注解

## File.separator

字符串替换时出现java.lang.IllegalArgumentException: character to be escaped is missing  
如果replace(old, new),如果new中单独出现File.separator就会出现上面的异常  
windows的File.separator为`\`,处理时会被当作转义字符，后面看这个字符后一位，如果没有任何内容，就报异常了  

1. 需要Matcher.quoteReplacement(File.separator)

## finally不执行

1. try中System.exit(1)
2. try中调用halt函数: `Runtime.getRuntime().halt(1)`
3. 守护线程,如果守护线程刚开始执行到 finally 代码块，此时没有任何其他非守护线程，那么虚拟机将退出，此时 JVM 不会等待守护线程的 finally 代码块执行完成
4. try中无限循环且没有异常

## 查看内存占用

1. 在命令行中输入jps,即可查看当前已启动的进程和对应的PID
2. 执行命令`jmap -dump:format=b,file=heap.bin <pid>`
   1. jmap 能查看jvm内存中，对象占用内存的情况，还提供非常方便的命令将jvm的内存信息导出的文件
   2. format=b是通过二进制的意思,-dump:format=b,file=heap.bin意思是：把内存结构全部dump到二进制文件heap.bin中
3. 执行命令：`jhat -J-Xmx512m heap.bin`,就可以将我们刚刚使用jmap导出的内存信息交给jhat解析了,默认的情况下，它会监听7000端口
   1. 可以在命令中调整命令使用的内存大小
   2. 访问`http://localhost:7000/histo/`

## org.aspectj.apache.bcel.classfile.ClassFormatException: Invalid byte tag in constant pool: 15

aspectjweaver包版本太低,升级版本

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.8.14</version>
</dependency>
```

## java.lang.ClassNotFoundException: javax.xml.bind.JAXBException

JAXB API是java EE 的API，因此在java SE 9.0 中不再包含这个 Jar 包。  
java 9 中引入了模块的概念，默认情况下，Java SE中将不再包含java EE 的Jar包  
而在 java 6/7 / 8 时关于这个API 都是捆绑在一起的

1. jdk降级
2. 或者maven引入依赖
```xml
<!-- Java 6 = JAX-B Version 2.0   -->
<!-- Java 7 = JAX-B Version 2.2.3 -->
<!-- Java 8 = JAX-B Version 2.2.8 -->
<dependencies>
    <dependency>
        <groupId>javax.xml.bind</groupId>
        <artifactId>jaxb-api</artifactId>
        <version>2.3.0</version>
    </dependency>
    <dependency>
        <groupId>com.sun.xml.bind</groupId>
        <artifactId>jaxb-impl</artifactId>
        <version>2.3.0</version>
    </dependency>
    <dependency>
        <groupId>com.sun.xml.bind</groupId>
        <artifactId>jaxb-core</artifactId>
        <version>2.3.0</version>
    </dependency>
    <dependency>
        <groupId>javax.activation</groupId>
        <artifactId>activation</artifactId>
        <version>1.1.1</version>
    </dependency>
</dependencies>
```